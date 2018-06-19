# UDF {#concept_vz1_scd_wdb .concept}

This section describes how to develop UDF with the Eclipse plug-in and how to run UDF on local. The preparation and implement process is similar to UDF. You can see the example of UDF.   MaxCompute Eclipse plug-in provides two methods to run UDF: Menu Bar and run by right-clicking it.

## Run UDF using Menu Bar {#section_t43_ckx_wdb .section}

1.  Select **Run \> ** \> **Run Configurations… from the menu bar ** and the following dialog box appears:

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/12153/3192_en-US.PNG)

2.  You can create a new Run Configuration. Select the UDF class and type to be executed, select MaxCompute Project and enter the information of input table. For example:

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/12153/3193_en-US.JPG)

    In the preceding configuration, Table indicates the input table of UDF. Partitions indicates the partitions from which the data is read, separated by commas. Columns indicates the columns, which are considered as the parameters of UDF to be introduced. The columns are separated by commas.

3.  Click **Run** to run the program and the running result is displayed in the console:

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/12153/3194_en-US.JPG)


## Run by right-clicking {#section_ap3_ckx_wdb .section}

1.  Select a udf.java file \(such as UDFExample.java\) and right-click it. Then select **Run As ** \> **\> Run UDF|UDAF|UDTF**:

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/12153/3196_en-US.png)

2.  The configuration information is shown as follows:

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/12153/3197_en-US.png)

    In the preceding configuration, Table indicates the input table of UDF. Partitions indicates the partitions from which the data is read, separated by commas. Columns indicates the columns, which are considered as the parameters of UDF to be introduced. The columns are separated by commas.

3.  Click **Finish** to run UDF and get the output result.

## Running customized UDF program {#section_gp3_ckx_wdb .section}

Right-click a project and select **New ** \> **\>UDF **\(or select the menu bar **File \> ** \> **New \> ** \> **UDF**\).

Enter the UDF class name and click **Finish**.  Generate a Java file in corresponding src directory with the same name as this UDF class. Edit this java file as follows:

```
package odps;
import com.aliyun.odps.udf.UDF;
public class UserUDF extends UDF {
      
       * project: example_project 
       * table: wc_in1 
       * columns: col1,col2
        
       
      public String evaluate(String a, String b) {
        return "ss2s:" + a + "," + b;
      }

```

Right-click this java file \(such as UserUDF.java\) and select **Run As -\>  ODPS UDF|UDTF|UDAF**:

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/12153/3200_en-US.png)

Configure the following dialog box:

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/12153/3201_en-US.png)

Click **Finish** to get the result:

```
ss2s:A1,A2
ss2s:A1,A2
ss2s:A1,A2
ss2s:A1,A2
```

Only the operation instance of UDF is described in this section, and the way of UDTF operating is basically similar to the UDF.

