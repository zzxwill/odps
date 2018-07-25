# Multi-input and Output {#concept_p5h_f3g_vdb .concept}

## Preparation {#section_e3n_syg_vdb .section}

1.  Prepare the Jar package of the test program. Assume the package is named mapreduce-examples.jar, and the local storage path isThe local storage path is data\\resources.
2.  Prepare tables and resources for testing the multi-table input and output operations.
    -   Create tables:

        ```
        create table wc_in1(key string, value string);
        create table wc_in2(key string, value string);
        create table mr_multiinout_out1 (key string, cnt bigint);
        create table mr_multiinout_out2 (key string, cnt bigint) partitioned by (a string, b string);
        alter table mr_multiinout_out2 add partition (a='1', b='1');
        alter table mr_multiinout_out2 add partition (a='2', b='2');
        ```

    -   Add resources:

        ```
        add jar data\resources\mapreduce-examples.jar -f;
        ```

3.  Run tunnel to import data.

    ```
    tunnel upload data1 wc_in1;
    tunnel upload data2 wc_in2;
    ```

    The data  imported into the wc\_in1 table is as follows:

    ```
     hello,odps
    ```

    The data  imported into the wc\_in2 table is as follows:

    ```
     hello,world
    ```


## Procedure {#section_rlv_bzg_vdb .section}

Run MultipleInOut  in odpscmd.

```
jar -resources mapreduce-examples.jar -classpath data\resources\mapreduce-examples.jar
com.aliyun.odps.mapred.open.example.MultipleInOut wc_in1,wc_in2 mr_multiinout_out1,mr_multiinout_out2|a=1/b=1|out1,mr_multiinout_out2|a=2/b=2|out2;
```

## Expected Results {#section_hzz_dzg_vdb .section}

The content of ‘mr\_multiinout\_out1’   is as follows:

```
+------------+------------+
| key | cnt |
+------------+------------+
| default | 1 |
+------------+------------+
```

The content of ‘mr\_multiinout\_out2’  is as follows:

```
+--------+------------+---+---+
| key | cnt | a | b |
+--------+------------+---+---+
| odps | 1 | 1 | 1 |
| world | 1 | 1 | 1 |
| out1 | 1 | 1 | 1 |
| hello | 2 | 2 | 2 |
| out2 | 1 | 2 | 2 |
+--------+------------+---+---+
```

## Sample code {#section_jgb_gzg_vdb .section}

```
    package com.aliyun.odps.mapred.open.example;
    import java.io.IOException;
    Import java.util.iterator;
    import java.util.LinkedHashMap;
    import com.aliyun.odps.data.Record;
    import com.aliyun.odps.data.TableInfo;
    import com.aliyun.odps.mapred.JobClient;
    import com.aliyun.odps.mapred.MapperBase;
    import com.aliyun.odps.mapred.ReducerBase;
    import com.aliyun.odps.mapred.TaskContext;
    import com.aliyun.odps.mapred.conf.JobConf;
    import com.aliyun.odps.mapred.utils.InputUtils;
    import com.aliyun.odps.mapred.utils.OutputUtils;
    import com.aliyun.odps.mapred.utils.SchemaUtils;
    /**
     * Multi input & output example.
     **/
    public class MultipleInOut {
      public static class TokenizerMapper extends MapperBase {
        Record word;
        Record one;
        @Override
        public void setup(TaskContext context) throws IOException{
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
        private Record result;
        private Record result1;
        private Record result2;
        @Override
        public void setup(TaskContext context) throws IOException{
          // For different outputs you need to create different records, which are distinguished by label
          result = context.createOutputRecord();
          result1 = context.createOutputRecord("out1");
          result2 = context.createOutputRecord("out2");
        }
        @Override
        public void reduce(Record key,Iterator<Record>values,TaskContext context)
            Throws ioexception {
          Long Count = 0;
          while(values.hasNext()) {
            Record val = values.next();
            count += (Long) val.get(0);
          }
          long mod = count % 3;
          if (mod == 0) {
            result.set(0, key.get(0));
            result.set(1, count);
            //No label is specified. Default output is adopted.
            context.write(result);
          } else if (mod == 1) {
            result1.set(0, key.get(0));
            result1.set(1, count);
            context.write(result1, "out1");
          } else {
            result2.set(0, key.get(0));
            result2.set(1, count);
            context.write(result2, "out2");
          }
        }
        @Override
        public void cleanup(TaskContext context) throws IOException {
          Record result = context.createOutputRecord();
          result.set(0, "default");
          result.set(1, 1L);
          context.write(result);
          Record result1 = context.createOutputRecord("out1");
          result1.set(0, "out1");
          result1.set(1, 1L);
          context.write(result1, "out1");
          Record result2 = context.createOutputRecord("out2");
          result2.set(0, "out2");
          result2.set(1, 1L);
          context.write(result2, "out2");
        }
      }
      // Convert the partition string such as "ds = 1/pt = 2" into map form
      public static LinkedHashMap<String, String> convertPartSpecToMap(
          String partSpec) {
        LinkedHashMap<String, String> map = new LinkedHashMap<String, String>();
        if (partSpec ! = null && ! partSpec.trim().isEmpty()) {
          String[] parts = partSpec.split("/");
          for (String part : parts) {
            String[] ss = part.split("=");
            if (ss.length ! = 2) {
              throw new RuntimeException("ODPS-0730001: error part spec format: "
                  + partSpec);
            }
            map.put(ss[0], ss[1]);
          }
        }
        return map;
      }
      public static void main(String[] args) throws Exception {
        String[] inputs = null;
        String[] outputs = null;
        if (args.length == 2) {
          inputs = args[0].split(",");
          outputs = args[1].split(",");
        } else {
          System.err.println("MultipleInOut in... out...") ;
          System.exit(1);
        }
        JobConf job = new JobConf();
        job.setMapperClass(TokenizerMapper.class);
        job.setReducerClass(SumReducer.class);
        job.setMapOutputKeySchema(SchemaUtils.fromString("word:string"));
        job.setMapOutputValueSchema(SchemaUtils.fromString("count:bigint"));
         //Parse the user input table strings.
        for (String in : inputs) {
          String[] ss = in.split("\\|");
          if (ss.length == 1) {
            InputUtils.addTable(TableInfo.builder().tableName(ss[0]).build(), job);
          } else if (ss.length == 2) {
            LinkedHashMap<String, String> map = convertPartSpecToMap(ss[1]);
            InputUtils.addTable(TableInfo.builder().tableName(ss[0]).partSpec(map).build(), job);
          } else {
            System.err.println("Style of input: " + in + " is not right");
            System.exit(1);
          }
        }
        //Parse the user output table strings.
        for (String out : outputs) {
          String[] ss = out.split("\\|");
          if (ss.length == 1) {
            OutputUtils.addTable(TableInfo.builder().tableName(ss[0]).build(), job);
          } else if (ss.length == 2) {
            LinkedHashMap<String, String> map = convertPartSpecToMap(ss[1]);
            OutputUtils.addTable(TableInfo.builder().tableName(ss[0]).partSpec(map).build(), job);
          } else if (ss.length == 3) {
            if (ss[1].isEmpty()) {
              LinkedHashMap<String, String> map = convertPartSpecToMap(ss[2]);
              OutputUtils.addTable(TableInfo.builder().tableName(ss[0]).partSpec(map).build(), job);
            } else {
              LinkedHashMap<String, String> map = convertPartSpecToMap(ss[1]);
              OutputUtils.addTable(TableInfo.builder().tableName(ss[0]).partSpec(map)
                  .label(ss[2]).build(), job);
            }
          } else {
            System.err.println("Style of output: " + out + " is not right");
            System.exit(1);
          }
        }
        Jobclient. runjob (job );
      }
    }

```

