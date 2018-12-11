# Cloud data migration {#concept_c3l_njf_vdb .concept}

[Data upload and data download tools](reseller.en-US/User Guide/Data upload and download/Data upload and download tools.md) of the MaxCompute platform can be used for a wide range of cloud data migration scenarios. This article introduces some typical scenarios.

## Hadoop data migration {#section_xy1_jkf_vdb .section}

For Hadoop data migration, either use Sqoop or DataWorks.

-   Sqoop runs an MR job on the original Hadoop cluster for the distributed data transmission to MaxCompute and is highly efficient. For more information, see [Sqoop tool introduction](http://sqoop.apache.org/).
-   DataWorks can be combined with DataX for Hadoop data migration.


## Database synchronization {#section_i51_4kf_vdb .section}

To synchronize the data of a database to MaxCompute, select an appropriate tool based on the database type and synchronization rule.

-   For offline batch data synchronization, use DataWorks. It supports a wide range of database types, including MySQL, SQL Server, and PostgreSQL. For more information, see [Data synchronization introduction](https://www.alibabacloud.com/help/doc-detail/47677.html). For instance operation instructions, see [Create a synchronization task](https://www.alibabacloud.com/help/doc-detail/30269.htm).
-   For real-time Oracle data synchronization, use OGG plug-in tools.
-   For real-time RDS data synchronization, use DTS.

## Log collection {#section_k51_4kf_vdb .section}

For log collection, use Flume, Fluentd, and Logstash tools.

