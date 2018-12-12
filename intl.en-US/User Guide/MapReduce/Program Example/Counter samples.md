# Counter samples {#concept_ipg_qbh_vdb .concept}

## Prerequisites {#section_e3n_syg_vdb .section}

1.  Prepare the Jar package of the test program. Assume the package is named mapreduce-examples.jar, and the local storage path is data\\resources.
2.  Prepare the UserDefinedCounters test table and resource.
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

    The data imported into the wc\_in the table wc\_in, is as follows:

    ```
    hello,odps
    ```


## Procedure {#section_rlv_bzg_vdb .section}

Execute UserDefinedCounters on the odpscmd:

```
jar -resources mapreduce-examples.jar -classpath data\resources\mapreduce-examples.jar
com.aliyun.odps.mapred.open.example.UserDefinedCounters wc_in wc_out
```

## Expected output {#section_hzz_dzg_vdb .section}

The output of Counters is as follows:

```
Counters: 3
com.aliyun.odps.mapred.open.example.UserDefinedCounters$MyCounter
MAP_TASKS=1
REDUCE_TASKS=1
TOTAL_TASKS=2
```

The content of output table “wc\_out”  is as follows:

```
+------------+------------+
| key | cnt |
+------------+------------+
| hello | 1 |
| odps | 1 |
+------------+------------+
```

## Sample code {#section_jgb_gzg_vdb .section}

```
    package com.aliyun.odps.mapred.open.example;
    import java.io.IOException;
    import java.util.Iterator;
    import com.aliyun.odps.counter.Counter;
    import com.aliyun.odps.counter.Counters;
    import com.aliyun.odps.data.Record;
    import com.aliyun.odps.mapred.JobClient;
    import com.aliyun.odps.mapred.MapperBase;
    import com.aliyun.odps.mapred.ReducerBase;
    import com.aliyun.odps.mapred.RunningJob;
    import com.aliyun.odps.mapred.conf.JobConf;
    import com.aliyun.odps.mapred.utils.SchemaUtils;
    import com.aliyun.odps.mapred.utils.InputUtils;
    import com.aliyun.odps.mapred.utils.OutputUtils;
    import com.aliyun.odps.data.TableInfo;
    /**
     * 
     * User Defined Counters
     *
     **/
    public class UserDefinedCounters {
      enum MyCounter {
        TOTAL_TASKS, MAP_TASKS, REDUCE_TASKS
      }
      public static class TokenizerMapper extends MapperBase {
        private Record word;
        private Record one;
        @Override
        public void setup(TaskContext context) throws IOException{
          super.setup(context);
          Counter map_tasks = context.getCounter(MyCounter.MAP_TASKS);
          Counter total_tasks = context.getCounter(MyCounter.TOTAL_TASKS);
          map_tasks.increment(1);
          total_tasks.increment(1);
          word = context.createMapOutputKeyRecord();
          one = context.createMapOutputValueRecord();
          one.set(new Object[] { 1L });
        }
        @Override
        public void map(long recordNum, Record record, TaskContext context)
            Throws ioexception {
          for (int i = 0; i < record.getColumnCount(); i++) {
            word.set(new Object[] { record.get(i).toString() });
            context.write(word, one);
          }
        }
      }
      public static class SumReducer extends ReducerBase {
        private Record result = null;
        @Override
        public void setup(TaskContext context) throws IOException{
          result = context.createOutputRecord();
          Counter reduce_tasks = context.getCounter(MyCounter.REDUCE_TASKS);
          Counter maid = context. getcounter (mycounter );
          reduce_tasks.increment(1);
          total_tasks.increment(1);
        }
        @Override
        public void reduce(Record key,Iterator<Record>values,TaskContext context)
            Throws ioexception {
          Long Count = 0;
          while(values.hasNext()) {
            Record val = values.next();
            count += (Long) val.get(0);
          }
          result.set(0, key.get(0));
          result.set(1, count);
          context.write(result);
        }
      }
      public static void main(String[] args) throws Exception {
        if (args.length ! = 2) {
          System.err
              .println("Usage: TestUserDefinedCounters <in_table> <out_table>");
          System.exit(2);
        }
        JobConf job = new JobConf();
        job.setMapperClass(TokenizerMapper.class);
        job.setReducerClass(SumReducer.class);
        job.setMapOutputKeySchema(SchemaUtils.fromString("word:string"));
        job.setMapOutputValueSchema(SchemaUtils.fromString("count:bigint"));
        InputUtils.addTable(TableInfo.builder().tableName(args[0]).build(), job);
        OutputUtils.addTable(TableInfo.builder().tableName(args[1]).build(), job);
        RunningJob rJob = JobClient.runJob(job);
        // After the job has completed successfully, you can get the value of the custom counter inside the job
        Counters counters = rJob.getCounters();
        long m = counters.findCounter(MyCounter.MAP_TASKS).getValue();
        long r = counters.findCounter(MyCounter.REDUCE_TASKS).getValue();
        long total = counters.findCounter(MyCounter.TOTAL_TASKS).getValue();
        System.exit(0);
      }
    }

```

