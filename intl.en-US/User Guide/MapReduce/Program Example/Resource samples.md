# Resource samples {#concept_fw4_ygg_vdb .concept}

## Prerequisites {#section_e3n_syg_vdb .section}

1.  Prepare a Jar package of the test program. Assume the package is named mapreduce-examples.jar, and the local storage path isdata\\resources.
2.  Prepare the test table and the resource.
    -   Create the tables:

        ```
        create table mr_upload_src(key bigint, value string);
        ```

    -   Add the resource:

        ```
        add jar data\resources\mapreduce-examples.jar -f;
        add file data\resources\import.txt -f;
        ```

    -   The contents of import.txt:

        ```
        1000,odps
        ```


## Procedure {#section_rlv_bzg_vdb .section}

Run Upload on the odpscmd:

```
jar -resources mapreduce-examples.jar,import.txt -classpath data\resources\mapreduce-examples.jar
com.aliyun.odps.mapred.open.example.Upload import.txt mr_upload_src;
```

## Expected output {#section_hzz_dzg_vdb .section}

The content in the output table “mr\_upload\_src” is as follows:

```
+------------+------------+
| key | value |
+------------+------------+
| 1000 | odps |
+------------+------------+
```

## Sample code {#section_jgb_gzg_vdb .section}

```
    package com.aliyun.odps.mapred.open.example;
    import java.io.BufferedInputStream;
    import java.io.FileNotFoundException;
    import java.io.IOException;
    import com.aliyun.odps.data.Record;
    import com.aliyun.odps.data.TableInfo;
    import com.aliyun.odps.mapred.JobClient;
    import com.aliyun.odps.mapred.MapperBase;
    import com.aliyun.odps.mapred.TaskContext;
    import com.aliyun.odps.mapred.conf.JobConf;
    import com.aliyun.odps.mapred.utils.InputUtils;
    import com.aliyun.odps.mapred.utils.OutputUtils;
    import com.aliyun.odps.mapred.utils.SchemaUtils;
    /**
     * Upload
     *
     * Import data from text file into table
     *
     **/
    public class Upload {
      public static class UploadMapper extends MapperBase {
        @Override
        public void setup(TaskContext context) throws IOException{
          Record record = context.createOutputRecord();
          StringBuilder importdata = new StringBuilder();
          BufferedInputStream bufferedInput = null;
          try {
            byte[] buffer = new byte[1024];
            int bytesRead = 0;
            String filename = context.getJobConf().get("import.filename");
            bufferedInput = context.readResourceFileAsStream(filename);
            while ((bytesRead = bufferedInput.read(buffer)) ! = -1) {
              String chunk = new String(buffer, 0, bytesRead);
              importdata.append(chunk);
            }
            String lines[] = importdata.toString().split("\n");
            for (int i = 0; i < lines.length; i++) {
              String[] ss = lines[i].split(",");
              record.set(0, Long.parseLong(ss[0].trim()));
              record.set(1, ss[1].trim());
              context.write(record);
            }
          } catch (FileNotFoundException ex) {
            throw new IOException(ex);
          } catch (IOException ex) {
            throw new IOException(ex);
          } finally {
          }
        }
        @Override
        public void map(long recordNum, Record record, TaskContext context)
            Throws ioexception {
        }
      }
      public static void main(String[] args) throws Exception {
        if (args.length ! = 2) {
          System.err.println("Usage: Upload <import_txt> <out_table>");
          System.exit(2);
        }
        JobConf job = new JobConf();
        job.setMapperClass(UploadMapper.class);
        // Set the Resource Name, which can be obtained from jobconf in the map
        job.set("import.filename", args[0]);
        // Maponly job needs to explicitly set reducer number to 0
        job.setNumReduceTasks(0);
        job.setMapOutputKeySchema(SchemaUtils.fromString("key:bigint"));
        job.setMapOutputValueSchema(SchemaUtils.fromString("value:string"));
        InputUtils.addTable(TableInfo.builder().tableName("mr_empty").build(), job);
        OutputUtils.addTable(TableInfo.builder().tableName(args[1]).build(), job);
        Jobclient. runjob (job );
      }
    }

```

A user can set up JobConf through the following methods:

-   T Use JobConf interface in SDK. This method is used is the preceding example. Moreover, this is the most recommended method and is given the highest priority.
-   In jar command lines, specify new JobConf file through the parameter -conf. This method is of the lowest priority.

