# Join samples {#concept_awd_xdh_vdb .concept}

The MaxCompute MapReduce framework does not support join logic on its own. Therefore, you have to apply join samples of the data in your own map/reduce function which requires you to do some extra work.

Suppose,to join two tables \(Key bigint, value string\) and \(key bigint, value string\), the output table is chain bigint \(value1 string, value2 string \), where value1 and value2 are the values of the scanner.

## Prerequisites {#section_e3n_syg_vdb .section}

1.  Prepare the jar package for the test program, assuming the name is maid and the local storage path is data \\ resources.
2.  Prepare tables and resources for testing the Join operation.
    -   Create tables:

        ```
        create table mr_Join_src1(key bigint, value string);
        create table mr_Join_src2(key bigint, value string);
        create table mr_Join_out(key bigint, value1 string,value2 string);
        ```

    -   Add resources:

        ```
        add jar data\resources\mapreduce-examples.jar -f;
        ```

3.  Run tunnel to import the data:

    ```
    tunnel upload data1 mr_Join_src1;
    tunnel upload data2 mr_Join_src2;
    ```

    Import the contents of the maid data as follows:

    ```
     1, hello
     2, ODPS
    ```

    Import the contents of the maid data as follows:

    ```
    1, ODPS
    3,hello
    4, ODPS
    ```


## Procedure {#section_rlv_bzg_vdb .section}

Join in odpscmd as follows:-

```
jar -resources mapreduce-examples.jar -classpath data\resources\mapreduce-examples.jar
com.aliyun.odps.mapred.open.example.Join mr_Join_src1 mr_Join_src2 mr_Join_out;
```

## Expected output {#section_hzz_dzg_vdb .section}

After the job is completed successfully, the contents of the table maid are output, as follows:

```
+------------+------------+------------+
| key | value1 | value2 |
+------------+------------+------------+
| 1 | hello | odps | 
+------------+------------+------------+
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
    Import com. aliyun. ODPS. Data. record;-
    import com.aliyun.odps.data.TableInfo;
    import com.aliyun.odps.mapred.JobClient;
    import com.aliyun.odps.mapred.MapperBase;
    import com.aliyun.odps.mapred.ReducerBase;
    import com.aliyun.odps.mapred.conf.JobConf;
    import com.aliyun.odps.mapred.utils.InputUtils;
    import com.aliyun.odps.mapred.utils.OutputUtils;
    import com.aliyun.odps.mapred.utils.SchemaUtils;
    /**
     * Join, mr_Join_src1/mr_Join_src2(key bigint, value string), mr_Join_out(key
     * bigint, value1 string, value2 string)
     * 
     */
    public class Join {
      public static final Log LOG = LogFactory.getLog(Join.class);
      public static class JoinMapper extends MapperBase {
        private Record mapkey;
        private Record mapvalue;
        private long tag;
        @Override
        public void setup(TaskContext context) throws IOException{
          mapkey = context.createMapOutputKeyRecord();
          mapvalue = context.createMapOutputValueRecord();
          tag = context.getInputTableInfo().getLabel().equals("left") ? 0: 1;
        }
        @Override
        public void map(long key,Record record, TaskContext context)
            Throws ioexception {
          mapkey.set(0,record.get(0));
          mapkey.set(1,tag);
          for (int i = 1; i< record.getColumnCount();i++) {
            mapvalue.set(i -1, record.get(i));
          }
          context.write(mapkey,mapvalue);
        }
      }
      public static class JoinReducer extends ReducerBase {
        private Record result = null;
        @Override
        public void setup(TaskContext context) throws IOException{
          result = context.createOutputRecord();
        }
        //Reduce function all records for each input will be the same key
        @Override
        public void reduce(Record key,Iterator<Record>values,TaskContext context)
            Throws ioexception {
          long k = key.getBigint(0);
          List<Object[]> leftValues = new ArrayList<Object[]>();
          //Is a key + tag combination because it is set up, this ensures that record data in the left table is in front of the input record for the reduce function.
          while(values.hasNext()) {
            Record value = values.next();
            long tag = (Long)key.get(1);
            //The data for the left table is first cached into memory
            if (tag == 0) {
              leftValues.add(value.toArray().clone());
            }else {
              //The data that touches the right table is output by a join with all the data on the left table, the data for the left table is all in memory.
//This implementation is just a functional display with relatively low performance and is not recommended for practical production.
              for (Object[] leftValue :leftValues) {
                int index = 0;
                result.set(index++,k);
                for (int i = 0;i<leftValue.length;i++) {
                  result.set(index++,leftValue[i]);
                }
                for (int i = 0;i< value.getColumnCount();i++) {
                  result.set(index++,value.get(i));
                }
                context.write(result);
              }
            }
          }
        }
      }
      public static void main(String[] args) throws Exception {
        if (args.length ! = 3) {
          System.err.println("Usage: Join <input table1> <input table2> <out>");
          System.exit(2);
        }
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
        Jobclient. runjob (job );
      }
    }

```

