# Summary {#concept_lhh_2dw_tdb .concept}

MaxCompute Tunnel is the data tunnel of MaxCompute. You can use Tunnel to upload data to or download data from MaxCompute.  Tunnel only supports table data uploading and data downloading.

MaxCompute provides [Data upload and download tools](../../../../intl.en-US/Tools and Downloads/Client.md)programmed based on the Tunnel SDK.

When using Maven, you can search for odps-sdk-core in the [Maven database](http://search.maven.org/) to find different versions of Java  SDK. The configuration is as follows:

```
<dependency>
                <groupId>com.aliyun.odps</groupId>
                <artifactId>odps-sdk-core</artifactId>
                <version>0.24.0-public</version>
                </dependency>
```

This document describes the main interfaces of Tunnel SDK, which may vary with the SDK version.

|Main interface|Description|
|:-------------|:----------|
|TableTunnel|The portal class interface to access the MaxCompute Tunnel service.  You can access MaxCompute and its Tunnel using the Internet  or intranet of Alibaba Cloud.  When you download data with MaxCompute Tunnel using intranet of Alibaba Cloud, no traffic fee is incurred. The intranet address is only valid for cloud products in the Hangzhou region.|
|TableTunnel.UploadSession|Indicates a session used to upload data to a MaxCompute table.|
|TableTunnel.DownloadSession|Indicates a session used to download data from a MaxCompute table.|

**Note:** 

-   For more information about the SDK, see [SDK Java Doc](http://repo.aliyun.com/java-sdk-doc/).
-   For more information about service connections, see [Access Domains and Data Centers](../../../../intl.en-US/Prepare/Access domains and data centers.md).

