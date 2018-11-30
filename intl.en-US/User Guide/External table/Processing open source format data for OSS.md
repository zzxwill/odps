# Processing open source format data for OSS {#concept_tlm_34v_ydb .concept}

This article will show you how to process various popular open source data formats \(ORC, PARQUET, SEQUENCEFILE, RCFILE, AVRO and TEXTFILE\) stored on OSS through unstructured frameworks in MaxCompute.

[Accessing the OSS unstructured data](intl.en-US/User Guide/External table/Accessing OSS unstructured data.md) shows you how to access the text stored on the OSS on MaxCompute, audio, image, and other format data. The non-structural framework directly calls the implementation of the open source community to parse the open source data format, and seamlessly with the MaxCompute system.

**Note:** Before processing the Open Source format data for OSS, it is necessary to authorize [STS mode for OSS](intl.en-US/User Guide/External table/OSS STS mode authorization.md).

## Create External Table {#section_bxs_hsv_ydb .section}

The MaxCompute unstructured data framework is associated with a variety of data through external table, external of Open Source format data associated with OSS

```
DROP TABLE [IF EXISTS] <external_table>;
CREATE EXTERNAL TABLE [IF NOT EXISTS] <external_table>
(<column schemas>)
[PARTITIONED BY (partition column schemas)]
[ROW FORMAT SERDE '<serde class>'
  [With serdeproperties ('ODPS. properties. rolearn '=' $ {roleran }'[, 'name2 '= 'value2',...]
]
STORED AS <file format>
LOCATION 'oss://${endpoint}/${bucket}/${userfilePath}/';
```

**Note:** The syntax format is quite similar to hive's syntax, but the following issues need to be noted:

-   STORED AS keyword, which is not STORED BY keyword used for ordinary unstructured appearance in this grammar format, is unique in reading open source compatible data at present.

    STORED AS is followed by file format names, such as ORC/PARQUET/RCFILE/SEQUENCEFILE/TEXTFILE.

-   The column schemas of the external tables must match the schema where the stored data is stored on the specific OSS.
-   ROW FORMAT SERDE option is not required, and is only available in a number of special formats, for example, textfile needs to be used.
-   When WITH SERDEPROPERTIES associates OSS privileges with [STS mode authorization](intl.en-US/User Guide/External table/OSS STS mode authorization.md), this parameter is required to specify the odps.properties.rolearn attribute, whose value is the Role Arn information specifically used in RAM.

    If you do not use STS mode, you do not need to specify this property to pass in the clear text `AccessKeyId` and the `AccessKeySecret` directly at location.

-   Location if you associate OSS with clear AK, write as follows:

    ```
    LOCATION 'oss://${accessKeyId}:${accessKeySecret}@${endpoint}/${bucket}/${userPath}/'
    ```

-   Accessing the OSS External tables is not currently supported with outer-network Endpoint.
-   Currently the STORE AS single file size cannot exceed 3G, split is recommended if the file is too large.

## Example of PARQUET data associated with OSS {#section_spx_ttv_ydb .section}

Assume that some parquet files are stored on an OSS path, and that each file is in parquet format, the schema is stored in 16 columns \(4 columns bigint, 4 columns double, and 8 columns string\) the data for the build table Div statement is as follows:

```
CREATE EXTERNAL TABLE tpch_lineitem_parquet
(
  Rochelle orderkey bigint,
  l_partkey bigint,
  l_suppkey bigint,
  Rochelle linenumber bigint,
  l_quantity double,
  l_extendedprice double,
  l_discount double,
  l_tax double,
  l_returnflag string,
  l_linestatus string,
  l_shipdate string,
  l_commitdate string,
  l_receiptdate string,
  l_shipinstruct string,
  l_shipmode string,
  _Comment string
)
STORED AS PARQUET
LOCATION 'oss://${accessKeyId}:${accessKeySecret}@oss-cn-hangzhou-zmf.aliyuncs.com/bucket/parquet_data/';
```

The default parquet data is not compressed, and if you need to compress parquet data on MaxCompute, you need set `set odps.sql.hive.compatible=true;`. The supported compression types are: SNAPPY, GZIP.

## Text data associated with OSS {#section_gvy_15v_ydb .section}

If the data is stored as TEXTFILE file on OSS in JSON format for each row and organized by multiple directories in OSS, then MaxCompute partition table and data association can be used. An example of DDL statement for partition table is shown below.

```
CREATE EXTERNAL TABLE tpch_lineitem_textfile
(
  l_orderkey bigint,
  l_partkey bigint,
  l_suppkey bigint,
  l_linenumber bigint,
  l_quantity double,
  l_extendedprice double,
  l_discount double,
  l_tax double,
  l_returnflag string,
  Maid string,
  l_shipdate string,
  Rochelle Commission string,
  l_receiptdate string,
  l_shipinstruct string,
  l_shipmode string,
  l_comment string
)
PARTITIONED BY (ds string)
ROW FORMAT serde 'org.apache.hive.hcatalog.data.JsonSerDe'
STORED AS TEXTFILE
Location 'oss: // $ {accesskeyid}: $ {accesskeysecret} @ fig /';
```

If the sub-directory under the OSS table directory is organized as Partition Name, the example is as follows.

```
oss://${accessKeyId}:${accessKeySecret}@oss-cn-hangzhou-zmf.aliyuncs.com/bucket/text_data/ds=20170102/'
oss://${accessKeyId}:${accessKeySecret}@oss-cn-hangzhou-zmf.aliyuncs.com/bucket/text_data/ds=20170103/'
...
```

You can ADD PARTITION using the following pant statement.

```
ALTER TABLE tpch_lineitem_textfile ADD PARTITION(ds="20170102");
ALTER TABLE tpch_lineitem_textfile ADD PARTITION(ds="20170103");
```

If the OSS partition directory is not organized in this way, or not in the table directory at all, the example is as follows:

```
oss://${accessKeyId}:${accessKeySecret}@oss-cn-hangzhou-zmf.aliyuncs.com/bucket/text_data_20170102/;
oss://${accessKeyId}:${accessKeySecret}@oss-cn-hangzhou-zmf.aliyuncs.com/bucket/text_data_20170103/;
...
```

In this case, you can use the following pant statement to ADD PARTITION.

```
ALTER TABLE tpch_lineitem_textfile ADD PARTITION(ds="20170102")
LOCATION 'oss://${accessKeyId}:${accessKeySecret}@oss-cn-hangzhou-zmf.aliyuncs.com/bucket/text_data_20170102/';
ALTER TABLE tpch_lineitem_textfile ADD PARTITION(ds="20170103")
LOCATION 'oss://${accessKeyId}:${accessKeySecret}@oss-cn-hangzhou-zmf.aliyuncs.com/bucket/text_data_20170103/';
...
```

Text data supports serdeproperties \(key: default\)

```

Fields terminator：'\001'
Escape delimitor：'\\'
Collection items terminator：'\002'
Map keys terminator：'\003'
Lines terminate: '\ N'
Null defination：'\\N'
```

## CSV data associated with OSS {#section_jq3_sgq_5fb .section}

The Tasmania statement format is as follows.

```

CREATE EXTERNAL TABLE [IF NOT EXISTS] 
(<column schemas>)
[PARTITIONED BY (partition column schemas)]
ROW FORMAT SERDE 'org.apache.hadoop.hive.serde2. OpenCSVSerde'
  WITH SERDEPROPERTIES
    ('Separates atorchare' =, ', 'pigeon techar' = '"', 'escarechar '= '\\')
STORED AS TEXTFILE
LOCATION 'oss://${endpoint}/${bucket}/${userfilePath}/';
```

As you can see from the above statement, the CSV data pant Statement supports serdeproperties \(key: default\)

```

separatorChar：','
quoteChar：'"'
Escarechar :'\'
```

**Note:** hive OpenCSVSerde only supports string types.

OpenCSVSerde does not currently belong to Builtin Serde. When DML statements are executed, you need to set odps.sql.hive.compatible = true.

## JSON data associated with OSS {#section_hsx_nhq_5fb .section}

The Tasmania statement format is as follows, and SERDEPROPERTIES is supported.

```


CREATE EXTERNAL TABLE [IF NOT EXISTS] 
(<column schemas>)
[PARTITIONED BY (partition column schemas)]
ROW FORMAT SERDE 'org.apache.hive.hcatalog.data.JsonSerDe'
STORED AS TEXTFILE
LOCATION 'oss://${endpoint}/${bucket}/${userfilePath}/';
```

## ORC data associated with OSS {#section_ycz_nhq_5fb .section}

The Tasmania statement format is as follows.

```

CREATE EXTERNAL TABLE [IF NOT EXISTS] 
(<column schemas>)
[PARTITIONED BY (partition column schemas)]
STORED AS ORC
LOCATION 'oss://${endpoint}/${bucket}/${userfilePath}/';
```

## AVRO data associated with OSS {#section_wh1_4hq_5fb .section}

The format of the DDL statement is as follows.

```

CREATE EXTERNAL TABLE [IF NOT EXISTS] 
(<column schemas>)
[PARTITIONED BY (partition column schemas)]
STORED AS AVRO
LOCATION 'oss://${endpoint}/${bucket}/${userfilePath}/';
```

## SEQUENCEFILE data associated with OSS {#section_cqs_b3q_5fb .section}

```

CREATE EXTERNAL TABLE [IF NOT EXISTS] 
(<column schemas>)
[PARTITIONED BY (partition column schemas)]
STORED AS SEQUENCEFILE
LOCATION 'oss://${endpoint}/${bucket}/${userfilePath}/';
```

## Read and process open source format data for OSS {#section_ewt_bvv_ydb .section}

Compare the two external representations created in the previous article, you can see that for different file types, Simply modify the format name after STORED AS. In the following example, only the processing of the appearance \(tpch\_lineitem\_parquet\) corresponding to the above PARQUET data will be described centrally. If you want to work with different file types, just specify parquet/ORC/TEXTFILE/RCFILE/TEXTFILE as long as you want to create the appearance when the DDL is created, the statement that processes the data is the same.

-   Read and process open source data directly from OSS

    After creating a data table to associate, you can directly do the same thing as a normal MaxCompute table, as shown below.

    ```
    SELECT l_returnflag, l_linestatus,
    SUM(l_extendedprice*(1-l_discount)) AS sum_disc_price,
    AVG(l_quantity) AS avg_qty,
    COUNT(*) AS count_order
    FROM tpch_lineitem_parquet
    WHERE l_shipdate <= '1998-09-02'
    Group by l_returnflag, l_linestatus;
    ```

    The appearance tpch\_lineitem\_parquet is used as a common internal table, except that the MaxCompute internal computing engine reads the corresponding PARQUET data directly from OSS for processing.

    The external partition table for the associated textfile that was created in the previous article, because`ROW FORMAT`+ `STORED AS` is used, you need to set flag manually \(Only use STORED AS, odps.sql.hive.compatible is FALSE by default.\) and then reads again, otherwise there will be an error.

    ```
    SELECT * FROM tpch_lineitem_textfile LIMIT 1;
    Failed: Maid: User Defined Function exception-traceback:
    com.aliyun.odps.udf.UDFException: java.lang.ClassNotFoundException: com.aliyun.odps.hive.wrapper.HiveStorageHandlerWrapper
    --You need to manually set up hive compatible flag.
    set odps.sql.hive.compatible=true;
    Select * from Maid limit 1;
    +------------+------------+------------+--------------+------------+-----------------+------------+------------+--------------+--------------+------------+--------------+---------------+----------------+------------+-----------+
    | l_orderkey | l_partkey  | l_suppkey  | l_linenumber | l_quantity | l_extendedprice | l_discount | l_tax      | l_returnflag | l_linestatus | l_shipdate | l_commitdate | l_receiptdate | l_shipinstruct | l_shipmode | l_comment |
    +------------+------------+------------+--------------+------------+-----------------+------------+------------+--------------+--------------+------------+--------------+---------------+----------------+------------+-----------+
    | 5640000001 | 174458698  | 9458733    | 1            | 14.0       | 23071.58        | 0.08       | 0.06       | N            | O            | 1998-01-26 | 1997-11-16   | 1998-02-18    | TAKE BACK RETURN | SHIP       | cuses nag silently. quick |
    +------------+------------+------------+--------------+------------+-----------------+------------+------------+--------------+--------------+------------+--------------+---------------+----------------+------------+-----------+
    ```

    **Note:** Direct use of the external table, each time reading data requires I/O operations involving external OSS, and the MaxCompute system itself does not use many high-performance optimizations for internal storage, so there will be a loss in performance. Therefore, if it is a scenario that requires repeated computation of data and is sensitive to the efficiency of computation, it is recommended to use the following usage: first import the data into MaxCompute and then calculate it.

    These complex data types are involved in SQL \(create, select, insert, etc.\). The statement `set odps. sql. type. system. odps2 = true;` should be added before the SQL statement, and the set statement and the SQL statement should be submitted together for execution at execution time. See for details [Data types](../../../../intl.en-US/User Guide/Definition/Data types.md#).

-   Importing the open source data from OSS into MaxCompute for Calculation

    First, create an internal table tpch\_lineitem\_internal, which is the same as the external table schema, and then import the open source data from OSS into the internal table of MaxCompute for storage in the internal data storage format of MaxCompute.

    ```
    CREATE TABLE tpch_lineitem_internal LIKE tpch_lineitem_parquet;
    INSERT OVERWRITE TABLE tpch_lineitem_internal;
    SELECT * FROM tpch_lineitem_parquet;
    ```

    Next take the same action directly to the internal table:

    ```
    SELECT l_returnflag, l_linestatus,
    SUM(l_extendedprice*(1-l_discount)) AS sum_disc_price,
    AVG(l_quantity) AS avg_qty,
    COUNT(*) AS count_order
    FROM tpch_lineitem_internal
    WHERE l_shipdate <= '1998-09-02'
    GROUP BY l_returnflag, l_linestatus;
    ```

    By doing so, you can pilot the data into the MaxCompute system for storage, computational processing of the same data will be more efficient.


