# Visit Table Store Data {#concept_dgw_n2b_wdb .concept}

Table Store is a NoSQL database service that built on Alibaba Cloud’s Apsara distributed file system, enabling you to store and access massive volumes of structured data in real time. For more information about Table Store, see [What is Table Store](https://www.alibabacloud.com/help/doc-detail/27280.html).

MaxCompute and Table Store are two independent big data computing and storage services. Therefore, these two services must ensure that the network between them is open.  When MaxCompute’s public cloud service accesses data stored in Table Store, we recommend that you use Table Store’s **private network** address, usually a host name suffixed ‘ots-internal.aliyuncs.com’, for example tablestore://odps-ots-dev.cn-shanghai.ots-internal.aliyuncs.com.

This document introduces how to [access OSS](intl.en-US/User Guide/Handle-Unstructured-data/Access OSS Data.md#) to import data from Table Store to the MaxCompute computing environment. This allows seamless connections between multiple data sources.

Both TableStore and MaxCompute have their own type systems. Both Table Store and MaxCompute have their own data type systems. When you process Table Store data in MaxCompute, the data type associations are as follow:

|MaxCompute Type|TableStore Type  |
|:--------------|:----------------|
|STRING|STRING|
|BIGINT|INTEGER|
|DOUBLE|Double|
|BOOLEAN|Boolean|
|BINARY|BINARY|

## Authorization with STS Mode {#section_spx_rrb_wdb .section}

To access Table Store data, MaxCompute requires a secure authorization channel.  To address this issue, MaxCompute integrates Alibaba Cloud Resource Access Management \(RAM\) and Token Service \(STS\) to implement secure data access.

You can authorize permissions in the following two ways:

-   When the MaxCompute and Table Store owner are the same account, you can directly log on with the Alibaba Cloud account and click [here](https://ram.console.aliyun.com/?spm=5176.100239.blogcont281191.24.uJg9dR#/role/authorize?request=%7B%22Requests%22:%20%7B%22request1%22:%20%7B%22RoleName%22:%20%22AliyunODPSDefaultRole%22,%20%22TemplateId%22:%20%22DefaultRole%22%7D%7D,%20%22ReturnUrl%22:%20%22https:%2F%2Fram.console.aliyun.com%2F%22,%20%22Service%22:%20%22ODPS%22%7D) to complete authorization.
-   Custom authorization.
    1.  Firstly, you must grant Table Store access permission to MaxCompute in the RAM console. 

        Log on to the  [RAM console](https://account.alibabacloud.com/login/login.html) \(if MaxCompute and Table Store are not the same account, you must log on with the Table Store account to authorize\), and create the role AliyunODPSDefaultRole.

    2.  Set its policy content as follows:

        ```
        --if MaxCompute and Table Store are same account  
        {
        "Statement": [
        {
         "Action": "sts:AssumeRole",
         "Effect": "Allow",
         "Principal": {
           "Service": [
             "odps.aliyuncs.com"
           ]
         }
        }
        ],
        "Version": "1"
        }
        --if MaxCompute and Table Store are not the same account
        {
        "Statement": [
        {
         "Action": "sts:AssumeRole",
         "Effect": "Allow",
         "Principal": {
           "Service": [
             "MaxCompute's Owner cloud account UID@odps.aliyuncs.com"
           ]
         }
        }
        ],
        "Version": "1"
        }
        ```

        **Note:** On the upper-right corner, click the avatar to open the Billing Management page, and then check the account UID.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/12076/15336071072844_en-US.png)

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/12076/15336071072845_en-US.jpg)

    3.  Edit this role’s authorization policy AliyunODPSRolePolicy:

        ```
        {
        "Version": "1",
        "Statement": [
        {
         "Action": [
           "ots:ListTable",
           "ots:DescribeTable",
           "ots:GetRow",
           "ots:PutRow",
           "ots:UpdateRow",
           "ots:DeleteRow",
           "ots:GetRange",
           "ots:BatchGetRow",
           "ots:BatchWriteRow",
           "ots:ComputeSplitPointsBySize"
         ],
         "Resource": "*",
         "Effect": "Allow"
        }
        ]
        }
        --You can also customize other permissions
        ```

    4.  Grant the permission AliyunODPSRolePolicy to this role.

## create an external table {#section_dh2_ksb_wdb .section}

In MaxCompute, after creating an external table and introducing the Table Store table data descriptions to the MaxCompute meta system, you can process Table Store data.  The following example demonstrates the concept and practice that used in MaxCompute’s Table Store access.

Use following statements to create an external table:

```
DROP TABLE IF EXISTS ots_table_external;
CREATE EXTERNAL TABLE IF NOT EXISTS ots_table_external
(
odps_orderkey bigint,
odps_orderdate string,
odps_custkey bigint,
odps_orderstatus string,
odps_totalprice double
)
STORED BY 'com.aliyun.odps.TableStoreStorageHandler' -- (1)
WITH SERDEPROPERTIES ( -- (2)
'tablestore.columns.mapping'=':o_orderkey,:o_orderdate,o_custkey, o_orderstatus,o_totalprice', -- ①
'tablestore.table.name'='ots_tpch_orders' -- ②
'odps.properties.rolearn'='acs:ram::xxxxx:role/aliyunodpsdefaultrole' --③
)
LOCATION 'tablestore://odps-ots-dev.cn-shanghai.ots-internal.aliyuncs.com'; -- （3）
```

The statement is described as follows：

\(1\) com.aliyun.odps.TableStoreStorageHandler is a storagehandler built into maxcompute that handles the tablestore data, which defines the interaction between maxcompute and tablestore, the correlation logic is implemented by maxcompute.

\(2\) SERDEPROPERITES is an interface that provides Parameter options, and when you use, there are two options that must be specified, each of which is the tablestore described below.columns.mapping, tablestore.table.name and odps.properties.rolearn.

①tablestore.columns.mapping option: required to describe the columns of the table store table that maxcompute is going to access, includes primary key and attribute columns.

-   At the beginning of the column name, `:` indicates a Table Store primary key. In this example`:o_orderkey` and `:o_orderdate`  are primary key columns and all others are attribute columns.
-   Table Store supports up to 4 primary keys. Primary keys support the STRING, INTEGER, and BINARY data types. The first primary key is the partition key.
-   When specifying a mapping relationship, you must provide all the primary keys of the specified Table Store table, but you do not have to provide all attribute columns, only the attribute columns you must access by using MaxCompute.

  ②tablestore.table.name：the name of the table store table that needs to be accessed. If you specify an incorrect Table Store table name \(such as a table that does not exist\), the system reports an error. MaxCompute does not create a new Table Store table with the specified name.

③odps.properties.rolearn中的信息是RAM中AliyunODPSDefaultRole的Arn信息。 You can get it through the **details of the role** in the RAM console.

\(3\) LOCATION clause: lets you specify specific information such as the table storeinstance name, endpoint, and so on. Because you must specify the AccessKey the of OSS owner, to avoid disclosing the AccessKey of your primary account, we recommend that you use RAM user credentials.

If you want to view the created external table structure, run the following statement:

```
desc extended <table_name>;

```

In the returned information, “Extended Info” contains external tables information such as StorageHandler and Location.

## Access Table Data by Using an External Table {#section_dcl_ssb_wdb .section}

After creating an external table, you can introduce Table Store data to the MaxCompute ecosystem.There, you can use MaxCompute SQL syntax to access Table Store data as follows:

```
SELECT odps_orderkey, odps_orderdate, SUM(odps_totalprice) AS sum_total
FROM ots_table_external
WHERE odps_orderkey > 5000 AND odps_orderkey < 7000 AND odps_orderdate >= '1996-05-03' AND odps_orderdate < '1997-05-01'
GROUP BY odps_orderkey, odps_orderdate
HAVING sum_total> 400000.0;
```

When using the MaxCompute SQL syntax, all of the accessed Table Store details are processed in MaxCompute.  This includes column name selection. For example, the column names used in the preceding SQL statements \(such as odps\_orderkey and odps\_totalprice\) are not the original primary key names \(o\_orderkey\) or attribute column names \(o\_totalprice\) used in Table Store. This is because mapping was already performed in the DDL statement used to create the external table.  Certainly, you can retain the original Table Store primary key/column names when creating the external table.

If you perform **multiple computations** on a single data set, instead of remotely reading data from Table Store each time, you can import all the necessary data to MaxCompute, to create a MaxCompute \(internal\) table. For example:

```
CREATE TABLE internal_orders AS
SELECT odps_orderkey, odps_orderdate, odps_custkey, odps_totalprice
FROM ots_table_external
WHERE odps_orderkey > 5000 ; 
```

Currently, internal\_orders is a MaxCompute table, with all features of a MaxCompute internal table, including an efficiently compressed column storage data format and complete internal macro data, and statistics information. Furthermore, because the data is stored in MaxCompute, the access speed is faster than when accessing external Table Store data. This is especially suitable for hotspot data that is frequently computed.

## Export MaxCompute Data to TableStore {#section_gy2_xsb_wdb .section}

**Note:** MaxCompute does not directly create external Table Store tables.Therefore, before outputting data to a Table Store table, you must make sure this table has already been created \(or the system reports an error\).

In the preceding operations, the external table ots\_table\_external has been created to connect MaxCompute with the Table Store table ots\_tpch\_orders, and data has been stored in the internal MaxCompute table internal\_orders. Now you can write the processed data from internal\_orders back to Table Store, perform the **INSERT OVERWITE TABLE** operation on the external table as follows:

```
INSERT OVERWRITE TABLE ots_table_external
SELECT odps_orderkey, odps_orderdate, odps_custkey, CONCAT(odps_custkey, 'SHIPPED'), CEIL(odps_totalprice)
FROM internal_orders;
```

Because Table Store is a KV data NoSQL storage medium, the data output from MaxCompute only affects the rows with the corresponding primary keys. In this example, the output only affects data in rows with corresponding dps\_orderkey + odps\_orderdate primary key values.  In addition, in the Table Store rows, only the attribute columns specified during external table \(ots\_table\_external\) creation are updated. Data columns that do not appear in the external table are not modified.

