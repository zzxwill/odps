# Join Sample {#concept_awd_xdh_vdb .concept}

MaxCompute MapReduce framework does not support JOIN, however, you can implement data join in your Map/Reduce function.

Assume that table mr\_join\_src1\(key bigint, value string\) must be joined with mr\_join\_src2\(key bigint, value string\). The output table is mr\_join\_out \(key bigint, value1 string, value2 string\). value1 is value in mr\_join\_src1 and value2 is value in mr\_join\_src2.

## Preparation {#section_e3n_syg_vdb .section}

1.  Prepare the JAR package of the test program. Assume the package is named mapreduce-examples.jar, and the local storage path is data\\resources.
2.  Prepare tables and resources for testing the JOIN operation.
    -   Create tables.

        ```
        create table mr_Join_src1(key bigint, value string);
        create table mr_Join_src2(key bigint, value string);
        create table mr_Join_out(key bigint, value1 string, value2 string);
        ```

    -   Add resources.

        ```
        add jar data\resources\mapreduce-examples.jar -f;
        ```

3.  Run tunnel to  import data.

    ```
    tunnel upload data1 mr_Join_src1;
    tunnel upload data2 mr_Join_src2;
    ```

    The data imported into  the mr\_join\_src1 table is as follows:

    ```
     1,hello
     2,odps
    ```

    The data imported into the mr\_join\_src2 table is as follows:

    ```
    1,odps
    3,hello
    4,odps
    ```


## Procedure {#section_rlv_bzg_vdb .section}

Run Join in odpscmd.

```
jar -resources mapreduce-examples.jar -classpath data\resources\mapreduce-examples.jar
com.aliyun.odps.mapred.open.example.Join mr_Join_src1 mr_Join_src2 mr_Join_out;
```

## Result {#section_hzz_dzg_vdb .section}

The output table mr\_join\_out contains the following content:

```

| key | value1 | value2 |

| 1 | hello | odps | 

```

## Sample code {#section_jgb_gzg_vdb .section}

```
    package com.aliyun.odps.mapred.open.example;
    import java.io.IOException;
    Import java. util. arraylist;
    import java.util.Iterator;
    import java.util.List;
    import org.apache.commons.logging.Log;
    import org.apache.commons.logging.LogFactory;
    import com.aliyun.odps.data.Record;
    import com.aliyun.odps.data.TableInfo;
    import com.aliyun.odps.mapred.JobClient;
    import com.aliyun.odps.mapred.MapperBase;
    import com.aliyun.odps.mapred.ReducerBase;
    import com.aliyun.odps.mapred.conf.JobConf;
    import com.aliyun.odps.mapred.utils.InputUtils;
    import com.aliyun.odps.mapred.utils.OutputUtils;
    import com.aliyun.odps.mapred.utils.SchemaUtils;
    
     * Join, mr_Join_src1/mr_Join_src2(key bigint, value string), mr_Join_out(key
     * bigint, value1 string, value2 string)
      
     
    public class Join {
      public static final Log LOG = LogFactory.getLog(Join.class);
      public static class JoinMapper extends MapperBase {
        private Record mapkey;
        private Record mapvalue;
        private long tag;
        @Override
        public void setup(TaskContext context) throws IOException {
          mapkey = context.createMapOutputKeyRecord();
          mapvalue = context.createMapOutputValueRecord();
          tag = context.getInputTableInfo().getLabel().equals("left") ? 0 : 1;
        
        @Override
        public void map(long key, Record record, TaskContext context)
            throws IOException {
          mapkey.set(0, record.get(0));
          mapkey.set(1, tag);
          for (int i = 1; i < record.getColumnCount(); i++) {
            mapvalue.set(i - 1, record.get(i));
          
          context.write(mapkey, mapvalue);
        
      
      public static class JoinReducer extends ReducerBase {
        private Record result = null;
        @Override
        public void setup(TaskContext context) throws IOException {
          result = context.createOutputRecord();
        
        @Override
        public void reduce(Record key, Iterator<Record> values, TaskContext context)
            throws IOException {
          long k = key.getBigint(0);
          List<Object[]> leftValues = new ArrayList<Object[]>();
          while (values.hasNext()) {
            Record value = values.next();
            long tag = (Long) key.get(1);
            if (tag == 0) {
              leftValues.add(value.toArray().clone());
            } else {
              for (Object[] leftValue : leftValues) {
                int index = 0;
                result.set(index++, k);
                for (int i = 0; i < leftValue.length; i++) {
                  result.set(index++, leftValue[i]);
                
                for (int i = 0; i < value.getColumnCount(); i++) {
                  result.set(index++, value.get(i));
                
                context.write(result);
              
            
          
        
      
      public static void main(String[] args) throws Exception {
        if (args.length ! = 3) {
          System.err.println("Usage: Join <input table1> <input table2> <out>");
          System.exit(2);
        
        JobConf job = new JobConf();
        job.setMapperClass(JoinMapper.class);
        job.setReducerClass(JoinReducer.class);
        job.setMapOutputKeySchema(SchemaUtils.fromString("key:bigint,tag:bigint"));
        job.setMapOutputValueSchema(SchemaUtils.fromString("value:string"));
        job.setPartitionColumns(new String[]{"key"});
        job.setOutputKeySortColumns(new String[]{"key", "tag"});
        job.setOutputGroupingColumns(new String[]{"key"});
        job.setNumReduceTasks(1);
        InputUtils.addTable(TableInfo.builder().tableName(args[0]).label("left").build(), job);
        InputUtils.addTable(TableInfo.builder().tableName(args[1]).label("right").build(), job);
        OutputUtils.addTable(TableInfo.builder().tableName(args[2]).build(), job);
        JobClient.runJob(job);
      
    

```

