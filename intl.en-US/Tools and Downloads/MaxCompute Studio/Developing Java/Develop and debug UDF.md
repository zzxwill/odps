# Develop and debug UDF {#concept_nvh_s2b_5db .concept}

After the [MaxCompute Java module is created, ](intl.en-US/Tools and Downloads/MaxCompute Studio/Developing Java/Create MaxCompute Java Module.md)the UDF program can be developed.

## Procedure {#section_aqr_m1g_vdb .section}

1.  Unfold the created MaxCompute Java Module directory, navigate to **src \> ** \> **main \> ** \> **java \> ** \> **new** , and click **MaxCompute Java** Java,  as shown in the following figure.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/12130/1944_en-US.png)

2.  Set **Name** and **Kind**, and click **OK** ,  as shown in the following figure.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/12130/1947_en-US.png)

    -   Name: Specifies the name of the MaxCompute Java Class. If you have not created a package, you can  enter packagename.classname to automatically create a package.

    -   Kind: Specifies the type. Supported types include custom functions \(UDF/UDAF/UDTF\), MapReduce \(Driver/Mapper/Reducer\), and non-structural development \(StorageHandler/Extractor\).

3.  After the creation is successful, the Java program can be developed, modified, and tested.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/12130/1948_en-US.png)

    **Note:** The code template can be customized in Intellij,  preference-Editor-file and code Templates specificly, and then look for the corresponding template modifications for maxcompute in the Code tab.


## Debug the UDF program {#section_vqx_3cg_vdb .section}

After the UDF program is developed,  it can be tested using UT or local running to check whether it meets expectations.

**UT**

There are various UT examples  in the examples directory and you can refer to them to compile your UT.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/12130/1949_en-US.png)

**Local running**

During local running of the UDF program, the running data source must be specified. The following two methods are provided to set the test data source:

-   MaxCompute Studio uses the Tunnel Service to automatically download table data of a specific project to the warehouse directory.

-   The mock project and table data are provided. You can see example\_project in warehouse to set it by yourself.


**Procedure**

1.  Right-click UDF Class and select **Run UDF class.main\(\)**. The Run Configuration dialog box is displayed. Generally, UDF/UDAF/UDTF  data is used as columns in tables of a select substatement. The MaxCompute project, table, and column must be configured. \(**The metadata  is from the mock project under project explorer and warehouse**.\) Debugging for complex types is also supported, as shown in the following figure:

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/12130/1950_en-US.png)

2.  Click **OK**.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/12130/1951_en-US.png)

    **Note:** 

    -   If table data of a specific project is not downloaded to warehouse,  download the data first. By default, 100 data records are downloaded.  If more data is required, use the Tunnel Command of the console or table downloading function of Studio.
    -   If the mock project is used or the table data is downloaded, directly run the program.
    -   The UDF local run framework uses data in the specific columns in warehouse as the UDF input and runs the  UDF program locally. You can view log output and result display on the console.

**Local warehouse directory**

The local warehouse directory is used to store tables \(including meta and data\) or resources for local UDF or MR running.   The following figure shows the warehouse directory.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/12130/1952_en-US.png)

**Note:** 

-   The project name, tables, table name, table scheme, and sample data are under the warehouse directory in sequence.
-   The schema file is configured with the project name, table name, and column name and type \(separated by a colon\) in sequence. For a partition table, the partition column also must be configured. \(For a non-partition table, see wc\_in1. For a partition table, see  wc\_in2.\)
-   The data file uses the standard CSV format to store table sample data.
    -   Special characters include comma, double quotation marks, and line feed \(\\n or \\r\\n\).
    -   The column separator is comma and the line separator is \\n or \\r\\n.
    -   If the column content includes special characters, double quotation marks \(“\) must be added before and after the column content. For example, if the column content is 3,No, it is changed to “3, No”.
    -   If the column content includes double quotation marks, each double quotation mark is converted to two double quotation marks. For example, if the column content is a”b”c, it is changed to “a””b””c”.
    -   \\N indicates that a column is null. If the column content \(string type\) is \\N, it must be converted to “””\\N”””.
    -   The file character code is UTF-8.

