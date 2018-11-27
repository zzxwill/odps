# Partition {#concept_qlm_lz1_5db .concept}

To improve MaxCompute’s processing efficiency, you can specify a partition when creating a table. Specifically, several fields in the table can be specified as partition columns. A partition is comparable in terms of functionality to a directory under a file system.

In MaxCompute, each field can be specified as a separate partition. Alternatively, you can specify multiple fields of the table as a partition whereby they function similarly to multi-level directories.

If the partitions to be accessed are specified when you use data, then only corresponding partitions are read and a full table scan is avoided, improving processing efficiency while reducing costs.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/11922/15432891751036_en-US.png)

## Partition types {#section_rbw_nz1_5db .section}

MaxCompute 2.0 extends the support for partitioning types, currently, MaxCompute supports tinyint, smallint, Int, bigint, varchar, and string partition types.

**Note:** In MaxCompute versions earlier than 2.0, only STRING partition type is supported. For historical reasons, although you can specify a partition type of bigint, however, except for the schema representation of the table as bigint, any other case is actually handled as a string. Examples:

```
create table parttest (a bigint) partitioned by (pt bigint);
insert into parttest partition(pt) select 1, 2 from dual;
insert into parttest partition(pt) select 1, 10 from dual;
select * from parttest where pt >= 2;
```

After the execution, the returned result is only one line, because 10 was treated as a STRING and compared with 2, meaning no result can be returned.

## Restrictions {#section_vbw_nz1_5db .section}

When using a partition, the following restrictions apply:

-   The maximum number of partition levels for a single table is 6 levels.
-   The maximum number of single table partitions is 60,000.
-   The maximum number of query partitions for a query is 10,000.

Examples:

```
-- create a two-level partition table with the date as the level one partition and the region as the level two partition
create table src (key string, value bigint) partitioned by (pt string,region string);
```

When querying, use the partition column as a filter condition in the WHERE condition filter:

```
select * from src where pt='20170601' and region='hangzhou';  -- This example is the correct method of using WHERE conditional filter. When MaxCompute generates a query plan, only data of the region 'hangzhou' under the '20170601' partition is accessed.
select * from src where pt = 20170601;   -- This example is an incorrect method of using the WHERE conditional filter. In this example, the effectiveness of the partition filter cannot be guaranteed. pt is a STRING type. When the STRING type is compared with BIGINT type (20170601), MaxCompute converts both to DOUBLE type, and loss of precision occurs.
```

Some SQL operations on the partitions are less efficient and may cause higher billing, for example, [using dynamic partition](../../../../reseller.en-US/User Guide/SQL/Insert Operation/DYNAMIC PARTITION.md#).

For some of the operation commands for MaxCompute, there is a difference in the syntax when you process the partition and non-partition tables, for more information, see the [DDL statement](../../../../reseller.en-US/User Guide/SQL/DDL SQL/Column__Partition operation.md#) and using dynamic partitioning.

