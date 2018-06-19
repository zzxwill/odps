# Data upload and download {#concept_am2_p3f_vdb .concept}

This document describes the process of uploading and downloading MaxCompute system data, including service connection, SDKs, tools, and cloud data migration.

You can use the real-time data tunnel of DataHub or the batch data tunnel of Tunnel to access the MaxCompute system.  Both DataHub and Tunnel provide their own SDKs. The SDKs and derivative data upload and data download tools can meet your requirements for data upload and data download in a variety of scenarios.

Data upload and data download tools include the following: DataWorks , DTS , OGG plugin , Sqoop , Flume plugin , Logstash plugin , Fluentd plugin , Kettle plugin , MaxCompute console.

Underlying data tunnels used by these tools include the following:

-   DataHub tunnel tools
    -   OGG
    -   Flume
    -   LogStash
    -   Fluentd
-   Tunnel tools
    -   DataWorks
    -   DTS
    -   Sqoop
    -   Kettle
    -   MaxCompute  console

The wide range of data upload and data download tools are applicable for most scenarios of cloud data migration. The following documents introduce the tools, Hadoop data migration, database data synchronization, log collection, and other cloud migration scenarios, which you can reference when you are selecting technical solutions.

