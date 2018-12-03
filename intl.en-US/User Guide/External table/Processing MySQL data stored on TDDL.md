# Processing MySQL data stored on TDDL {#concept_atn_kp4_hfb .concept}

This paper introduces how to process MySQL data on middleware database by creating external tables, reading external tables and writing data to external tables.

## Overview of processing MySQL data stored on TDDL {#section_kg1_zp4_hfb .section}

[TDDL](http://gitlab.alibaba-inc.com/middleware/tddl5-wiki/wikis/home) is a widely used database middleware within the group. By encapsulating MySQL, TDDL provides data segmentation, separation of reading and writing, switching between master and standby, etc. The major use of MySQL within the group is to connect to the database through tddl's middleware. At the same time, tddl provides a Corona connection mode. Corona is a proxy of MySql, which provides standard MySQL protocol and can interact through jdbc.

MaxCompute currently supports connection to MySQL data for the group middleware tddl. Mainly through the development of built-in storageHandler, Hadoop provides the original org.apache.hadoop.mapreduce.lib.db.xxxx interface, the bottom of the mysql-jdbc for data communication, to achieve the MaxCompute and MySQL data reading and writing functions.

**Note:** 

The operation of CRUD is currently only supported

-   The appearance of MySQL is built in MaxCompute to read data from the appearance.
-   Write data from MaxCompute to the surface \(append\).

## Pre-Description {#section_g22_rq4_hfb .section}

MaxCompute's new generation of 2.0 computing engine and framework is still in the process of gray release. Many functions are not turned on by default, so to use the newly introduced unstructured data processing framework, you need to open some settings:

```
set odps.sql.hive.compatible=true; -- needs to be set for both ddls and DML related to tddl appearances
set odps.sql.udf.java.retain.legacy=false; -- required for both ddls and DML related to tddl appearances
set odps.sql.jdbc.splits.num=3; -- sets the number of instance shares for which MaxCompute reads MySQL. Maximum must not exceed 256, default is 1. You need to set up when you select a tddl appearance. 
set odps.sql.jdbc.reducer.num=3; -- sets the number of instances that maxcompute writes to MySQL concurrent. The maximum cannot exceed 256, the default is 64. If the generated execution plan concurrent instance is smaller than this value, no change is made. You need to set up when you insert a tddl appearance.
set odps.sql.hive.compatible=true; -- use the interfaces implemented by the open source community to get and parse MySQL's data format. You need to set up both the DDL and the DML associated with the tddl appearance.
Set odps.sql.type.system.odps2 = true; new data types (TINYINT, SMALLINT, INT, FLOAT, VARCHAR, TIMESTAMP BINARY) need to be set in SQL (Create, select, insert, etc.).
```

## Creating an External table {#section_fs5_tq4_hfb .section}

The MaxCompute unstructured data framework provides the connection between MaxCompute and various data through the concept of EXTERNAL TABLE. Similar to the method of [reading OSS data](reseller.en-US/User Guide/External table/Access OSS unstructured data.md#), the OSS data is written by first creating an external table through the CREATE EXTERNAL TABLE statement. When reading the open source data format, the format of the DDL statement that creates the outer table is as follows:

```
-- Note set related set statements
DROP TABLE [IF EXISTS] <external_table_name>;
CREATE EXTERNAL TABLE [IF NOT EXISTS] <external_table_name>
(<column schemas>)
STORED BY 'com.aliyun.odps.jdbc.JdbcStorageHandler'
Location 'jdbc: mysql: // path_format'
TBLPROPERTIES(
  ...
);
```

## Data Types supported by column schema {#section_nxp_mr4_hfb .section}

|MySQL Type Name|MaxCompute type name|
|:--------------|:-------------------|
|tinyint \(unsigned\)|tinyint|
|smallint \(unsigned\)|smallint|
|int \(unsigned\)|int|
|bigint \(unsigned\)|bigint|
|boolean|boolean|
|float|float|
|double|double|
|varchar|varchar|
|text|String|
|Date|Date|
|Datetime|datetime|
|Decimal|Decimal \(x, y\) \(default precision is \(10, 0 \), precision overflow will report error\)|

**Note:** The MaxCompute base type does not have an unsigned type. Therefore, there is a loss of precision in the support for the unsigned type, which is divided into the following two situations.

-   Set `setproject odps.sql.udf.strict.mode=false;` \(that is, in non-strict mode, the settings need to be displayed\)
    -   For reading the external table: If unsigned is converted to signed without precision loss, the data is read normally and converted. If there is a loss of precision, the read data is null.
    -   For writing to the external table: MaxCompute does not do type checking. Users can make mysql produce the expected behavior by setting mysql sql\_mode.

-   `STORED BY`

    Currently, only the built-in StorageHandler is supported. The table field type of tddl needs to correspond to the data type supported by the column scheme in the previous section.

-   Location format

    Three types of LOCATION formats are supported, as follows:

    -   To connect to a MySQL database location as a JDBC connection string: `jdbc:mysql://<user>:<password>@<host>/<databaseName>? useSSL=false&table=<tableName>`
        -   User/password is the user name and password of the JDBC connection string for MySQL.
        -   Host is the network address of the MySQL instance.
        -   DatabaseName is the name of the data for MySQL.
        -   Tablename is the table name of the MySQL that corresponds to this external table.
    -   Connect to the database of tddl in the form of a Corona, And the location format is the same as the first point.

        **Note:** The current production environment, Corona direct link, needs to have vipserver installed on the corresponding cluster. Please consult @winter for specific use.

    -   Connect to the database location of tddl by app name: `jdbc:mysql://dummy_host? table=<tableName>`
        -   Tablename is the table name of the MySQL that corresponds to this appearance.
        -   `odps.federation.jdbc.tddl.appname` must be specified in TBLPROPERTIES.
        -   For a internal cloud network environment，you must specify `odps.federation.jdbc.tddl.app.access.key`.
        -   For a internal cloud network environment，you must specify `odps.federation.jdbc.tddl.app.access.key`.
        -   Due to the middleware hardening project, it is currently accessible without specifying access.key and secret.key.
    **Note:** 

    -   In the first location way, MaxCompute interacting with the database as a direct connection to JDBC. You need to write your username and password in clear text, which poses a security risk. Although MaxCompute already supports hiding username and password in logview or desc extended table, for risk control reasons, it is recommended that you use a separate ddl statement to create the appearance in advance and then use it.

        For example, a project member with a higher authority separately establishes a foreign appearance to be used in MaxCompute, and a project member with a lower authority directly uses the appearance. This avoids the low-privileged project members from accessing the plaintext username and password, and avoids plaintext passwords in the sql script.

    -   In the third location, MaxCompute interacts with the database in the form of a tddl sdk connection. This usage requires specifying `odps.federation.jdbc.tddl.appname` in tableProperties.
        -   Tddl sdk permission verification \(offline/test environment\): Just specify appname.
        -   Tddl sdk privilege verification \(online/in-ball environment\): ak and sk need to be specified, ak/sk is the security credential of the application authorized by tddl. The way to obtain the security credentials is to access the [link](https://dauth-portal.alibaba.net/). For details, see the [link](http://docs.idb.alibaba-inc.com/manual/db_tddl_app.html).

            See [link 1](http://gitlab.alibaba-inc.com/middleware/1111/wikis/safe), [link 2](http://gitlab.alibaba-inc.com/middleware/1111/wikis/platform_tddl_security) for specific procedures and principles of authorization.

            Since the middleware hardening is currently in the excessive phase of the old version upgrade and compatibility, the current authentication does not specify ak/sk, and the middleware reinforcement team will do the unified closing. If there is no ak, the sk will fail the authentication.

-   TBLPROPERTIES
    -   `odps.federation.jdbc.condition`: 从mysql获取数据的时候，会添加该筛选条件。 Set the difference between `odps.federation.jdbc.condition` and `select * from text_test_jdbc_write_external where condition`: Assuming there are 100 rows of data in the mysql table, the condition condition can filter out 10 pieces of data. If you set it on mysql by setting `odps.federation.jdbc.condition`, there are only 10 data read by MaxCompute through the external table. If you filter by

        ```
        select * from text_test_jdbc_write_external where
                  condition
        ```

        , and MaxCompute will read 100 pieces of data from mysql and then filter out 10 pieces of data at MaxCompute's runtime.

    -   `Odps.federation.jdbc.colmapping` allows mapping of column names through attributes. For example,

        ```
        -- mysql schema:  mysqlId int
          -- MaxCompute create table
          CREATE EXTERNAL TABLE if not exists table_external
          (
          odpsId1 int,
          odpsId2 int
          )
          STORED BY ...
          location ... 
          TBLPROPERTIES('odps.federation.jdbc.colmapping'='odpsId1:mysqlId, odpsId2:mysqlId');
        ```

    -   `odps.federation.jdbc.insert.type`: type when writing data to MySQL. Only the following three categories are supported `[simplesetter, maid, replacement into]`. If not set, the default is simplesetter.

        MaxCopute's insert statement will be parsed into the following three types of sql statements to update the database:

        ```
        insert into sqlTable xxx values xxx;
          insert into sqlTable xxx values xxx on duplicate key update col1=values(col1), col2=values(col2);
          replace into sqlTable xxx values xxx;
        ```

    -   `odps.federation.jdbc.tddl.app.access.key`: access key for authorized aone application.
    -   `odps.federation.jdbc.tddl.app.access.key`: Secret Key of authorized aone app.
    -   `odps.federation.jdbc.tddl.appname`: Application name configured by tddl. Note: if this value is set, ODPS then connects to the MySQL database using the appname in the form of the tddl SDK. For detailed use of the tddl SDK, see tddl wiki.
-   Create representation example

    If the database of tddl is connected in the form of an app name appname = ODPS\_TDDL\_TEST\_APP，tablename=odps\_federation\_localrun\_write:

    ```
    -- Note set related set statements
    drop table if exists text_test_jdbc_external;
    CREATE EXTERNAL TABLE if not exists text_test_jdbc_external
    (
      colmapping tinyint, --c_tinyint tinyint,
      C_smallint smallint,
      c_int int,
      c_bigint bigint,
      c_utinyint tinyint,
      c_usmallint smallint,
      c_uint int,
      c_ubigint bigint,
      c_boolean tinyint,
      -- C_float float, -- In tddl, not recommend float and double type as it may lost precision
      --c_double double,
      c_string string,
      c_datetime datetime,
      c_decimal decimal
    )
    STORED BY 'com.aliyun.odps.jdbc.JdbcStorageHandler'
    Location 'jdbc: mysql: // dummy_host? table=odps_federation_localrun_write'
    TBLPROPERTIES(
    'odps.federation.jdbc.insert.type'='simpleInsert',
    'odps.federation.jdbc.condition'='c_boolean = 1 and c_int is not null and c_utinyint=127',
    'odps.federation.jdbc.colmapping'='colmapping:c_tinyint',
    'ODPS. Federation. JDBC. tddl. appname' =' ODPS _ Porter ',
    'odps.federation.jdbc.tddl.app.access.key'='your tddl app access key',
    'odps.federation.jdbc.tddl.app.secret.key'='your tddl app secret key');
    ```


## Read External tables {#section_x3t_rs4_hfb .section}

The External table data associated with MySQL is imported into MaxCompute.

-   Create a corresponding internal table in MaxCompute.

    ```
    CREATE TABLE if not exists text_test_jdbc_max_compute
    (
      c_tinyint tinyint,
      c_smallint smallint,
      c_int int,
      c_bigint bigint,
      C_utinyint tinyint,
      c_usmallint smallint,
      c_uint int,
      c_ubigint bigint,
      c_boolean tinyint,
      --c_float float,
      --c_double double,
      c_string string,
      C_datetime datetime,
      c_decimal decimal
    );
    ```

-   The data is imported into MaxCompute.

    ```
    -- Note set related set statements
    insert OVERWRITE TABLE text_test_jdbc_odps select * from text_test_jdbc_read_external;
    ```


For complex operations, such as group join, it is recommended that you import the data for the external table as a table for MaxCompute, and then do the operation again. In the case of data being imported into the system in this way, the same data is calculated to be much more efficient.

Establish the relationship between the external table and the data imported into the MaxCompute table

-   Building a external table can be understood as creating a data channel for MaxCompute and mysql,MaxCompute does not store any data for mysql. If MySQL Data is lost, the data is lost.
-   After the data is imported into the MaxCompute table, MaxCompute actually stores the data in MySQL. When MySQL Data is lost, you can retrieve the data by importing the data's MaxCompute table.

## Write out \(append\) {#section_a1b_ws4_hfb .section}

To ensure that the data is written correctly, when creating an appearance, the column name and Data Type of the mapped column need to be consistent with the scheme of the database. The behavior corresponding to the loss of precision on the type, refer to the attention item for the data type supported above.

```
-- Note set related set statements
insert INTO TABLE  text_test_jdbc_external select * from text_test_jdbc_max_compute;
```

Differences between `insert into mysql-external-table` and `insert override mysql-external-table`:

-   There is no difference between the two syntax for writing MySQL external table, which is actually append to the outside.
-   You can set the Insert Type through the `odps.federation.jdbc.insert.type` property. Refer specifically to the property descriptions in the "TBLPROPERTIES.

**Note:** The above description is only for the case of mysql external table. For MaxCompute table, the above syntax runs according to its own semantics.

