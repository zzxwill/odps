# Secondary Sort samples {#concept_yvc_5hg_vdb .concept}

## Prerequisites {#section_e3n_syg_vdb .section}

1.  Prepare a JAR package of the test program. Assume the package is named “mapreduce-examples.jar”.The local storage path is data\\resources.
2.  Prepare tables and resources for testing the SecondarySort operation.
    -   Create tables:

        ```
        create table ss_in(key bigint, value bigint);
        create table ss_out(key bigint, value bigint)
        ```

    -   Add resources:

        ```
        add jar data\resources\mapreduce-examples.jar -f;
        ```

3.  Import the data through tunnel command:

    ```
    tunnel upload data ss_in;
    ```

    The contents of data file  imported into the table “ss\_in” are as follows:

    ```
    1,2
    2,1
    1,1
    2,2
    ```


## Procedure {#section_rlv_bzg_vdb .section}

Run SecondarySort on the odpscmd:

```
jar -resources mapreduce-examples.jar -classpath data\resources\mapreduce-examples.jar 
com.aliyun.odps.mapred.open.example.SecondarySort ss_in ss_out;
```

## Expected output {#section_hzz_dzg_vdb .section}

The contents in the output table “ss\_out”  are as follows:

```

| key | value |

| 1 | 1 |
| 1 | 2 |
| 2 | 1 |
| 2 | 2 |

```

## Sample code {#section_jgb_gzg_vdb .section}

```
    package com.aliyun.odps.mapred.open.example;
    import java.io.IOException;
    import java.util.Iterator;
    import com.aliyun.odps.data.Record;
    import com.aliyun.odps.mapred.JobClient;
    import com.aliyun.odps.mapred.MapperBase;
    import com.aliyun.odps.mapred.ReducerBase;
    import com.aliyun.odps.mapred.TaskContext;
    import com.aliyun.odps.mapred.conf.JobConf;
    import com.aliyun.odps.mapred.utils.SchemaUtils;
    import com.aliyun.odps.mapred.utils.InputUtils;
    import com.aliyun.odps.mapred.utils.OutputUtils;
    import com.aliyun.odps.data.TableInfo;
    
      
     * This is an example ODPS Map/Reduce application. It reads the input table that
     * must contain two integers per record. The output is sorted by the first and
     * second number and grouped on the first number.
     
     
    public class SecondarySort {
      
       * Read two integers from each line and generate a key, value pair as ((left,
       * right), right).
       
      public static class MapClass extends MapperBase {
        private Record key;
        private Record value;
        @Override
        public void setup(TaskContext context) throws IOException {
          key = context.createMapOutputKeyRecord();
          value = context.createMapOutputValueRecord();
        
        @Override
        public void map(long recordNum, Record record, TaskContext context)
            throws IOException {
          long left = 0;
          long right = 0;
          if (record.getColumnCount() > 0) {
            left = (Long) record.get(0);
            if (record.getColumnCount() > 1) {
              right = (Long) record.get(1);
            
            key.set(new Object[] { (Long) left, (Long) right });
            value.set(new Object[] { (Long) right });
            context.write(key, value);
          
        
      
      
       * A reducer class that just emits the sum of the input values.
       
      public static class ReduceClass extends ReducerBase {
        private Record result = null;
        @Override
        public void setup(TaskContext context) throws IOException {
          result = context.createOutputRecord();
        
        @Override
        public void reduce(Record key, Iterator<Record> values, TaskContext context)
            throws IOException {
          result.set(0, key.get(0));
          while (values.hasNext()) {
            Record value = values.next();
            result.set(1, value.get(0));
            context.write(result);
          
        
      
      public static void main(String[] args) throws Exception {
        if (args.length ! = 2) {
        System.err.println("Usage: secondarysrot <in> <out>");
          System.exit(2);
        
        JobConf job = new JobConf();
        job.setMapperClass(MapClass.class);
        job.setReducerClass(ReduceClass.class);
        // set multiple columns to key
        // compare first and second parts of the pair
        job.setOutputKeySortColumns(new String[] { "i1", "i2" });
        // partition based on the first part of the pair
        job.setPartitionColumns(new String[] { "i1" });
        // grouping comparator based on the first part of the pair
        job.setOutputGroupingColumns(new String[] { "i1" });
        // the map output is LongPair, Long
        job.setMapOutputKeySchema(SchemaUtils.fromString("i1:bigint,i2:bigint"));
        Job. Fig (schemeiutils. fromstring ("i2x: bigint "));
        InputUtils.addTable(TableInfo.builder().tableName(args[0]).build(), job);
        OutputUtils.addTable(TableInfo.builder().tableName(args[1]).build(), job);
        JobClient.runJob(job);
        System.exit(0);
      
    


```

