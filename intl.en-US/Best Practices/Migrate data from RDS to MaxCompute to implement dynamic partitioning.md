# Migrate data from RDS to MaxCompute to implement dynamic partitioning {#concept_gxd_crn_4fb .concept}

This topic describes how to use the data synchronization feature of DataWorks to automatically create partitions and dynamically migrate data from RDS to MaxCompute.

## Preparations {#section_od5_frc_pfb .section}

1.  MaxCompute

    Activate MaxCompute and create a workspace. In this example, create a workspace in China North 2 \(Beijing\) and enable related DataWorks services for this workspace, as shown in the following figure.

    ![](images/14287_en-US.jpeg)

    **Note:** If you are using DataWorks for the first time, you need to complete the operations described in [Preparation](../../../../intl.en-US/Prepare/Prepare Alibaba Cloud account.md#). For example, you need to get your account ready, set the workspace role, and configure the workspace. For more information about how to activate MaxCompute, see [Activate MaxCompute](../../../../intl.en-US/Prepare/Activate MaxCompute.md#). After you have completed the DataWorks preparation, log on to the DataWorks console, select the corresponding workspace, and click **Enter Project** for data development.

2.  Add data sources.

    1.  Add the RDS data source.

        Log on the DataWorks console, select [Data Integration](../../../../intl.en-US/User Guide/Data integration/Data integration introduction/Data Integration Overview.md#), and add the [MySQL data source](../../../../intl.en-US/User Guide/Data integration/Data source configuration/Configure MySQL data source.md#).

    2.  Add the ODPS data source. For more information, see [Configure MaxCompute data source](../../../../intl.en-US/User Guide/Data integration/Data source configuration/Configure MaxCompute data source.md#).
    Verify that the configuration is completed, as shown in the following figure.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/24452/154399516914385_en-US.png)

3.  Make sure that a table is available in the destination ODPS database.

    In the ODPS database, create a destination table named ods\_user\_info\_d. This table corresponds to a table in RDS. Under **Data Development**, right-click **New ODPS SQL node**, create a node named create\_table\_ddl, and enter the table creation statements, as shown in the following figure.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/24452/154399516933545_en-US.png)

    The SQL statements are as follows:

    ```
    
    CREATE TABLE IF NOT EXISTS ods_user_info_d (
    uid STRING COMMENT 'UserID',
    gender STRING COMMENT 'gender',
    age_range STRING COMMENT 'Age',
    zodiac STRING COMMENT 'zodiac'
    )
    PARTITIONED BY (
    dt STRING
    );
    
    ```

    You can also select **Create Table** under **Business Flow** \> **Table**, as shown in the following figure.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/24452/154399516933547_en-US.png)

    For more information, see [Create a table and upload data](../../../../intl.en-US/Quick Start/Step 1: Create a table and upload data.md#).

4.  Create a business flow.

    Choose **Data Development** \> **Business Flow** and click **Create Business Flow** to create a workshop, as shown in the following figure.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/24452/154399516933546_en-US.png)

5.  Create and configure a synchronization task node.

    Create a synchronization node named rds\_sync under the workshop business flow, as shown in the following figure.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/24452/154399516914388_en-US.png)


For more information about how to configure a data synchronization task, see [DataWorks data development and O&M](../../../../intl.en-US/Quick Start/Instructions.md#).

## Automatic creation of partitions {#section_sfm_3rc_pfb .section}

After the preparations are completed, the data in RDS needs to be synchronized to MaxCompute every day, so that the date-based partition can be automatically created.

1.  Select the data source and data destination, as shown in the following figure.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/24452/154399516914391_en-US.png)

2.  Set the parameters, as shown in the following figure.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/24452/154399516914396_en-US.png)

    The system time parameter `${bizdate}` is generally used by default. The format is yyyymmdd. This parameter corresponds to the **Partition** parameter under **Destination**. The partition date is called business date. In most cases, users process the business data generated in the previous day. Therefore, when the data synchronization task is scheduled and executed, the partition date is automatically replaced with the date one day before the task execution date. To use the task execution date as the partition value \(partition date\), you must select **Schedule** to customize the parameter, as shown in the following figure.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/24452/154399516914396_en-US.png)

    The custom parameter can be configured in different formats. By default, the real-time date is displayed. You can select a date and a format as needed. The custom parameter can be set in one of the following formats:

    N years later: `$[add_months(yyyymmdd,12*N)]`

    N years ago: `$[add_months(yyyymmdd,-12*N)]`

    N months later: `$[add_months(yyyymmdd,N)]`

    N months ago: `$[add_months(yyyymmdd,-N)]`

    N weeks later: `$[yyyymmdd+7*N]`

    N weeks ago: `$[yyyymmdd-7*N]`

    N days later: `$[yyyymmdd+N]`

    N days ago: `$[yyyymmdd-N]`

    N hours later: `$[hh24miss+N/24]`

    N hours ago: `$[hh24miss-N/24]`

    N minutes later: `$[hh24miss+N/24/60]`

    N minutes ago: `$[hh24miss-N/24/60]`

    **Note:** 

    -   You need to use brackets \(\[\]\) to edit the value calculation formula of the custom parameter, for example, `key1=$[yyyy-mm-dd]`.
    -   The default calculation unit of the custom parameter is day. For example, `$[hh24miss-N/24/60]` indicates the calculation result of `(yyyymmddhh24miss-(N/24/60 * 1 day))`. The hour, minute, and second are in the format of hh24miss.
    -   The calculation unit of add\_months is month. For example, `$[add_months(yyyymmdd,12 N)-M/24/60]` indicates the calculation result of `(yyyymmddhh24miss-(12 N 1 month))-(M/24/60 1 day)`. The year, month, and day are in the format of `yyyymmdd`.
    For more information, see [Parameter configuration](https://www.alibabacloud.com/help/zh/doc-detail/74450.htm).

3.  Perform the test run.

    Click **Save** to save all configurations, and click **Run**, as shown in the following figure.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/24452/154399516933548_en-US.png)

    View the running log, as shown in the following figure.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/24452/154399516914399_en-US.png)

    In the sample log shown in the preceding figure, you can find that the partition value in MaxCompute \(whose printed name is ODPS\) information is dt=20181025. This indicates that the partition value is automatically replaced. Verify that the data is successfully migrated to the ODPS table, as shown in the following figure.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/24452/154399517014400_en-US.png)

    **Note:** In MaxCompute 2.0, parameter settings are required for partition table query. Full query is not supported. The SQL statements are as follows:

    ```
    --Check whether the data is successfully written to MaxCompute.
    select count(*)from ods_user_info_d where dt=business date;
    ```

    For more information about the SELECT command, see [Select operation](../../../../intl.en-US/User Guide/SQL/Select Operation/Introduction to the SELECT Syntax.md#).

    Now you can see that the data has been migrated to the ODPS table and a partition value has been successfully created. Then, when the task is executed at scheduled time, the data in RDS is automatically synchronized to the date-based partition in MaxCompute.


## Data patching {#section_cbp_cqd_pfb .section}

If you have many historical data that is generated before the execution date, and you want to implement automatic synchronization and partitioning, you can log on to the DataWorks console, click **O&M**, select the data synchronization node, and click **Patch Data**.

1.  Filter the historical data in RDS by date. For example, filter the historical data generated on 2018-09-13, so that the data can be automatically synchronized to the 20180825 partition in MaxCompute. You can use a WHERE clause to filter data in RDS, as shown in the following figure.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/24452/154399517014401_en-US.png)

2.  Perform data patching. Choose **Save** \> **Submit**. After the data is submitted, choose **O&M** \> **Task List** \> **Cycle Task**, select the rds\_sync node, and choose **Patch Data** \> **Current node**, as shown in the following figure.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/24452/154399517020998_en-US.png)

3.  On the displayed page, select the business date, as shown in the following figure.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/24452/154399517020999_en-US.png)

4.  Click **OK**. Multiple synchronization task instances are generated at the same time and executed in sequence, as shown in the following figure.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/24452/154399517021000_en-US.png)

5.  View the running log. You can see the process of extracting data from RDS. A partition has been automatically created in MaxCompute, as shown in the following figure.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/24452/154399517021001_en-US.png)

    View the results. You can check whether the data is written successfully, whether a partition is created, and whether the data is synchronized to the partition table, as shown in the following figure.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/24452/154399517021002_en-US.png)

    Query the partition information, as shown in the following figure.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/24452/154399517033549_en-US.png)

    **Note:** In MaxCompute 2.0, parameter settings are required for partition table query. The partition column needs to be updated to the business date. If the task execution date is 20180717, the business date is 20180716. The SQL statements are as follows:

    ```
    --Check whether the data is successfully written to MaxCompute.
    select count(*)from ods_user_info_d where dt=business date;
    ```


## Create a partition by non-date field using hash {#section_rr4_tgt_pfb .section}

If a huge volume of data needs to be processed, or if a full amount of data is partitioned according to a non-date field \(such as province\) at the first time, a data partition cannot be created automatically during data integration. Therefore, you can use the hash algorithm to save the same values in an RDS field to the corresponding partition in MaxCompute.

The procedure is as follows:

1.  Synchronize the full amount of data to a temporary table in MaxCompute. Create an SQL script node and choose **Run** \> **Save** \> **Submit**.

    The SQL statements are as follows:

    ```
    drop table if exists ods_user_t;
    CREATE TABLE ods_user_t ( 
            dt STRING,
    	uid STRING,
            gender STRING,
            age_range STRING,
            zodiac STRING);
    insert overwrite table ods_user_t select dt,uid,gender,age_range,zodiac from ods_user_info_d;--Save the data in the ODPS table to the temporary table.
    ```

2.  Create a synchronization task node named mysql\_to\_odps to synchronize the full amount of RDS data to MaxCompute without setting the partition, as shown in the following table.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/24452/154399517033591_en-US.png)

3.  Use SQL statements to dynamically create a partition for the destination table. The SQL statements are as follows:

    ```
    drop table if exists ods_user_d;
    --Create a partition table (the destination table) in ODPS.
    	CREATE TABLE ods_user_d (
    	uid STRING,
            gender STRING,
            age_range STRING,
            zodiac STRING
    )
    PARTITIONED BY (
    	dt STRING
    );
    --Execute the dynamic partitioning SQL statements to automatically create a partition according to the dt field in the temporary table. The partition value is automatically created for a data record. Data records that share the same dt value have the same partition field value.
    --For example, some data records share the value 20180913 in the dt field. As a result, a partition is automatically created in the MaxCompute partition table with a partition value of 20181025.
    --The dynamic partitioning SQL statements are as follows:
    --A date_time field is added in the select field, indicating that a partition is automatically created according to this field.
    insert overwrite table ods_user_d partition(dt)select dt,uid,gender,age_range,zodiac from ods_user_t;
    --After the import is completed, you can delete the temporary table to reduce excessive storage costs.
    drop table if exists ods_user_d;
    ```

4.  Configure the three nodes to form a workflow and execute these nodes in sequence, as shown in the following figure.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/24452/154399517021007_en-US.png)

5.  View the execution process. The last node represents the process of dynamic partitioning, as shown in the following figure.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/24452/154399517021008_en-US.png)

    View data. Dynamic partitioning is completed automatically. Data records with the same date are synchronized to the same partition, as shown in the following figure.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/24452/154399517033594_en-US.png)

    Query the partition information, as shown in the following figure.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/24452/154399517021013_en-US.png)

    You can follow the preceding steps to name a partition using the province field.


The data synchronization feature of DataWorks supports automatic data operations, including data synchronization, data migration, and data synchronization task scheduling. For more information about scheduling configuration, see [Time attributes](../../../../intl.en-US/User Guide/Data development/Scheduling Configuration/Time attributes.md#) in Scheduling Configuration.

