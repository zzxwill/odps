# Pipeline Sample {#concept_kss_3zf_vdb .concept}

## Preparation {#section_e3n_syg_vdb .section}

1.  Prepare the JAR package of the test program. Assume the package is named mapreduce-examples.jar, and the local storage path isdata\\resources.
2.  Prepare tables and resources for testing the the WordCountPipeline operation.
    -   Create tables:

        ```
        create table wc_in (key string, value string);
        create table wc_out(key string, cnt bigint);
        ```

    -   Add resources:

        ```
        add jar data\resources\mapreduce-examples.jar -f;
        ```

3.  Use the tunnel command to import the data:

    ```
    tunnel upload data wc_in;
    ```

    The contents of data file imported into the table wc\_in, as follows:

    ```
    hello,odps
    ```


## Procedure {#section_rlv_bzg_vdb .section}

Run WordCountPipeline on the odpscmd, as follows:

```
jar -resources mapreduce-examples.jar -classpath data\resources\mapreduce-examples.jar
com.aliyun.odps.mapred.open.example.WordCountPipeline wc_in wc_out;
```

## Result {#section_hzz_dzg_vdb .section}

The content of output table wc\_out  is as follows:

```

| key | cnt |

| hello | 1 |
| odps | 1 |

```

## Sample code {#section_jgb_gzg_vdb .section}

```
    package com.aliyun.odps.mapred.open.example;
    import java.io.IOException;
    Import java. util. iterator;
    import com.aliyun.odps.Column;
    import com.aliyun.odps.OdpsException;
    import com.aliyun.odps.OdpsType;
    import com.aliyun.odps.data.Record;
    import com.aliyun.odps.data.TableInfo;
    import com.aliyun.odps.mapred.Job;
    import com.aliyun.odps.mapred.MapperBase;
    import com.aliyun.odps.mapred.ReducerBase;
    import com.aliyun.odps.pipeline.Pipeline;
    public class WordCountPipelineTest {
      public static class TokenizerMapper extends MapperBase {
        Record word;
        Record one;
        @Override
        public void setup(TaskContext context) throws IOException {
          word = context.createMapOutputKeyRecord();
          one = context.createMapOutputValueRecord();
          one.setBigint(0, 1L);
        
        @Override
        public void map(long recordNum, Record record, TaskContext context)
            throws IOException {
          for (int i = 0; i < record.getColumnCount(); i++) {
            String[] words = record.get(i).toString().split("\\s+");
            for (String w : words) {
              word.setString(0, w);
              context.write(word, one);
            
          
        
      
      public static class SumReducer extends ReducerBase {
        private Record value;
        @Override
        public void setup(TaskContext context) throws IOException {
          value = context.createOutputValueRecord();
        
        @Override
        public void reduce(Record key, Iterator<Record> values, TaskContext context)
            Throws ioexception {
          Long Count = 0;
          while (values.hasNext()) {
            Record val = values.next();
            count += (Long) val.get(0);
          
          value.set(0, count);
          context.write(key, value);
        
      
      public static class IdentityReducer extends ReducerBase {
        private Record result;
        @Override
        public void setup(TaskContext context) throws IOException {
          result = context.createOutputRecord();
        
        @Override
        public void reduce(Record key, Iterator<Record> values, TaskContext context)
            throws IOException {
          while (values.hasNext()) {
            result.set(0, key.get(0));
            result.set(1, values.next().get(0));
            context.write(result);
          
        
      
      public static void main(String[] args) throws OdpsException {
        if (args.length ! = 2) {
          System.err.println("Usage: WordCountPipeline <in_table> <out_table>");
          System.exit(2);
        
        Job job = new Job();
        
         * In the process of constructing pipeline, if you do not specify mapper's OutputKeySortColumns，PartitionColumns，OutputGroupingColumns,
         * the framework defaults to its OutputKey as the default configuration for the three
         
        Pipeline pipeline = Pipeline.builder()
            . Addmapper (maid. Class)
            .setOutputKeySchema(
                    new Column[] { new Column("word", OdpsType.STRING) })
            .setOutputValueSchema(
                    new Column[] { new Column("count", OdpsType.BIGINT) })
            .setOutputKeySortColumns(new String[] { "word" })
            .setPartitionColumns(new String[] { "word" })
            .setOutputGroupingColumns(new String[] { "word" })
            .addReducer(SumReducer.class)
            .setOutputKeySchema(
                    new Column[] { new Column("word", OdpsType.STRING) })
            .setOutputValueSchema(
                    new Column[] { new Column("count", OdpsType.BIGINT)})
            .addReducer(IdentityReducer.class).createPipeline();
        job.setPipeline(pipeline);
        job.addInput(TableInfo.builder().tableName(args[0]).build());
        job.addOutput(TableInfo.builder().tableName(args[1]).build());
        job.submit();
        job.waitForCompletion();
        System.exit(job.isSuccessful() == true ? 0 : 1);
      
    

```

