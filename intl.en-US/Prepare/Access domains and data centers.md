# Access domains and data centers {#concept_m2j_h1y_5db .concept}

This sections details the regions where MaxCompute is available, and connection methods. Problems such as network connectivity and downloading charges related to use of other Alibaba Cloud products \(such as ECS, TableStore, OSS, and so on\) are also detailed.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/11949/1423_en-US.png)

MaxCompute provides two types of service connection addresses as follows:

-   The connection address of the MaxCompute service itself. All requests except uploading and downloading data can be sent to MaxCompute, such as creating a table, deleting a function, creating a job, and more.
-   The connection address of MaxCompute Tunnel service, which permits users the ability to upload and download data. When you want to upload and download data through the Tunnel, you can initiate a request through the link address provided by the Tunnel.

    **Note:** Because the deployment and network connection status for every region are different, the billing rules for downloading data through Tunnel are also different.


## Accessing method and data downloading billing rule {#section_ydd_51y_5db .section}

According to the deployment and network conditions of Alibaba Cloud regions, it mainly contains three connection methods as follows:

-   Access MaxCompute Service or Tunnel Service from the Internet.
-   Access MaxCompute service and Tunnel service from Alibaba Cloud classic network.
-   Access MaxCompute service and Tunnel service from Alibaba Cloud VPC network.

**Note:** You only need to specify a network when you want to connect to a project, not during MaxCompute project creation.

**Data uploading**

Data uploading is free-of-charge, regardless of which kind of network configuration is adopted.

\*\***Data downloading**

According to the Tunnel service request to download data, and from which region of the ECS server, the charging rules are as follows:

-   If requester and requestee are both in the same region, the tunnel download request through Alibaba Cloud classic network or VPC network is **free**.
-   If requester and requestee are not in the same region or cannot meet the same region access, then the request is a cross-region access, and data download **fees** are calculated under this condition.

    **Note:** Due to deployment and network conditions of cross-regional **access of Alibaba Cloud products and services**, MaxCompute cannot be **guaranteed** a permanent connection. Some fees may apply.


## The connectivity configuration {#section_d2d_51y_5db .section}

MaxCompute 2.0 supports reading and writing OSS object, and also supports reading and writing tables of Table Store. For more information, see [Access OSS Unstructured Data](../../../../intl.en-US/User Guide/Handle-Unstructured-data/Access OSS Data.md) and [Access Table Store Data](../../../../intl.en-US/User Guide/Handle-Unstructured-data/Visit Table Store Data.md).

Configuration instructions about network connectivity are as follows:

-   If MaxCompute and TableStore/OSS are in the same region, we recommend to configure the Alibaba Cloud classic network or VPC network connection method. The external network method can also be selected.
-   If MaxCompute and TableStore/OSS are not in the same region, select the external network method to connect. In the case of cross-regional access, connectivity is not permanently guaranteed if you select the Alibaba Cloud classic network or VPC network method.

## MaxCompute activation and service connections by region {#section_f2d_51y_5db .section}

The table as follows gives information about deployment of MaxCompute in different regions. On the International Site, MaxCompute is currently only available in the following regions. You can apply to use MaxComputer in this region only. In this case, your data storage and computing consumption all occur in the Asia Pacific SE 1 \(Singapore\) region.

**Note:** All Endpoints support http and https protocol. If the request need to be encrypted, use https.

-   **External MaxCompute activation and service connections by region**

    |English region name|Location|Service status|Internet endpoint|Internet tunnel endpoint|
    |:------------------|:-------|:-------------|:----------------|:-----------------------|
    |East China 1|cn-hangzhou|In service|http://service.cn.maxcompute.aliyun.com/api|http://dt.cn-hangzhou.maxcompute.aliyun.com|
    |East China 2|cn-shanghai|In service|http://service.cn.maxcompute.aliyun.com/api|Http://dt.cn-shanghai.maxcompute.aliyun.com|
    |North China 2|cn-beijing|In service|http://service.cn.maxcompute.aliyun.com/api|Http://dt.cn-beijing.maxcompute.aliyun.com|
    |South China 1|cn-shenzhen|In service|http://service.cn.maxcompute.aliyun.com/api|Http://dt.cn-shenzhen.maxcompute.aliyun.com|
    |cn-hongkong|cn-hongkong|In service|Http://service.cn-hongkong.maxcompute.aliyun.com/api|Http://dt.cn-hongkong.maxcompute.aliyun.com|
    |ap-southeast-1|Singapore|In service|http://service.ap-southeast-1.maxcompute.aliyun.com/api|http://dt.ap-southeast-1.maxcompute.aliyun.com|
    |ap-southeast-2|Australia|In service|http://service.ap-southeast-2.maxcompute.aliyun.com/api|http://dt.ap-southeast-2.maxcompute.aliyun.com|
    |ap-southeast-3|Kuala Lumpur|In service|Http://service.ap-southeast-3.maxcompute.aliyun.com/api|Http://dt.ap-southeast-3.maxcompute.aliyun.com|
    |ap-southeast-5|Jakarta|In service|Http://service.ap-southeast-5.maxcompute.aliyun.com/api|Http://dt.ap-southeast-5.maxcompute.aliyun.com|
    |ap-northeast-1|Japan|In service|Http://service.ap-northeast-1.maxcompute.aliyun.com/api|Http://dt.ap-northeast-1.maxcompute.aliyun.com|
    |eu-central-1|Frankfurt|In service|Http://service.eu-central-1.maxcompute.aliyun.com/api|Http://dt.eu-central-1.maxcompute.aliyun.com|
    |us-west-1|Silicon Valley|In service|Http://service.us-west-1.maxcompute.aliyun.com/api|Http://dt.us-west-1.maxcompute.aliyun.com|
    |us-east-1|Virginia|In service|Http://service.us-east-1.maxcompute.aliyun.com/api|Http://dt.us-east-1.maxcompute.aliyun.com|
    |ap-south-1|Mumbai|In service|http://service.ap-south-1.maxcompute.aliyun.com/api|http://dt.ap-south-1.maxcompute.aliyun.com|

-   **Classic network MaxCompute activation and service connections by region**

    |English region name|Location|Service status|Classic network endpoint|Classic Network tunnel endpoint|
    |-------------------|--------|--------------|------------------------|-------------------------------|
    |East China 1|Hangzhou|In service|http://service.cn.maxcompute.aliyun-inc.com/api|http://dt.cn-hangzhou.maxcompute.aliyun-inc.com|
    |East China 2|Shanghai|In service|http://service.cn.maxcompute.aliyun-inc.com/api|http://dt.cn-shanghai.maxcompute.aliyun-inc.com|
    |North China 2|Beijing|In service|http://service.cn.maxcompute.aliyun-inc.com/api|http://dt.cn-beijing.maxcompute.aliyun-inc.com|
    |South China 1|Shenzhen|In service|http://service.cn.maxcompute.aliyun-inc.com/api|http://dt.cn-shenzhen.maxcompute.aliyun-inc.com|
    |Hong Kong|Hong Kong|In service|http://service.cn-hongkong.maxcompute.aliyun-inc.com/api|http://dt.cn-hongkong.maxcompute.aliyun-inc.com|
    |ap-southeast-1|Singapore|In service|http://service.ap-southeast-1.maxcompute.aliyun-inc.com/api|http://dt.ap-southeast-1.maxcompute.aliyun-inc.com|
    |ap-southeast-2|Australia|In service|http://service.ap-southeast-2.maxcompute.aliyun-inc.com/api|http://dt.ap-southeast-2.maxcompute.aliyun-inc.com|
    |ap-southeast-3|Kuala Lumpur|In service|http://service.ap-southeast-3.maxcompute.aliyun-inc.com/api|http://dt.ap-southeast-3.maxcompute.aliyun-inc.com|
    |ap-southeast-5|Jakarta|In service|http://service.ap-southeast-5.maxcompute.aliyun-inc.com/api|http://dt.ap-southeast-5.maxcompute.aliyun-inc.com|
    |ap-northeast-1|Japan|In service|http://service.ap-northeast-1.maxcompute.aliyun-inc.com/api|http://dt.ap-northeast-1.maxcompute.aliyun-inc.com|
    |eu-central-1|Frankfurt|In service|http://service.eu-central-1.maxcompute.aliyun-inc.com/api|http://dt.eu-central-1.maxcompute.aliyun-inc.com|
    |us-west-1|Silicon Valley|In service|http://service.us-west-1.maxcompute.aliyun-inc.com/api|http://dt.us-west-1.maxcompute.aliyun-inc.com|
    |us-east-1|Virginia|In service|http://service.us-east-1.maxcompute.aliyun-inc.com/api|http://dt.us-east-1.maxcompute.aliyun-inc.com|
    |ap-south-1|Mumbai|In service|http://service.ap-south-1.maxcompute.aliyun-inc.com/api|http://dt.ap-south-1.maxcompute.aliyun-inc.com|

-   **VPC MaxCompute activation and service connections by region**

    In a VPC, you can only access MaxCompute using the Endpoint and Tunnel Endpoint as follows.

    |English region name|Location|Service status|VPC endpoint|VPC tunnel endpoint|
    |:------------------|:-------|:-------------|:-----------|:------------------|
    |East China 1|Hangzhou|In service|http://service.cn.maxcompute.aliyun-inc.com/api|http://dt.cn-hangzhou.maxcompute.aliyun-inc.com|
    |East China 2|Shanghai|In service|http://service.cn.maxcompute.aliyun-inc.com/api|http://dt.cn-shanghai.maxcompute.aliyun-inc.com|
    |North China 2|Beijing|In service|http://service.cn.maxcompute.aliyun-inc.com/api|http://dt.cn-beijing.maxcompute.aliyun-inc.com|
    |South China 1|Shenzhen|In service|http://service.cn.maxcompute.aliyun-inc.com/api|http://dt.cn-shenzhen.maxcompute.aliyun-inc.com|
    |Hong Kong|Hong Kong|In service|http://service.cn-hongkong.maxcompute.aliyun-inc.com/api|http://dt.cn-hongkong.maxcompute.aliyun-inc.com|
    |ap-southeast-1|Singapore|In service|http://service.ap-southeast-1.maxcompute.aliyun-inc.com/api|http://dt.ap-southeast-1.maxcompute.aliyun-inc.com|
    |ap-southeast-2|Australia|In service|http://service.ap-southeast-2.maxcompute.aliyun-inc.com/api|http://dt.ap-southeast-2.maxcompute.aliyun-inc.com|
    |ap-southeast-3|Kuala Lumpur|In service|http://service.ap-southeast-3.maxcompute.aliyun-inc.com/api|http://dt.ap-southeast-3.maxcompute.aliyun-inc.com|
    |ap-southeast-5|Jakarta|In service|http://service.ap-southeast-5.maxcompute.aliyun-inc.com/api|http://dt.ap-southeast-5.maxcompute.aliyun-inc.com|
    |ap-northeast-1|Japan|In service|http://service.ap-northeast-1.maxcompute.aliyun-inc.com/api|http://dt.ap-northeast-1.maxcompute.aliyun-inc.com|
    |eu-central-1|Frankfurt|In service|http://service.eu-central-1.maxcompute.aliyun-inc.com/api|http://dt.eu-central-1.maxcompute.aliyun-inc.com|
    |us-west-1|Silicon Valley|In service|http://service.us-west-1.maxcompute.aliyun-inc.com/api|http://dt.us-west-1.maxcompute.aliyun-inc.com|
    |us-east-1|Virginia|In service|http://service.us-east-1.maxcompute.aliyun-inc.com/api|http://dt.us-east-1.maxcompute.aliyun-inc.com|
    |ap-south-1|Mumbai|In service|http://service.ap-south-1.maxcompute.aliyun-inc.com/api|http://dt.ap-south-1.maxcompute.aliyun-inc.com|


## Access concepts { .section}

-   In regions where the service is available, you can connect to the MaxCompute service through the Internet, classic network, or VPC.
-   Fees are calculated for the data downloaded through the configured Internet tunnel endpoint, at a rate of 0.1166 USD/GB.

