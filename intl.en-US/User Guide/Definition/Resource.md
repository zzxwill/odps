# Resource {#concept_fqd_ygb_5db .concept}

This paper introduces the concept of MaxCompute Resource, which can provide resource dependency for MaxCompute specific operations.

## The concept of resources {#section_fdb_ftt_bgb .section}

Resources is a concept that is unique to MaxCompute. To accomplish tasks using user-defined functions \(for more information, see [UDF](../../../../reseller.en-US/User Guide/SQL/UDF/UDF Summary.md)\), or [MapReduce](../../../../reseller.en-US/User Guide/MapReduce/Summary/MapReduce.md), you must use resources.

-   **SQL UDF**: After writing a UDF, you must compile it as a Jar package and upload the package to MaxCompute as a resource.  Then, when you run this UDF, MaxCompute automatically downloads its corresponding JAR package to obtain the written code.  The JAR package is one type of MaxCompute resource.
-   **MapReduce**: After writing a MapReduce program, you must compile it as a Jar package and upload the package to MaxCompute as a resource.  Then, when running a MapReduce job, the MapReduce framework automatically downloads the corresponding JAR package and obtain the written code. You can upload text files and MaxCompute tables to MaxCompute as different types of resources. Then, you can read or use these resources when running UDF or MapReduce.

## Resource type {#section_ncb_g5t_bgb .section}

MaxCompute provides interfaces for you to read and use resources. For more information, see [Use Resourse Example](../../../../reseller.en-US/User Guide/MapReduce/Program Example/Resource samples.md) and [UDTF Usage](../../../../reseller.en-US/User Guide/SQL/UDF/Java UDF.md)  .

**Note:** For more information about the resource reading capabilities of user-defined functions \([UDF](../../../../reseller.en-US/User Guide/SQL/UDF/UDF Summary.md)\) or [MapReduce](../../../../reseller.en-US/User Guide/MapReduce/Summary/MapReduce.md) , see [Application Restriction](../../../../reseller.en-US/User Guide/MapReduce/MR limits.md).

The max size that MaxCompute support for single resource is 500MB. Types of MaxCompute resources include:

-   File
-   Table: tables in MaxCompute

    **Note:** Currently, only BIGINT, DOUBLE, STRING, DATETIME, and BOOLEAN fields are supported in tables referenced by MapReduce.

-   Jar type, which is compiled Java JAR packages
-   Archive type, which is the compression type, and is determined by the resource name suffix. Supported compression types include: .zip/.tgz/.tar.gz/.tar/jar

For more information about resources, see [Add Resource](../../../../reseller.en-US/User Guide/Common commands/Resources.md) , [Drop Resource](../../../../reseller.en-US/User Guide/Common commands/Resources.md) , [List Resources](../../../../reseller.en-US/User Guide/Common commands/Resources.md) and [Describe Resource](../../../../reseller.en-US/User Guide/Common commands/Resources.md).

