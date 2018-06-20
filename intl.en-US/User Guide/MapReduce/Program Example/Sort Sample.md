# Sort Sample {#concept_ryq_qfl_vdb .concept}

## Preparation {#section_e3n_syg_vdb .section}

1.  Prepare the JAR package of the test program. Assume the package is named mapreduce-examples.jar, and the local storage path is data\\resources.
2.  Prepare tables and resources for testing the SORT operation.
    -   Create tables.

        ```
        create table ss_in(key bigint, value bigint);
        create table ss_out(key bigint, value bigint);
        ```

    -   Add resources.

        ```
        add jar data\resources\mapreduce-examples.jar -f;
        ```

3.  Use the tunnel command to import the data.

    ```
    tunnel upload data ss_in;
    ```

    The contents of data file imported into the table ss\_in:

    ```
     2,1
     1,1
     3,1
    ```


## Procedure {#section_rlv_bzg_vdb .section}

Run Sort on the odpscmd, as follows:

```
jar -resources mapreduce-examples.jar -classpath data\resources\mapreduce-examples.jar
com.aliyun.odps.mapred.open.example.Sort ss_in ss_out;
```

## Result {#section_hzz_dzg_vdb .section}

The content of output table ss\_out is as follows:

```

| key | value |

| 1 | 1 |
| 2 | 1 |
| 3 | 1 |

```

## Sample code {#section_jgb_gzg_vdb .section}

```
    package com.aliyun.odps.mapred.open.example;
    import java.io.IOException;
    import java.util.Date;
    import com.aliyun.odps.data.Record;
    import com.aliyun.odps.data.TableInfo;
    import com.aliyun.odps.mapred.JobClient;
    import com.aliyun.odps.mapred.MapperBase;
    import com.aliyun.odps.mapred.TaskContext;
    import com.aliyun.odps.mapred.conf.JobConf;
    import com.aliyun.odps.mapred.example.lib.IdentityReducer;
    import com.aliyun.odps.mapred.utils.InputUtils;
    import com.aliyun.odps.mapred.utils.OutputUtils;
    import com.aliyun.odps.mapred.utils.SchemaUtils;
    
     * This is the trivial map/reduce program that does absolutely nothing other
     * than use the framework to fragment and sort the input values.
     
     
    public class Sort {
      static int printUsage() {
        System.out.println("sort <input> <output>");
        return -1;
      
      
       * Implements the identity function, mapping record's first two columns to
       * outputs.
       
      public static class IdentityMapper extends MapperBase {
        private Record key;
        private Record value;
        @Override
        public void setup(TaskContext context) throws IOException {
          key = context.createMapOutputKeyRecord();
          value = context.createMapOutputValueRecord();
        
        @Override
        public void map(long recordNum, Record record, TaskContext context)
            throws IOException {
          Key. set (new object [] {(long) record. get (0 )});
          value.set(new Object[] { (Long) record.get(1) });
          context.write(key, value);
        
      
      
       * The main driver for sort program. Invoke this method to submit the
       * map/reduce job.
       
       * @throws IOException
       * When there is communication problems with the job tracker.
       
      public static void main(String[] args) throws Exception {
        JobConf jobConf = new JobConf();
        jobConf.setMapperClass(IdentityMapper.class);
        jobConf.setReducerClass(IdentityReducer.class);
        jobConf.setNumReduceTasks(1);
        Jobconf. setmapoutputkeyschema schemautils schemeiutils. fromstring ("key: bigint "));
        jobConf.setMapOutputValueSchema(SchemaUtils.fromString("value:bigint"));
        Inpututils. addtable (tableinfo. builder (). tablename (ARGs [0]). build (), jobconf );
        OutputUtils.addTable(TableInfo.builder().tableName(args[1]).build(), jobConf);
        Date starttime = new date ();
        System.out.println("Job started: " + startTime);
        JobClient.runJob(jobConf);
        Date end_time = new Date();
        System.out.println("Job ended: " + end_time);
        System.out.println("The job took "
            + (end_time.getTime() - startTime.getTime()) / 1000 + " seconds.")
      
    

```

