# MapOnly Sample {#concept_wz1_4yg_vdb .concept}

For MapOnly jobs, Map directly sends < Key, Value \> pairs to tables on MaxCompute. You only need to specify the output table but do not need to specify the  Key/Value metadata to be output by Map.

## Testing Preparation {#section_e3n_syg_vdb .section}

1.  Prepare the JAR package of the test program. Assume the package is named mapreduce-examples.jar,the local storage path is data\\resources.
2.  Prepare tables and resources for testing the MapOnly operation.
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

    The contents of data file imported into table is into the table “mr\_src”:

    ```
     hello,odps
     hello,odps
    ```


## Procedure {#section_rlv_bzg_vdb .section}

Run MapOnly in odpscmd.

```
jar -resources mapreduce-examples.jar -classpath data\resources\mapreduce-examples.jar
com.aliyun.odps.mapred.open.example.MapOnly wc_in wc_out map
```

## Expected Results {#section_hzz_dzg_vdb .section}

The content of output table wc\_out  is as follows:

```
+------------+------------+
| key | cnt |
+------------+------------+
| hello | 1 |
| hello | 1 |
+------------+------------+
```

## Sample code {#section_jgb_gzg_vdb .section}

```
    package com.aliyun.odps.mapred.open.example;
    import java.io.IOException;
    import com.aliyun.odps.data.Record;
    import com.aliyun.odps.mapred.JobClient;
    Import com. aliyun. ODPS. mapred. mapperbase;
    import com.aliyun.odps.mapred.conf.JobConf;
    import com.aliyun.odps.mapred.utils.SchemaUtils;
    import com.aliyun.odps.mapred.utils.InputUtils;
    import com.aliyun.odps.mapred.utils.OutputUtils;
    import com.aliyun.odps.data.TableInfo;
    public class MapOnly {
      public static class MapperClass extends MapperBase {
        @Override
        public void setup(TaskContext context) throws IOException{
          boolean is = context.getJobConf().getBoolean("option.mapper.setup", false);
          // The Main function sets option.mapper.setup to true in jobconf to execute the following logic.
          if (is) {
            Record result = context.createOutputRecord();
            result.set(0, "setup");
            result.set(1, 1L);
            context.write(result);
          }
        }
        @Override
        public void map(long key, Record record, TaskContext context) throws IOException {
          boolean is = context.getJobConf().getBoolean("option.mapper.map", false);
          //The Main function sets option.mapper.map to true in jobconf to execute the following logic.
          if (is) {
            Record result = context.createOutputRecord();
            result.set(0, record.get(0));
            result.set(1, 1L);
            context.write(result);
          }
        }
        @Override
        public void cleanup(TaskContext context) throws IOException {
          boolean is = context.getJobConf().getBoolean("option.mapper.cleanup", false);
           //The Main function sets option.mapper.cleanup to true in jobconf to execute the following logic.
          if (is) {
            Record result = context.createOutputRecord();
            result.set(0, "cleanup");
            result.set(1, 1L);
            context.write(result);
          }
        }
      }
      public static void main(String[] args) throws Exception {
        if (args.length ! = 2 && args.length ! = 3) {
          System.err.println("Usage: OnlyMapper <in_table> <out_table> [setup|map|cleanup]");
          System.exit(2);
        }
        JobConf job = new JobConf();
        job.setMapperClass(MapperClass.class);
        // For maponly jobs, the number of reducers must be explicitly set to 0
        job.setNumReduceTasks(0);
        //Set table information for Input Output
        InputUtils.addTable(TableInfo.builder().tableName(args[0]).build(), job);
        OutputUtils.addTable(TableInfo.builder().tableName(args[1]).build(), job);
        if (args.length == 3) {
          String options = new String(args[2]);
        //Jobconf can set custom key, value, and getJobConf can get relevant settings in mapper through getJobConf of context.
          if (options.contains("setup")) {
            job.setBoolean("option.mapper.setup", true);
          }
          if (options.contains("map")) {
            job.setBoolean("option.mapper.map", true);
          }
          if (options.contains("cleanup")) {
            job.setBoolean("option.mapper.cleanup", true);
          }
        }
        Jobclient. runjob (job );
      }
    }

```

