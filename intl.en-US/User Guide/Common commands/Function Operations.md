# Function Operations {#concept_hb1_wdf_vdb .concept}

This article shows how to use the common commands to operate functions in the MaxCompute client. 

You can also operate functions through the visualized online data development tools in DataWorks.You can refer to [Function Management](https://www.alibabacloud.com/help/doc-detail/30278.html).

## Create a Function {#section_rrq_12f_vdb .section}

**The command format is as follows:**

```
CREATE FUNCTION <function_name> AS <package_to_class> USING <resource_list>;
```

**Parameters information:**

-   function\_name：UDF name, which is the name referenced in SQL.
-   package\_to\_class：For Java UDF,  this name is a fully qualified class name \(from top-level package name to UDF class name\). This parameter must be in double quotation marks. For Python UDF,   this name is python script name.  classname.   And for the name you should use quotation marks.
-     resource\_list： resources list used by UDF.
    -   The resource which contains UDF code must be included in the list.
    -   If the your code reads the resource file by distributed cache interface, this list also contains the list of resource files read by the UDF.
    -   The resource list is composed of multiple resource names, separated by comma \(,\). The resource list must be in double quotation marks.
    -   If you need to specify the project in which the resource is located, write it as  `<project_name>/resources/<resource_name>`。

**Examples:**

-   Suppose that  Java UDF class org.alidata.odps.udf.examples.Lower is in my\_lower.jar, create function my\_lower as follows:

    ```
    CREATE FUNCTION test_lower AS org.alidata.odps.udf.examples.Lower USING my_lower.jar;
    USING 'my_lower.jar';
    ```

-   Suppose that  Python UDF MyLower is used in project pyudf\_test.py,create function my\_lower  as follows:

    ```
     create function test_lower as 'pyudf_test.MyLower'
    using 'test_project/resources/pyudf_test.py';
    ```

-     Suppose that  Java UDF class com.aliyun.odps.examples.udf.UDTFResource is in udtfexample1.jar, and it depends on file resource file\_resource.txt and table resource table\_resource1,create function  test\_udtf as follows:

    ```
    create function test_udtf as com.aliyun.odps.examples.udf.UDTFResource using 'udtfexample1.jar, file_resource.txt, table_resource1,test_archive.zip';
    ```


**Note:** 

-   Similarly to the resource files, UDF the same name can only be registered once.
-   Generally UDF cannot overwrite system built-in functions.  Only the project owner has right to overwrite the built-in functions.  If you use a UDF which overwrites the built-in function, the warning is triggered in Summary after SQL execution.

## Drop a Function {#section_esm_m2f_vdb .section}

**The command format is as follows:**

```
DROP FUNCTION <function_name>;
```

**Examples:**

```
DROP FUNCTION test_lower;
```

## List Functions {#section_o2r_42f_vdb .section}

**The command format is as follows:**

```
list functions; --View all user-defined functions in current project.
list functions -p my_project; --View all user-defined functions in the project 'my_project'.
```

