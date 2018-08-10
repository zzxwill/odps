# Summary {#concept_lhh_2dw_tdb .concept}

MaxCompute Tunnel is the data tunnel of MaxCompute.It helps in uploading and downloading data to MaxCompute. However, Tunnel only supports table data upload and download. Tunnel only supports table data uploading and data downloading.

Based on the Tunnel SDK, MaxCompute offers [Data upload and download tools](../../../../intl.en-US/Tools and Downloads/Client.md).

When using [Maven](http://search.maven.org/), you can search for odps-sdk-core in the Maven database to find different versions of Java SDK. The configuration is as follows: SDK \(available in different versions\).

```
<dependency>
    <groupId>com.aliyun.odps</groupId>
    <artifactId>odps-sdk-core</artifactId>
    <version>0.24.0-public</version>
</dependency>
```

This document describes the main interfaces of Tunnel SDK, which may differ according to the SDK version.You can refer to [SDK Java Doc](http://repo.aliyun.com/java-sdk-doc/?spm=5176.doc34614.2.4.j5zSFu).

|Interface|Description|
|:--------|:----------|
|TableTunnel|The portal class interface to access the MaxCompute Tunnel service.  You can access MaxCompute and its Tunnel using the Internet or intranet of Alibaba Cloud.  No traffic fee is incurred when you use intranet to download data through MaxCompute Tunnel.The intranet address is only valid for cloud products in the Hangzhou region.|
|TableTunnel.UploadSession|Indicates a process of uploading data to a MaxCompute table.|
|TableTunnel.DownloadSession|Indicates a process of downloading data from a MaxCompute table.|

**Note:** 

-   For more information about the SDK, see [SDK Java Doc](http://repo.aliyun.com/java-sdk-doc/).
-   For more information about service connections, see [Access Domains and Data Centers](../../../../intl.en-US/Prepare/Endpoints and Data Centers.md).

