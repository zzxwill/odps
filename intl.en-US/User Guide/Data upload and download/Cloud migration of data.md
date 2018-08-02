# Cloud migration of data {#concept_c3l_njf_vdb .concept}

[Data upload and data download tools](intl.en-US/User Guide/Data upload and download/Tools.md) of the MaxCompute platform are applicable for a wide range of cloud data migration scenarios. This document introduces some typical scenarios.

## Hadoop data migration {#section_xy1_jkf_vdb .section}

You can use Sqoop or DataWorks for Hadoop data migration.

-   Sqoop runs an MR job on the original Hadoop cluster for distributed data transmission to MaxCompute and is highly efficient. For more information, see the Sqoop tool introduction.
-   DataWorks can be used with DataX for Hadoop data migration.


## Database synchronization {#section_i51_4kf_vdb .section}

To synchronize data of database to MaxCompute, select an appropriate tool based on the database type and synchronization policy.

-   For offline batch data synchronization, you can use DataWorks which supports a wide range of database types, including MySQL, SQL Server, and PostgreSQL. For more information, see [Data synchronization introduction](https://www.alibabacloud.com/help/doc-detail/47677.html). For instance operation instructions, see [Create a synchronization task](https://www.alibabacloud.com/help/doc-detail/30269.htm).
-   For real-time Oracle data synchronization, use OGG plug-in tools.
-   For real-time RDS data synchronization, use DTS.

## Log collection {#section_k51_4kf_vdb .section}

For log collection, use Flume, Fluentd, and Logstash tools.

