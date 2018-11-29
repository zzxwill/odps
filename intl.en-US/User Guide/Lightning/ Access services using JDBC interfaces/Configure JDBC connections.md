# Configure JDBC connections {#concept_qjz_415_z2b .concept}

To connect SQL client tools to MaxCompute projects, you must have JDBC URLs for the MaxCompute projects.

The following shows the format of a JDBC URL:

```
jdbc:postgresql://endpoint:port/database
```

The following table describes the connection parameters:

|Parameter|Value|Description|
|:--------|:----|:----------|
|endpoint|Access domain name of MaxCompute Lightning in the region|For more information, see [Access domain name](reseller.en-US/User Guide/Lightning/Access domain name.md#). For example, accessing the Shanghai Region service through the external network using `lightning.cn-shanghai.maxcompute.aliyun.com`|
|port|443|-|
|database|Name of a MaxCompute project|-|
|User|Access Key ID of the user|-|
|password|Access Key Secret of the user|-　|
|ssl|true|MaxCompute Lightning servers are enabled with SSL protection by default, and you must use SSL connections.|
|prepareThreshold|0|Optional. When using the JDBC PrepareStatement function, it is recommended to set `prepareThreshold=0`.|

For example, `jdbc:postgresql://lightning.cn-shanghai.maxcompute.aliyun.com:443/myproject`

You must specify the user, password, and SSL connection parameters before establishing a connection to MaxCompute projects.

You can also add parameters to the JDBC URL to connect to MaxCompute projects. For example:

```
jdbc:postgresql://lightning.cn-shanghai.maxcompute.aliyun.com:443/myproject? ssl=true& prepareThreshold=0&user=xxx&password=yyy
```

Note:

-   lightning.cn-shanghai.maxcompute.aliyun.com: The endpoint of the China East 2 region.
-   Myproject: The name of the MaxCompute project you want to access.
-   SSL=true: The application of SSL connections.
-   xxx: Access Key ID of the user.
-   yyy: Access Key Secret of the user.

