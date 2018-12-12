# Sleep samples {#concept_zjq_s2h_vdb .concept}

## Prerequisites {#section_e3n_syg_vdb .section}

1.  Prepare the Jar package of the test program. Assume the package is named mapreduce-examples.jar, and the local storage path is data\\resources.
2.  Prepare resources for testing the SleepJob operation.

    ```
    Add jar data \ resources \ mapreduce-examples.jar-f;
    ```


## Procedure {#section_rlv_bzg_vdb .section}

Run Sleep on the odpscmd is as follows:

```
  jar -resources mapreduce-examples.jar -classpath data\resources\mapreduce-examples.jar 
  com.aliyun.odps.mapred.open.example.Sleep 10;
  jar -resources mapreduce-examples.jar -classpath data\resources\mapreduce-examples.jar 
  com.aliyun.odps.mapred.open.example.Sleep 100;
```

## Expected output {#section_hzz_dzg_vdb .section}

The job runs successfully. The run time of different sleep durations can be compared to determine the effect.

## Sample code {#section_jgb_gzg_vdb .section}

```
package com.aliyun.odps.mapred.open.example;
import java.io.IOException;
import com.aliyun.odps.mapred.JobClient;
Import com.aliyun.odps.mapred.mapperbase;
import com.aliyun.odps.mapred.conf.JobConf;
public class Sleep {
  private static final String SLEEP_SECS = "sleep.secs";
  public static class MapperClass extends MapperBase {
    // Because the data is not entered, the map function is not executed, and the related logic can only be written in setup
    @Override
    public void setup(TaskContext context) throws IOException {
      try {
        // Get the number of sleep seconds set in jobconf to sleep
        Thread.sleep(context.getJobConf().getInt(SLEEP_SECS, 1) * 1000);
      } catch (InterruptedException e) {
        throw new RuntimeException(e);
      }
    }
  }
  public static void main(String[] args) throws Exception {
    if (args.length ! = 1) {
      System.err.println("Usage: Sleep <sleep_secs>");
      System.exit(-1);
    }
    JobConf job = new JobConf();
    job.setMapperClass(MapperClass.class);
    // This instance is also a maponly, so you need to set the reductor number to 0.
    job.setNumReduceTasks(0);
    // Because there is no input table, the number of mapper needs to be specified explicitly by the user
    job.setNumMapTasks(1);
    job.set(SLEEP_SECS, args[0]);
    JobClient.runJob(job);
  }
}

```

