# MapReduce {#concept_vwz_lcd_wdb .concept}

This article shows you how to use Eclipse to develop and run MapReduce programs.

Select WordCount example in MaxCompute project,as follows.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/12152/15450226343125_en-US.png)

Right-click **WordCount.java** and choose **Run As** -\> **ODPS  MapReduce**, as follows.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/12152/15450226343127_en-US.png)

After the dialog box is popped up, select **example\_project** and click Finish。As follows.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/12152/15450226343128_en-US.png)

After running is completed, the following result is displayed,as follows.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/12152/15450226343129_en-US.png)

## Run User-defined MapReduce Program {#section_v3b_ydx_wdb .section}

Right-click src directory. Select **New -\> ** \> **Mapper**,as follows.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/12152/15450226343162_en-US.png)

After selecting Mapper, the following dialog box is displayed.  Input the name of Mapper class and click Finish.as follows.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/12152/15450226343163_en-US.png)

The file UserMapper.java is generated in the src directory in Package Explorer.  The content of this file is a template of Mapper class,as follows.

```
package odps;
import java.io.IOException;
import com.aliyun.odps.data.Record;
import com.aliyun.odps.mapred.MapperBase;
public class UserMapper extends MapperBase {
    @Override
    public void setup(TaskContext context) throws IOException {
    
    @Override
    public void map(long recordNum, Record record, TaskContext context)
            throws IOException {
    
    @Override
    public void cleanup(TaskContext context) throws IOException {
    

```

In the template, the configured package name defaults to odps. You can modify it according to your actual requirement.  Write the template content as follows,as follows.

```
package odps;
import java.io.IOException;
import com.aliyun.odps.counter.Counter;
import com.aliyun.odps.data.Record;
import com.aliyun.odps.mapred.MapperBase;
public class UserMapper extends MapperBase {
    Record word;
    Record one;
    Counter gCnt;
    @Override
    public void setup(TaskContext context) throws IOException {
          word = context.createMapOutputKeyRecord();
          one = context.createMapOutputValueRecord();
          one.set(new Object[] { 1L });
          gCnt = context.getCounter("MyCounters", "global_counts");
    
    @Override
    public void map(long recordNum, Record record, TaskContext context)
            throws IOException {
          for (int i = 0; i < record.getColumnCount(); i++) {
              String[] words = record.get(i).toString().split("\\s+");
              for (String w : words) {
                word.set(new Object[] { w });
                Counter cnt = context.getCounter("MyCounters", "map_outputs");
                cnt.increment(1);
                gCnt.increment(1);
                context.write(word, one);
              
            
          
    @Override
    public void cleanup(TaskContext context) throws IOException {
    

```

Similarly, right-click src directory and select **New -\> ** \> **Reduce:**,as follows.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/12152/15450226343164_en-US.png)

Input the name of Reduce class. \(In this example, use UserReduce as the class name.\)

In Package Explorer, a file name UserReduce.java is generated in the src directory.  This file content is a template of Reduce class.  Edit the template,as follows.

```
package odps;
import java.io.IOException;
import java.util.Iterator;
import com.aliyun.odps.counter.Counter;
import com.aliyun.odps.data.Record;
import com.aliyun.odps.mapred.ReducerBase;
public class UserReduce extends ReducerBase {
    private Record result;
    Counter gCnt;
    @Override
    public void setup(TaskContext context) throws IOException {
          result = context.createOutputRecord();
          gCnt = context.getCounter("MyCounters", "global_counts");
    
    @Override
    public void reduce(Record key, Iterator<Record> values, TaskContext context)
            throws IOException {
          long count = 0;
          while (values.hasNext()) {
            Record val = values.next();
            count += (Long) val.get(0);
          
          result.set(0, key.get(0));
          result.set(1, count);
          Counter cnt = context.getCounter("MyCounters", "reduce_outputs");
          cnt.increment(1);
          gCnt.increment(1);
          context.write(result);
        
    @Override
    public void cleanup(TaskContext context) throws IOException {
    

```

Create main function: right-click src and select **New -\> ** \> **MapReduce Driver**.  Enter Driver  Name \(in this example, use UserDriver as the name\), Mapper and Reduce \(in this example use UserMapper and UserReduce as corresponding names\) and click Finish.  The file MyDriver.java is also displayed in src directory,as follows.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/12152/15450226353167_en-US.png)

Edit the driver content,as follows.

```
package odps;
import com.aliyun.odps.OdpsException;
import com.aliyun.odps.data.TableInfo;
import com.aliyun.odps.examples.mr.WordCount.SumCombiner;
import com.aliyun.odps.examples.mr.WordCount.SumReducer;
import com.aliyun.odps.examples.mr.WordCount.TokenizerMapper;
import com.aliyun.odps.mapred.JobClient;
import com.aliyun.odps.mapred.RunningJob;
import com.aliyun.odps.mapred.conf.JobConf;
import com.aliyun.odps.mapred.utils.InputUtils;
import com.aliyun.odps.mapred.utils.OutputUtils;
import com.aliyun.odps.mapred.utils.SchemaUtils;
public class UserDriver {
    public static void main(String[] args) throws OdpsException {
        JobConf job = new JobConf();
        job.setMapperClass(TokenizerMapper.class);
        job.setCombinerClass(SumCombiner.class);
        job.setReducerClass(SumReducer.class);
        job.setMapOutputKeySchema(SchemaUtils.fromString("word:string"));
        job.setMapOutputValueSchema(SchemaUtils.fromString("count:bigint"));
        InputUtils.addTable(
            TableInfo.builder().tableName("wc_in1").cols(new String[] { "col2", "col3" }).build(), job);
        InputUtils.addTable(TableInfo.builder().tableName("wc_in2").partSpec("p1=2/p2=1").build(), job);
        OutputUtils.addTable(TableInfo.builder().tableName("wc_out").build(), job);
        RunningJob rj = JobClient.runJob(job);
        rj.waitForCompletion();
    

```

Run MapReduce program. Right-click **UserDriver.java** and select **Run As -\> ** \> **ODPS MapReduce, **   the following dialog box is displayed,as follows.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/12152/15450226353168_en-US.png)

Select example\_project as the MaxCompute Project and  click **Finish** to run MapReduce program in the local,as follows.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/12152/15450226353170_en-US.png)

If the output is the same as in the preceding figure, it indicates that local operation runs successfully.  The output result is saved in the warehouse directory.   Refresh MaxCompute project,as follows.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/12152/15450226353172_en-US.png)

wc\_out is the output directory and R\_000000 is the result file. By local debugging, the result is confirmed to be correct and you can package MapReduce program using Eclipse export function.  After it is packaged, upload the jar package to MaxCompute. For more information how to run MapReduce in distributed environment, see [Quick Start](../../../../reseller.en-US/Quick Start/MapReduce.md).

After the local debugging is completed, you can package the codes in jar package using Eclipse Export function, provided for subsequent distributed environment. In this example, the package name is mr-examples.jar. Select the src directory and click **Export**,as follows.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/12152/15450226353180_en-US.png)

Select Jar File as an export mode,as follows.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/12152/15450226353181_en-US.jpg)

You must only export the package in src. The Jar  File name must be specified as mr-examples.jar,as follows.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/12152/15450226353183_en-US.jpg)

Click Next to export the jar file.

If you want to simulate new Project creation in the local, you can create a subdirectory \(has same level with example\_project\) in the warehouse directory. The directory hierarchy structure is shown as follows.

```
<warehouse>
   |____example_project(Project Dirctory)
          |____ <__tables__>
          | |__table_name1(non-partition table)
          | | |____ data(File)
          
          | | |____ <__schema__> (File)
          
          | |__table_name2(Partition Table)
          | |____ partition_name=partition_value(partition directory)
          | | |____ data(file)
          
          | |____ <__schema__> (file)
          
          |____ <__resources__>
                  
                  |___table_resource_name (table resource)
                  | |____<__ref__>
                  
                  |___ file_resource_name(file resource)
```

**schema** Example,as follows.

```
Non-partiton table:
project=project_name
table=table_name
columns=col1:BIGINT,col2:DOUBLE,col3:BOOLEAN,col4:DATETIME,col5:STRING
Partition table:
project=project_name
table=table_name
columns=col1:BIGINT,col2:DOUBLE,col3:BOOLEAN,col4:DATETIME,col5:STRING
partitions=col1:BIGINT,col2:DOUBLE,col3:BOOLEAN,col4:DATETIME,col5:STRING
Note:
Currently, the following five data formats are supported: bigint,double,boolean,datetime,string, which correspond to the data types in java: -long,double,boolean,java.util.Date,java.lang.String.
```

data Example,as follows.

```
1,1.1,true,2015-06-04 11:22:42 896,hello world
\N,\N,\N,\N,\N
Note: 
The time format is accurate to the millisecond level and all types are represented NULL by '\N'.
```

**Note:** 

-   If MapReduce program runs in the local, the default is to search corresponding tables or resources from the warehouse directory. If the tables or resources do not exist, corresponding data will be downloaded from the server and saved in warehouse. Then run MapReduce in the local.
-   After running MapReduce is finished, refresh the warehouse directory to view the generated result.

