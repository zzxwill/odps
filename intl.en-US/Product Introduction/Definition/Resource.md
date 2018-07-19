# Resource {#concept_fqd_ygb_5db .concept}

Resources is a concept that is unique to MaxCompute. To accomplish tasks using user-defined functions \(for more information, see [UDF](../../../../intl.en-US/User Guide/SQL/UDF/UDF Summary.md)\), or [MapReduce](../../../../intl.en-US/User Guide/MapReduce/Summary/MapReduce.md), you must use resources.

-   **SQL UDF**: After writing a UDF, you must compile it as a Jar package and upload the package to MaxCompute as a resource.  Then, when you run this UDF, MaxCompute automatically downloads its corresponding JAR package to obtain the written code.  The JAR package is one type of MaxCompute resource.
-   **MapReduce**: After writing a MapReduce program, you must compile it as a Jar package and upload the package to MaxCompute as a resource.  Then, when running a MapReduce job, the MapReduce framework automatically downloads the corresponding JAR package and obtain the written code. You can upload text files and MaxCompute tables to MaxCompute as different types of resources. Then, you can read or use these resources when running UDF or MapReduce.

MaxCompute provides interfaces for you to read and use resources. For more information, see [Use Resourse Example](../../../../intl.en-US/User Guide/MapReduce/Program Example/Resource Sample.md) and [UDTF Usage](../../../../intl.en-US/User Guide/SQL/UDF/Java UDF.md)  .

**Note:** For more information about the resource reading capabilities of user-defined functions \([UDF](../../../../intl.en-US/User Guide/SQL/UDF/UDF Summary.md)\) or [MapReduce](../../../../intl.en-US/User Guide/MapReduce/Summary/MapReduce.md) , see [Application Restriction](../../../../intl.en-US/User Guide/MapReduce/MR Restrictions.md).

Types of MaxCompute resources include:

-   File
-   Table: tables in MaxCompute

    **Note:** Currently, only BIGINT, DOUBLE, STRING, DATETIME, and BOOLEAN fields are supported in tables referenced by MapReduce.

-   Jar type, which is compiled Java JAR packages
-   Archive type, which is the compression type, and is determined by the resource name suffix. Supported compression types include: .zip/.tgz/.tar.gz/.tar/jar

For more information about resources, see [Add Resource](../../../../intl.en-US/User Guide/Common commands/Resource.md) , [Drop Resource](../../../../intl.en-US/User Guide/Common commands/Resource.md) , [List Resources](../../../../intl.en-US/User Guide/Common commands/Resource.md) and [Describe Resource](../../../../intl.en-US/User Guide/Common commands/Resource.md).

