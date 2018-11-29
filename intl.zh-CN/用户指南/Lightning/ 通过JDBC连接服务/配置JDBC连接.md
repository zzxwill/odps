# 配置JDBC连接 {#concept_qjz_415_z2b .concept}

您需要获得您的MaxCompute项目JDBC URL，才能将SQL客户端工具连接到该项目。

JDBC URL命名方式如下：

```
jdbc:postgresql://endpoint:port/database
```

连接参数说明如下：

|参数|取值|说明|
|:-|:-|:-|
|endpoint|所在区域不同网络环境下的Lightning访问域名|详情请参见[访问域名](intl.zh-CN/用户指南/Lightning/访问域名.md#)，例如通过外网访问上海Region的服务使用`lightning.cn-shanghai.maxcompute.aliyun.com`|
|port|443|-|
|database|填写MaxCompute的项目名称|-|
|user|访问用户的Access Key ID|-|
|password|访问用户的Access Key Secret|-　|
|ssl|true|MaxCompute Lightning服务端默认开启SSL服务，客户端需要使用SSL进行连接。|
|prepareThreshold|0|可选。需要使用JDBC PrepareStatement功能时， 建议设置`prepareThreshold=0`。|

例如`jdbc:postgresql://lightning.cn-shanghai.maxcompute.aliyun.com:443/myproject`

同时，设置user、password、SSL连接参数后进行连接。

您也可以将参数添加到JDBC URL来连接到MaxCompute项目，如下所示：

```
jdbc:postgresql://lightning.cn-shanghai.maxcompute.aliyun.com:443/myproject?ssl=true& prepareThreshold=0&user=xxx&password=yyy
```

说明如下：

-   lightning.cn-shanghai.maxcompute.aliyun.com是华东2区域的Endpoint。
-   myproject是需要访问的MaxCompute项目名称。
-   ssl=true是指定通过SSL方式连接。
-   xxx是访问用户的Access Key ID。
-   yyy是访问用户的Access Key Secret。

