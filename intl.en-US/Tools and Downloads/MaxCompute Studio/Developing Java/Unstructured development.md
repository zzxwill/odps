# Unstructured development {#concept_vcn_klg_vdb .concept}

An [unstructured data processing framework](../../../../intl.en-US/User Guide/Handle-Unstructured-data/Access OSS Data.md) is added for MaxCompute 2.0, supporting access to the OSS and Table Store using external tables.  Studio provides some code templates for the framework, facilitating users' fast development.

## Compile StorageHandler/Extractor/Outputter {#section_pww_xlg_vdb .section}

1.  After the [MaxCompute Java  Module](intl.en-US/Tools and Downloads/MaxCompute Studio/Developing Java/Create MaxCompute Java Module.md) \(Sample code is provided in the unstructured folder of the examples directory for your reference\).
2.  Right-click the module source code **directory src \> ** \> **main, **select **new**, and select **MaxCompute  Java**.
3.  Specify Name  and Kind. For example, set Name to myun.MyExtractor and Kind to Extractor. Click **OK**.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/12132/2044_en-US.png)

4.  The framework code has been automatically filled in the template. Compile your logic code.
5.  Compile Outputter and StorageHandler by following the preceding steps.

## Unit Testing {#section_pws_tmg_vdb .section}

You can compile the unit test \(UT\) by following the examples in the examples directory to test your Extractor/Outputter.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/12132/2037_en-US.png)

## Package and upload {#section_rws_tmg_vdb .section}

After StorageHandler/Extractor/Outputter is compiled, compress the completed Java program to a JAR package, and upload the package as a resource to the server, see [Package and release](https://www.alibabacloud.com/help/doc-detail/50904.htm).

## Create External Table {#section_vrq_yqg_vdb .section}

1.  Right-click **scripts** and select  **new** \> **MaxCompute Script**.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/12132/2046_en-US.png)

2.  Enter the SQL script name. Select the MaxCompute project in which the script is to be executed for Target Project  and click **OK**.
3.  Select create external table live template in the editor to rapidly insert the script template for creating an external table.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/12132/2039_en-US.png)

    Modify the external table name, column, type, StorageHanlder class path, configuration parameter, external path, and JAR name. Click Run MaxCompute SQL Script to create the external table.

4.  Query the created external table, such as:

    ![](images/2041_en-US.png)


