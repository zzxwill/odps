# RDS迁移到MaxCompute实现动态分区 {#concept_gxd_crn_4fb .concept}

本文向您详细介绍如何使用DataWorks数据同步功能，自动创建分区，动态的将RDS中的数据，迁移到Maxcompute大数据计算服务上。

## 准备工作 {#section_od5_frc_pfb .section}

1.  MaxCompute

    开通MaxCompute服务并创建好项目，本文中在华北2（北京）区域创建项目，同时启动DataWorks相关服务，如下所示。

    ![](images/14287_zh-CN.jpeg)

    **说明：** 如果您是第一次使用DataWorks，请确认已经根据[准备工作](https://help.aliyun.com/document_detail/74246.html)模块的操作，准备好账号和项目角色、项目空间等内容，开通MaxCompute请参见请参见[开通MaxCompute](../../../../cn.zh-CN/准备工作/开通MaxCompute.md#)。然后进入DataWorks管理控制台，单击对应项目后的**进入数据开发**，即可开始数据开发操作。

2.  准备数据源，如下图。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/24452/154081918714385_zh-CN.png)

    新增ODPS数据源和MYSQL数据源，详细描述请参考文档[配置MaxCompute数据源](https://help.aliyun.com/knowledge_detail/74280.html)和[配置MySQL数据源](https://help.aliyun.com/knowledge_detail/74276.html)。

3.  确认作为目标的ODPS数据库中有表

    在ODPS数据库中创建RDS对应目标表emp\_test\_new。

    建表完成后，可执行`desc emp_test_new;`语句查看表详情。

    详细的信息请参见[建表并上传数据](https://help.aliyun.com/document_detail/84670.html)。

4.  新建并配置同步任务节点

    本节将新建一个同步节点sdtest并进行配置，将MySQL中的表emp\_test\_new中的数据写入到自己的ODPS数据库中。如下图。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/24452/154081918714388_zh-CN.png)


详细的数据同步任务的操作和配置请参见[DataWorks数据开发和运维](https://help.aliyun.com/document_detail/84669.html)。

## 自动创建按天日期的分区 {#section_sfm_3rc_pfb .section}

将RDS中的数据定时每天同步到MaxCompute中，自动创建按天日期的分区。

1.  选择来源，如下图。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/24452/154081918714391_zh-CN.png)

2.  选择目标，如下图。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/24452/154081918714392_zh-CN.png)

3.  参数配置，如下图。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/24452/154081918714394_zh-CN.png)

    一般配置到这个地方的时候，默认是系统自带的时间参数：`${bdp.system.bizdate}`，格式为yyyymmdd。也就是说在调度执行这个任务的时候，这个分区会被自动替换为任务执行日期的前一天，一般用户会在当前跑前一天的业务数据，这个日期也叫业务日期。如果用户要使用当天任务运行的日期作为分区值，需要自定义这个参数。如下图。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/24452/154081918714396_zh-CN.png)

    自定义参数设置，格式非常灵活，日期是当天日期，用户可以自由选择哪一天以及格式。可供参考的变量参数配置方式如下：

    后N年：`$[add_months(yyyymmdd,12*N)]`

    前N年：`$[add_months(yyyymmdd,-12*N)]`

    后N月：`$[add_months(yyyymmdd,N)]`

    前N月：`$[add_months(yyyymmdd,-N)]`

    后N周：`$[yyyymmdd+7*N]`

    前N周：`$[yyyymmdd-7*N]`

    后N天：`$[yyyymmdd+N]`

    前N天：`$[yyyymmdd-N]`

    后N小时：`$[hh24miss+N/24]`

    前N小时：`$[hh24miss-N/24]`

    后N分钟：`$[hh24miss+N/24/60]`

    前N分钟：`$[hh24miss-N/24/60]`

    **说明：** 

    -   请以中括号 \[\] 编辑自定义变量参数的取值计算公式，例如 `key1=$[yyyy-mm-dd]`。
    -   默认情况下，自定义变量参数的计算单位为天。例如 `$[hh24miss-N/24/60]`表示`(yyyymmddhh24miss-(N/24/60 * 1天))`的计算结果，然后按 hh24miss 的格式取时分秒。
    -   使用 add\_months 的计算单位为月。例如 `$[add_months(yyyymmdd,12 N)-M/24/60]` 表示`(yyyymmddhh24miss-(12 N 1月))-(M/24/60 1天)`的结果，然后按 `yyyymmdd` 的格式取年月日。
    参数设置请参见[参数配置](https://help.aliyun.com/document_detail/74450.html)。

4.  测试运行，如下图。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/24452/154081918714399_zh-CN.png)

    可以看到日志中，MaxCompute（日志中打印原名ODPS）的信息中partition分区，date\_test=20170829，自动替换成功。检查下实际的数据有没有转移到ODPS表中，如下图。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/24452/154081918814400_zh-CN.png)

    **说明：** 在maxcompute2.0中分区表查询需要添加分区筛选，SQL语句如下。其中分区列需要更新为业务日期，如任务运行的日期为20180717，那么业务日期为20180716。

    ```
    --查看是否成功写入MaxCompute
    select count(*) from ods_raw_log_d where dt=业务日期;
    select count(*)from ods_user_info_d where dt=业务日期;
    ```

    此时看到数据已经迁移到ODPS表中，并且成功地创建了一个分区值。那么这个任务在执行定时调度的时候，会每天将RDS中的数据同步到MaxCompute中按照日期自动创建的分区中。


## 补数据功能实现历史数据自动同步 {#section_cbp_cqd_pfb .section}

1.  首先，我们需要在RDS端把历史数据按照日期筛选出来，比如历史数据2017-08-25这天的数据，我们要自动同步到MaxCompute的20170825的分区中。在RDS阶段可以设置where过滤条件，如图。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/24452/154081918814401_zh-CN.png)

2.  补数据操作。然后**保存** \> **提交**。提交后到**运维中心** \> **任务管理** \> **图形模式**右键单击**补数据节点**。如下图。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/24452/154081918820998_zh-CN.png)

3.  跳转至补数据节点页面。选择日期区间，如下图。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/24452/154081918820999_zh-CN.png)

4.  单击**提交** \> **运行**。此时会同时生成多个同步的任务实例按顺序执行。如下图。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/24452/154081918821000_zh-CN.png)

5.  查看运行的日志，可以看到运行过程中对RDS数据的抽取。此时MaxCompute已自动创建分区，如图。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/24452/154081918821001_zh-CN.png)

    查看运行结果。数据写入的情况，以及是否自动创建了分区，数据是否已同步到分区表中，如下图。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/24452/154081918821002_zh-CN.png)

    **说明：** 在maxcompute2.0中分区表查询需要添加分区筛选，SQL语句如下。其中分区列需要更新为业务日期，如任务运行的日期为20180717，那么业务日期为20180716。

    ```
    --查看是否成功写入MaxCompute
    select count(*) from ods_raw_log_d where dt=业务日期;
    select count(*)from ods_user_info_d where dt=业务日期;
    ```


## Hash实现非日期字段分区 {#section_rr4_tgt_pfb .section}

如果用户数据量比较巨大，或者第一次全量的数据并不是按照日期字段进行分区，而是按照省份等非日期字段分区，那么此时数据集成操作就不能做到自动分区了。也就是说，可以按照RDS中某个字段进行hash，将相同的字段值自动存放到这个字段对应值的MaxCompute分区中。

流程如下：

1.  首先我们需要把数据全量同步到MaxCompute的一个临时表。创建一个SQL脚本节点。命令如下。

    ```
    drop table if exists emp_test_new_temp;
    CREATE TABLE emp_test_new_temp 
    (date_time STRING,
    	name STRING,
    	age BIGINT,
    	sal DOUBLE);
    ```

2.  创建同步任务的节点，就是简单的同步任务，将RDS数据全量同步到MaxCompute，不需要设置分区。
3.  使用sql语句进行动态分区到目标表。命令如下：

    ```
    drop table if exists emp_test_new;
    --创建一个ODPS分区表（最终目的表）
    	CREATE TABLE emp_test_new (
    	date_time STRING,
    	name STRING,
    	age BIGINT,
    	sal DOUBLE
    )
    PARTITIONED BY (
    	date_test STRING
    );
    --执行动态分区sql，按照临时表的字段date_time自动分区，date_time字段中相同的数据值，会按照这个数据值自动创建一个分区值
    --例如date_time中有些数据是2017-08-25，会自动在ODPS分区表中创建一个分区，date=2017-08-25
    --动态分区sql如下
    --可以注意到sql中select的字段多写了一个date_time，就是指定按照这个字段自动创建分区
    insert overwrite table emp_test_new partition(date_test)select date_time,name,age,sal,date_time from emp_test_new_temp
    --导入完成后，可以把临时表删除，节约存储成本
    drop table if exists emp_test_new_temp;
    ```

    在MaxCompute中我们通过SQL语句来完成同步。详细的SQL语句介绍请参见[阿里云大数据利器MaxCompute学习之--分区表的使用](https://yq.aliyun.com/articles/81775?spm=a2c4e.11153940.blogcont182972.20.72856a07pHyeLb)。

4.  最后将三个节点配置成一个工作流，按顺序执行。如下图。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/24452/154081918821007_zh-CN.png)

5.  查看执行过程。我们可以重点观察最后一个节点的动态分区过程，如下图。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/24452/154081918821008_zh-CN.png)

    查看数据。动态的自动化分区完成。相同的日期数据迁移到了同一个分区中。如下图。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/24452/154081918821013_zh-CN.png)

    如果是以省份字段命名分区，执行步骤参请考上述内容。


DataWorks数据同步功能可以完成大部分自动化作业，尤其是数据的同步迁移，调度等，了解更多的调度配置请参见调度配置[时间属性](https://help.aliyun.com/document_detail/74452.html)。

