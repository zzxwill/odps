# Create/View/Delete a table {#concept_rkk_kcy_5db .concept}

You can use MaxCompute services once they are added to a project, and granted the corresponding privileges. Because the operation objects of MaxCompute \(input and output\) are performed on tables, you must create tables and partitions before processing data.

You can create or delete tables using the following methods:

-   MaxCompute Studio. For more information, see Visualization of operating the tables. see [Visualization of operating the tables](../../../../intl.en-US/Tools and Downloads/MaxCompute Studio/Manage data and resources/Visualization of operating the tables.md).
-   With dataworks, see [creating tables](https://www.alibabacloud.com/help/doc-detail/30291.html) and [deleting tables](https://www.alibabacloud.com/help/doc-detail/30290.html) for details.
-   Common client commands.

The following section explains how to create, view, and delete tables using commands through the DTplus console. For more information about console installation, see [Console](../../../../intl.en-US/Prepare/Install and configure a client.md).

## Create a table {#section_lz3_tcy_5db .section}

The command format is shown as follows.

```
CREATE TABLE [IF NOT EXISTS] table_name 
[(col_name data_type [COMMENT col_comment], ...)] 
[COMMENT table_comment] 
[PARTITIONED BY (col_name data_type [COMMENT col_comment], ...)] 
[LIFECYCLE days] 
[As select_statement]
CREATE TABLE [IF NOT EXISTS] table_name 
LIKE existing_table_name
```

Command descriptions:

-   The table name and column name are both case insensitive.
-   If you do not specify IF NOT EXISTS when creating a table and a table with the same name exists, an error is returned. If the option is specified then all returns are successful, regardless of whether there are tables with the same name, and regardless of whether the source table structure and the target table structure are inconsistent. The Meta information of the existing table does not change.
-   Only the [data types](../../../../intl.en-US/Product Introduction/Definition/Data types.md) BIGINT, DOUBLE, BOOLEAN, DATETIME, and STRING are supported.
-   A table name and column name obey the same naming conventions as follows: The name can be up to 128 bytes in length and can contain letters, numbers, and underscores ‘\_’.
-   Partitioned Partitioned by: Use PARTITIONED BY to specify the partition. Only String is supported. The value can be up to 128 bytes in length and can contain letters, numbers, and the special characters space ‘ ’, colon \(‘:’\), underscore \(‘\_’\), dollar sign \(’$’\), hash sign \(’\#’\), dot \(‘.’\), exclamation point \(‘!’\) and at symbol \(‘@’\). Other characters are considered as undefined characters, such as \(‘\\t’\), \(‘\\n’\), and \(‘/’\) . If you are using partition fields in the partition table, a full table scan is not needed when adding partitions, or when updating data in the partition and then reading the partition.
-   A comment must be a valid string within 1024 bytes.
-   Lifecycle indicates the lifecycle of the table. The unit is days. `The statement CREATE TABLE Like` does not copy the lifecycle attribute from source table.
-   Currently, the partition hierarchy cannot exceed 6 levels. In a project, the maximum partition number of a table can be configured. The maximum number of tables is 60,000.

    **Note:** 

    -   For more information about creating a table, see[Table Operations](../../../../intl.en-US/User Guide/SQL/DDL SQL/Table Operations.md#).
    -   For more information about the partition operation, see [Add/Remove Partition](https://www.alibabacloud.com/help/doc-detail/73771.html).
    -   For more information about the lifecycle operation, see [Modify Lifecycle for a Table](../../../../intl.en-US/User Guide/SQL/DDL SQL/Lifecycle of table.md#).

The following example shows how to create a table:

```
create table test1 (key string); -- create a no-partition table. table name is test 1,  field name is key, data type is string. 
create table test2 (key bigint) partitioned by (pt string, ds string);  --Create a partition table. 
create table test3 (key boolean) partitioned by (pt string, ds string) lifecycle 100;  -- Create a table with lifecycle. 
create table test4 like test3;  -- Except for the lifecycle property, other properties of test3 (field type, partition type) are completely consistent with test4. 
create table test5 as select * from test2;  -- This operation will create test5, but the partition and lifecycle information will not be copied to the object table. 
-- This operation will copy the data of test2 to the table test5.If test2 has data, the test2 in this example is an empty table. Subsequent chapters will introduce data import.
```

In the preceding example, an instance is used to create a table.

Create a table named user that includes the following information:

-   user\_id: Bigint type, user identifier, to identify a user.
-   gender: Bigint type, sex \(0, unknown; 1, male; 2, female\).
-   age: Bigint, the age of a user.

It must be partitioned by region and dt and the lifecycle is 365 days.

An example of table creation is as follows:

```
CREATE TABLE user 
( user_id BIGINT, gender BIGINT COMMENT '0 unknow,1 male, 2 Female', age BIGINT) 
PARTITIONED BY (region string, dt string) LIFECYCLE 365;
```

## Add a partition {#section_gn3_j2y_5db .section}

After creating a partition table, in order to import data into different partitions, a partition must be created. The statement format is as follows:

```
alter table table_name add [if not exists] partition partition_spec partition_spec: 
: (partition_col1 = partition_col_value1, partition_col2 = partiton_col_value2, ...)
```

In the preceding example, partitions must be added for the table user \(region is ‘hangzhou’ and dt is ‘20150923’\). The statement is as follows:

```
Alter table user add if not exists partition(region='hangzhou',dt='20150923');
```

## View a table {#section_ln3_j2y_5db .section}

View table information by using the following command: `desc <table_name>;`

For example, get information from test3: `desc test3;`

The results are as follows:

```
odps@ $odps_project>desc test3;
+------------------------------------------------------------------------------------+
| Owner: ALIYUN$maojing.mj@alibaba-inc.com | Project: $odps_project
| TableComment: |
+------------------------------------------------------------------------------------+
| CreateTime: 2015-09-18 12:26:57 |
| LastDDLTime: 2015-09-18 12:26:57 |
| LastModifiedTime: 2015-09-18 12:26:57 |
| Lifecycle: 100 |
+------------------------------------------------------------------------------------+
| InternalTable: YES | Size: 0 |
+------------------------------------------------------------------------------------+
| Native Columns: |
+------------------------------------------------------------------------------------+
| Field | Type | Label | Comment |
+------------------------------------------------------------------------------------+
| key | boolean | | |
+------------------------------------------------------------------------------------+
| Partition Columns: |
+------------------------------------------------------------------------------------+
| pt | string | |
| ds | string | |
+------------------------------------------------------------------------------------+
```

Get information from test4: `desc test4;`

```
odps@ $odps_project>desc test4;
+------------------------------------------------------------------------------------+
| Owner: ALIYUN$maojing.mj@alibaba-inc.com | Project: $odps_project
| TableComment: |
+------------------------------------------------------------------------------------+
| CreateTime: 2015-09-18 12:27:09 |
| LastDDLTime: 2015-09-18 12:27:09 |
| LastModifiedTime: 2015-09-18 12:27:09 |
+------------------------------------------------------------------------------------+
| InternalTable: YES | Size: 0 |
+------------------------------------------------------------------------------------+
| Native Columns: |
+------------------------------------------------------------------------------------+
| Field | Type | Label | Comment |
+------------------------------------------------------------------------------------+
| key | boolean | | |
+------------------------------------------------------------------------------------+
| Partition Columns: |
+------------------------------------------------------------------------------------+
| pt | string | |
| ds | string | |
+------------------------------------------------------------------------------------+
```

Except for the lifecycle property, other properties of test3 \(field type, partition type\) are completely consistent with test4. For more information about describing a table, see [Describe Table](../../../../intl.en-US/User Guide/SQL/DDL SQL/Table Operations.md).

When you view the information of test5, the ‘pt’ and ‘ds’ fields only exist as two common columns, rather than as the table partitions.

## Drop a partitio {#section_pp3_5fy_5db .section}

An example of how to drop a partition is as follows:

```
alter table table_name drop [if exists] partition_spec; partition_spec: 
: (partition_col1 = partition_col_value1, partition_col2 = partiton_col_value2, ...)
```

For example, to delete the partitions of region hangzhou and dt 20150923, the statement is as follows:

```
Alter table user drop if exists partition(region='hangzhou',dt='20150923');
```

## Delete a table {#section_up3_5fy_5db .section}

An example of how to drop a table is as follows:

```
DROP TABLE [IF EXISTS] table_name;
```

For example, to delete the table test2:

```
drop table test2;
```

For more information, see [Table Operations](../../../../intl.en-US/User Guide/SQL/DDL SQL/Table Operations.md#)Drop Table.

