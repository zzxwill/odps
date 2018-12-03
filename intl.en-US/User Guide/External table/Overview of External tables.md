# Overview of External tables {#concept_znp_tdb_wdb .concept}

MaxCompute is the core computing component of the Alibaba Cloud big data platform. It possesses powerful computing capabilities and can schedule parallel computing tasks on a large volume of nodes. It also provides a set of proven processing management mechanisms for distributed computing failover, retry, and other functions.

As the entry of distributed data processing, MaxCompute SQL provides powerful support for quick processing and storing of exabytes of offline data. With the continuous expansion of big data business, many new cases of data usage are emerging, and the MaxCompute computing frameworks are also evolving. Access to powerful computation capabilities is gradually opening to external data sources instead of to internal data with special formats.

At this stage, MaxCompute SQL faces structured data stored in the internal MaxCompute table in cfile format. For other user data outside of MaxCompute tables \(including text and various types of unstructured data\), you must first import the data to MaxCompute tables using various tools and then compute it. The process of data import has great limitations. For example, to process OSS data in MaxCompute, two common methods can be used:

-   To download data from OSS using the OSS SDK or other tools, the data is then imported into the table through the MaxCompute tunnel.
-   Write the UDF and call the OSS SDK directly within the UDF to access the OSS data.

But there are shortcomings in both of these practices:

-   You must relay data outside of the MaxCompute system. If the OSS data volume is too large, you need to consider using concurrent operations to accelerate the process and you cannot make full use of MaxCompute's large-scale computing capabilities.
-   The second type typically needs to apply for UDF network access, there is also a problem for developers to control the number of job concurrency and how the data is split.

This section describes the functionality of an External table, support is designed to provide the ability to process data other than existing MaxCompute tables. In this framework, you use a simple DDL statement to create an external table in MaxCompute. Then, you can associate MaxCompute tables with the external data source to provide various data access and output capabilities. After creating an external table, you can use it like a MaxCompute table \(in most situations\), to take full advantage of MaxCompute SQL's powerful computing functions.

**Note:** Using the external tables feature, the data of the external tables is not copied and placed on the MaxCompute for storage.

Here, a variety of data covers two dimensions:

A variety of data storage media: a plug-in framework can be used to connect to a wide variety of data storage media, such as OSS, OTS.

Diverse data formats: The MaxCompute table is structured data, external tables can not be limited to structured data.

-   There is no structured data, such as images, audio, video files, raw bindings, and so on.
-   Semi-structured data, such as CSV, TSV, and so on, implies a certain schema text file. Structured Data for a non-cfile, such as an orc/parquet file, or even hbase/OTS data.

We'll take some examples to help you gain insight into the processing of unstructured data. To access OSS and OTS unstructured data see accessing [OSS unstructured data](reseller.en-US/User Guide/External table/Accessing OSS unstructured data.md) and [accessing OTS unstructured data](reseller.en-US/User Guide/External table/Visit Table Store Data.md). External tables access the OSS account, and in Ram customize the permissions that authorize MaxCompute to access the OSS see[OSS STS mode authorization](reseller.en-US/User Guide/External table/OSS STS mode authorization.md#). The unstructured framework of MaxCompute supports output of MaxCompute data directly to OSS via insert, see [Unstructured data exported to OSS](reseller.en-US/User Guide/External table/Unstructured data exported to OSS.md#). To work with data on middleware databases, see [Processing MySQL Data stored on TDDL](reseller.en-US/User Guide/External table/Processing Mysql Data stored on TDDL.md#), see [Processing open source format data for OSS](reseller.en-US/User Guide/External table/Processing open source format data for OSS.md#)handling data in a variety of open source formats.

