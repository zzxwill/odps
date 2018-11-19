# 处理存储在TDDL上的MySQL数据 {#concept_atn_kp4_hfb .concept}

本文介绍如何通过创建外部表、读取外部表、将数据外写入外部表来处理中间件数据库上的MySQL数据。

## 处理存储在TDDL上的Mysql数据概述 {#section_kg1_zp4_hfb .section}

[TDDL](http://gitlab.alibaba-inc.com/middleware/tddl5-wiki/wikis/home)是集团内部广泛使用的数据库中间件，通过对MySQL的封装，提供了分库分表等数据切分，读写分离，主备切换等特性。集团内部的mysql的使用大多都是通过tddl的中间件连接数据库。同时tddl提供了一个Corona的连接方式，Corona是一个MySql的proxy，提供标准的MySQL协议，可以通过jdbc的方式实现交互。

MaxCompute目前支持与集团中间件tddl的mysql数据连通。主要是通过开发内置的storageHandler，包装了Hadoop提供的原生的org.apache.hadoop.mapreduce.lib.db.xxx等接口，底层以mysql-jdbc做数据通信，实现了MaxCompute和MySQL的数据读取和写入等功能。

**说明：** 

对于CRUD的操作，目前只支持

-   在MaxCompute中建立MySQL的外表，从外表读取数据。
-   从MaxCompute向外表写入数据 （append）。

## 前置说明 {#section_g22_rq4_hfb .section}

MaxCompute新一代的2.0计算引擎与框架还在灰度上线的过程中，默认设置下许多功能没有打开，所以要使用新引进的非结构化数据处理框架，需要打开一些settings：

```
set odps.sql.hive.compatible=true;--对于tddl外表相关的DDL和DML都需要设置
set odps.sql.udf.java.retain.legacy=false;--对于tddl外表相关的DDL和DML都需要设置
set odps.sql.jdbc.splits.num=3; --设置MaxCompute读取mysql的实例splits数量. 最大不可超过256, 默认为1。对tddl外表进行select操作时需要设置。 
set odps.sql.jdbc.reducer.num=3; --设置MaxCompute写入mysql的实例并发数量.最大不可超过256，默认64。如果生成的执行计划并发实例比该值小，则不做任何变动。对tddl外表进行insert操作时需要设置。
set odps.sql.hive.compatible=true;--使用开源社区实现的接口获取并解析mysql的数据格式。对于tddl外表相关的DDL和DML都需要设置。
set odps.sql.type.system.odps2=true; --SQL（Create、select、insert等操作）中涉及到新数据类型（TINYINT、SMALLINT、 INT、 FLOAT、VARCHAR、TIMESTAMP BINARY）时需要设置。
```

## 创建EXTERNAL TABLE {#section_fs5_tq4_hfb .section}

MaxCompute非结构化数据框架通过EXTERNAL TABLE的概念来提供MaxCompute与各种数据的联通，与[读取OSS数据](cn.zh-CN/用户指南/外部表/访问OSS非结构化数据.md#)的使用方法类似，对OSS数据进行写操作，首先要通过CREATE EXTERNAL TABLE语句创建出一个外部表，而在读取开源数据格式时，创建外表的DDL语句格式如下：

```
--注意设置相关set语句
DROP TABLE [IF EXISTS] <external_table_name>;
CREATE EXTERNAL TABLE [IF NOT EXISTS] <external_table_name>
(<column schemas>)
STORED BY 'com.aliyun.odps.jdbc.JdbcStorageHandler'
location 'jdbc:mysql://path_format'
TBLPROPERTIES(
  ...
);
```

## column schema支持的数据类型 {#section_nxp_mr4_hfb .section}

|MySQL Type Name|MaxCompute type name|
|:--------------|:-------------------|
|tinyint \(unsigned\)|tinyint|
|smallint \(unsigned\)|smallint|
|int \(unsigned\)|int|
|bigint \(unsigned\)|bigint|
|boolean|boolean|
|float|float|
|double|double|
|Varchar|Varchar|
|text|String|
|Date|Date|
|Datetime|datetime|
|Decimal|Decimal\(x, y\) （默认精度为\(10, 0\), 精度溢出会报错）|

**说明：** MaxCompute基本类型没有unsigned类型。因此这里对unsigned类型的支持存在精度损失，具体分为下面两种情况

-   设置 `setproject odps.sql.udf.strict.mode=true;` \(即strict模式默认模式\)

    -   对于读取外表：若unsigned转为signed没有精度损失，则正常读取数据并转换。若有精度损失，则抛出 RuntimeException \(“value out of range”\)。
    -   对于写入外表：MaxCompute不做类型检查。用户可以通过设置mysql的 sql\_mode让mysql产生预期的行为。sql\_mode的设置及行为请参见[Server SQL Modes](https://dev.mysql.com/doc/refman/5.7/en/sql-mode.html#sqlmode_no_unsigned_subtraction)。
-   设置 `setproject odps.sql.udf.strict.mode=false;`\(即 非strict 模式 需要显示设置\)
    -   对于读取外表：若unsigned转为signed 没有精度损失，则正常读取数据并转换。若有精度损失，读取数据为NULL。
    -   对于写入外表：MaxCompute不做类型检查。用户可以通过设置mysql的 sql\_mode让mysql产生预期的行为。

-   `STORED BY`

    目前暂时只支持使用内置的StorageHandler，要求tddl的表字段类型需与前面小章节“column scheme支持的数据类型”对应。

-   location格式

    支持3种方式LOCATION格式，具体说明如下：

    -   以jdbc连接串的形式连接mysql数据库location: `jdbc:mysql://<user>:<password>@<host>/<databaseName>?useSSL=false&table=<tableName>`
        -   user/password 为 mysql的jdbc连接串的用户名和密码。
        -   host 为mysql实例的网络地址。
        -   databaseName 为mysql的数据名称。
        -   tableName 为该外表对应的mysql的表名称。
    -   以Corona的方式连接tddl的数据库，location格式同第一点。

        **说明：** 目前生产环境Corona直连需要在对应集群安装VipServer。具体使用请咨询@煦冬。

    -   以app name的方式连接tddl的数据库location：`jdbc:mysql://dummy_host?table=<tableName>`
        -   tableName为该外表对应的mysql的表名称。
        -   在TBLPROPERTIES中必须指定 `odps.federation.jdbc.tddl.appname`。
        -   对于弹内的网络环境必须指定 `odps.federation.jdbc.tddl.app.access.key`。
        -   对于弹内的网络环境必须指定 `odps.federation.jdbc.tddl.app.secret.key`。
        -   由于中间件加固的项目，因此目前可以不用指定access.key和secret.key也能访问。
    **说明：** 

    -   第一种location的方式，MaxCompute会以jdbc直连的方式与数据库进行交互。您需要明文写入用户名和密码，存在一定的安全风险。虽然MaxCompute已经支持了在logview或者desc extended table的时候会隐藏用户名密码，但出于风险控制的考虑，建议您使用单独的ddl语句提前创建外表，然后再做使用。

        比如：权限较高的项目成员单独在MaxCompute中建立好要使用的外表，权限较低的项目成员直接使用外表。这样避免了权限较低的项目成员接触到明文的用户名密码，也避免了在sql脚本中出现明文密码。

    -   第三种location的方式，MaxCompute会以tddl sdk的连接方式与数据库进行交互。这种使用方式需要在tableProperties中指定`odps.federation.jdbc.tddl.appname`。
        -   tddl sdk权限验证（线下/测试环境）: 只需要指定appname即可。
        -   tddl sdk权限验证（线上/弹内环境）: 需要指定ak和sk, ak/sk是tddl授权的应用的安全凭证。安全凭证的获取方式访问[链接](https://dauth-portal.alibaba.net/)，具体原理请参见[链接](http://docs.idb.alibaba-inc.com/manual/db_tddl_app.html)。

            鉴权的具体过程及原理请参见[链接1](http://gitlab.alibaba-inc.com/middleware/1111/wikis/safe)，[链接2](http://gitlab.alibaba-inc.com/middleware/1111/wikis/platform_tddl_security)。

            由于中间件加固目前处于老版本升级和兼容的过度阶段，因此现在的鉴权不指定ak/sk也可以，后面中间件加固团队会做统一收口，如果没有ak，sk则tddl鉴权失败。

-   TBLPROPERTIES
    -   `odps.federation.jdbc.condition`: 从mysql获取数据的时候，会添加该筛选条件。设置`odps.federation.jdbc.condition` 与 `select * from text_test_jdbc_write_external where condition`两者的区别： 假设mysql表中有100行数据，condition条件可以筛选出10条数据。 若通过 `odps.federation.jdbc.condition` 设置，在mysql上做筛选，MaxCompute通过外表读取到的数据只有10条。 若通过

        ```
        select * from text_test_jdbc_write_external where
                  condition
        ```

        筛选，那么MaxCompute会从mysql读取100条数据，然后在MaxCompute的运行时筛选出其中10条数据。

    -   `odps.federation.jdbc.colmapping`通过属性可以做列名的映射。比如

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

    -   `odps.federation.jdbc.insert.type`: 写数据到mysql时候的类型。只支持以下三类 `[simpleInsert, insertOnDuplicateKeyUpdate, replaceInto]`。 若不设置则默认为simpleInsert。

        MaxCopute的insert语句会分别解析为以下的三类sql语句以更新数据库：

        ```
        insert into sqlTable xxx values xxx;
          insert into sqlTable xxx values xxx on duplicate key update col1=values(col1), col2=values(col2);
          replace into sqlTable xxx values xxx;
        ```

    -   `odps.federation.jdbc.tddl.app.access.key`: 被授权aone应用的access key.
    -   `odps.federation.jdbc.tddl.app.secret.key`: 被授权aone应用的secret key.
    -   `odps.federation.jdbc.tddl.appname`: tddl配置的应用名称。 注意：若设置了该值，则odps会以tddl sdk的方式使用该appname连接mysql数据库。具体tddl sdk的使用参见tddl wiki
-   建表示例

    如以app name的方式连接tddl的数据库appname=ODPS\_TDDL\_TEST\_APP，tablename=odps\_federation\_localrun\_write：

    ```
    --注意设置相关set语句
    drop table if exists text_test_jdbc_external;
    CREATE EXTERNAL TABLE if not exists text_test_jdbc_external
    (
      colmapping tinyint, --c_tinyint tinyint,
      c_smallint smallint,
      c_int int,
      c_bigint bigint,
      c_utinyint tinyint,
      c_usmallint smallint,
      c_uint int,
      c_ubigint bigint,
      c_boolean tinyint,
      --c_float float,  -- in tddl, not recommend float and double type as it may lost precision
      --c_double double,
      c_string string,
      c_datetime datetime,
      c_decimal decimal
    )
    STORED BY 'com.aliyun.odps.jdbc.JdbcStorageHandler'
    location 'jdbc:mysql://dummy_host?table=odps_federation_localrun_write'
    TBLPROPERTIES(
    'odps.federation.jdbc.insert.type'='simpleInsert',
    'odps.federation.jdbc.condition'='c_boolean = 1 and c_int is not null and c_utinyint=127',
    'odps.federation.jdbc.colmapping'='colmapping:c_tinyint',
    'odps.federation.jdbc.tddl.appname'='ODPS_TDDL_TEST_APP',
    'odps.federation.jdbc.tddl.app.access.key'='your tddl app access key',
    'odps.federation.jdbc.tddl.app.secret.key'='your tddl app secret key');
    ```


## 读取外部表 {#section_x3t_rs4_hfb .section}

关联Mysql的外部表数据导入到MaxCompute。

-   在MaxCompute建立对应的内部表。

    ```
    CREATE TABLE if not exists text_test_jdbc_max_compute
    (
      c_tinyint tinyint,
      c_smallint smallint,
      c_int int,
      c_bigint bigint,
      c_utinyint tinyint,
      c_usmallint smallint,
      c_uint int,
      c_ubigint bigint,
      c_boolean tinyint,
      --c_float float,
      --c_double double,
      c_string string,
      c_datetime datetime,
      c_decimal decimal
    );
    ```

-   数据导入到MaxCompute。

    ```
    --注意设置相关set语句
    insert OVERWRITE TABLE text_test_jdbc_odps select * from text_test_jdbc_read_external;
    ```


对于复杂的运算 比如 group join等，建议将外表的数据导入为MaxCompute的表，然后再做运算。 通过这样子将数据先导入系统的情况下，对同样数据的计算就会更高效得多。

建立外表与数据导入到MaxCompute表的关系

-   建立外表可以理解为建立了一个MaxCompute与mysql的数据通道，MaxCompute并不存储mysql的任何数据。若mysql数据丢失，则数据丢失。
-   数据导入到MaxCompute表后，MaxCompute实际存储有mysql中的数据。若mysql数据丢失，这时可以通过导入数据的MaxCompute的表将数据找回。

## 写入外表（append） {#section_a1b_ws4_hfb .section}

为保证数据的正确写入，建立外表时映射列的列名及数据类型需要与数据库的scheme保持一致。类型上的精度损失对应的行为 请参考上面支持的数据类型的注意项。

```
--注意设置相关set语句
insert INTO TABLE  text_test_jdbc_external select * from text_test_jdbc_max_compute;
```

`insert INTO mysql-external-table`和`insert OVERWRITE mysql-external-table`的区别：

-   对于mysql外表的写入，上述两种语法没有区别，实际上都是append到外表。
-   都可以通过`odps.federation.jdbc.insert.type`属性设置插入类型。具体参考“TBLPROPERTIES”中对应的属性说明。

**说明：** 上述描述只针对mysql外表的情况，对于MaxCompute的表上述语法按照各自的语义运行。

