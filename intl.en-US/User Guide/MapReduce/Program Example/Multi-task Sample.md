# Multi-task Sample {#concept_zmv_21h_vdb .concept}

## Preparation {#section_e3n_syg_vdb .section}

1.  Prepare the Jar package of the test program. Assume the package is named mapreduce-examples.jar, and the local storage path isdata\\resources.
2.  Prepare tables and resources for testing the MultiJobs operation.
    -   Create tables:

        ```
        create table mr_empty (key string, value string);
        create table mr_multijobs_out (value bigint);
        ```

    -   Add resources:

        ```
        add table mr_multijobs_out as multijobs_res_table -f;
        Add jar data \ resources \ mapreduce-examples.jar-f;
        ```


## Procedure {#section_rlv_bzg_vdb .section}

Run MultiJobs  in odpscmd.

```
jar -resources mapreduce-examples.jar,multijobs_res_table -classpath data\resources\mapreduce-examples.jar 
 com.aliyun.odps.mapred.open.example.MultiJobs mr_multijobs_out;
```

## Expected Results {#section_hzz_dzg_vdb .section}

The output table ‘mr\_multijobs\_out’   is as follows:

```
+------------+
| value |
+------------+
| 0 |
+------------+
```

## Sample code {#section_jgb_gzg_vdb .section}

```
    package com.aliyun.odps.mapred.open.example;
    import java.io.IOException;
    import java.util.Iterator;
    import com.aliyun.odps.data.Record;
    import com.aliyun.odps.data.TableInfo;
    import com.aliyun.odps.mapred.JobClient;
    import com.aliyun.odps.mapred.MapperBase;
    import com.aliyun.odps.mapred.RunningJob;
    import com.aliyun.odps.mapred.TaskContext;
    import com.aliyun.odps.mapred.conf.JobConf;
    import com.aliyun.odps.mapred.utils.InputUtils;
    import com.aliyun.odps.mapred.utils.OutputUtils;
    import com.aliyun.odps.mapred.utils.SchemaUtils;
    /**
     * MultiJobs
     *
     * Running multiple job
     *
     **/
    public class MultiJobs {
      public static class InitMapper extends MapperBase {
        @Override
        public void setup(TaskContext context) throws IOException{
          Record record = context.createOutputRecord();
          long v = context.getJobConf().getLong("multijobs.value", 2);
          record.set(0, v);
          context.write(record);
        }
      }
      public static class DecreaseMapper extends MapperBase {
        @Override
        public void cleanup(TaskContext context) throws IOException {
           //Obtain the variable values defined by the main function from JobConf.
          long expect = context.getJobConf().getLong("multijobs.expect.value", -1);
          long v = -1;
          int count = 0;
          // Read the data in the resource table, which is the output table of the previous job
          Iterator<Record> iter = context.readResourceTable("multijobs_res_table");
          while (iter.hasNext()) {
            Record r = iter.next();
            v = (Long) r.get(0);
            if (expect ! = v) {
              throw new IOException("expect: " + expect + ", but: " + v);
            }
            count++;
          }
          if (count ! = 1) {
            throw new IOException("res_table should have 1 record, but: " + count);
          }
          Record record = context.createOutputRecord();
          v--;
          record.set(0, v);
          context.write(record);
          // Sets counter, which can be obtained in the main function after the job has completed successfully
          context.getCounter("multijobs", "value").setValue(v);
        }
      }
      public static void main(String[] args) throws Exception {
        if (args.length ! = 1) {
          System.err.println("Usage: TestMultiJobs <table>");
          System.exit(1);
        }
        String tbl = args[0];
        long iterCount = 2;
        System.err.println("Start to run init job.") ;
        JobConf initJob = new JobConf();
        initJob.setLong("multijobs.value", iterCount);
        initJob.setMapperClass(InitMapper.class);
        InputUtils.addTable(TableInfo.builder().tableName("mr_empty").build(), initJob);
        OutputUtils.addTable(TableInfo.builder().tableName(tbl).build(), initJob);
        initJob.setMapOutputKeySchema(SchemaUtils.fromString("key:string"));
        initJob.setMapOutputValueSchema(SchemaUtils.fromString("value:string"));
        // Maponly job needs to explicitly set reducer number to 0
        initJob.setNumReduceTasks(0);
        JobClient.runJob(initJob);
        while (true) {
          System.err.println("Start to run iter job, count: " + iterCount);
          JobConf decJob = new JobConf();
          decJob.setLong("multijobs.expect.value", iterCount);
          decJob.setMapperClass(DecreaseMapper.class);
          InputUtils.addTable(TableInfo.builder().tableName("mr_empty").build(), decJob);
          OutputUtils.addTable(TableInfo.builder().tableName(tbl).build(), decJob);
          // Maponly job needs to explicitly set reducer number to 0
          decJob.setNumReduceTasks(0);
          RunningJob rJob = JobClient.runJob(decJob);
          iterCount--;
          // Exit the loop if the number of iterations has been reached
          if (rJob.getCounters().findCounter("multijobs", "value").getValue() == 0) {
            break;
          }
        }
        if (iterCount ! = 0) {
          throw new IOException("Job failed.") ;
        }
      }
    }


```

