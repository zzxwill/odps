# Import or export data using the Data Integration function {#concept_k5w_ybg_vdb .concept}

You can use [Data Integration](https://www.alibabacloud.com/help/doc-detail/47677.html) function of DataWorks to create data synchronization tasks and import and export MaxCompute data.

## Prerequisites {#section_y1n_bcg_vdb .section}

Before importing or exporting data, make sure you have completed the preparatory operations. See [Prepare an Alibaba Cloud account](../../../../intl.en-US/Prepare/Prepare Alibaba Cloud account.md) and [Purchase and create a project](../../../../intl.en-US/Prepare/Create a project.md).

## Add MaxCompute Data Source {#section_drp_ccg_vdb .section}

**Note:** 

-   Only the project administrator can create a data source. Other roles can only view the data source.
-   **If the data source you want to add is a current MaxCompute project, skip this operation**. After this project is created and appears as a Data Integration data source, this project is added as a MaxCompute data source named odps\_first by default.

**Procedure**

1.  Log on to the [DataWorks console](https://workbench.data.aliyun.com/console?spm=a2c4g.11186623.2.7.bBZHDz) as an administrator and click **Enter Workspace** in the actions column of the relevant project in the **Project List**.
2.  Click **Data Integration** in the top navigation bar to go to the **Data Source** page.
3.  Click **Add Data Source**.
4.  Enter relevant configurations in the data dialog box.

    Configurations:

    -   Data source name: Contains letters, numbers, and underlines. It must begin with a letter or an underline, and cannot exceed 60 characters.
    -   Data source description: Provides a brief description of the data source, and cannot exceed 80 characters.
    -   Data source type: Currently it is MySQL.
    -   ODPS Endpoint: Read-only by default. The value is automatically read from the system configuration.
    -   MaxCompute project name: Identifies the corresponding MaxCompute project.
    -   Access ID: The Access ID associated with the account of the MaxCompute project owner.
    -   AccessKey: The AccessKey associated with the account of the MaxCompute project owner, used in pairs with the Access ID.
5.  Click **Test Connectivity**.
6.  If the connectivity test is successful, click **Save**.

**Note:** For the configuration of other data sources, see [data source configuration](https://www.alibabacloud.com/help/doc-detail/60416.html).

## Import data through Data Integration {#section_zdw_1dg_vdb .section}

Take importing MySQL data to MaxCompute as an example, you can configure a synchronization task using **Wizard Mode** or **Script Mode**.

**Configure a synchronization task in wizard mode**

1.  Create a Wizard Mode synchronization task.
2.  Select the source.

    Select the MySQL data source and the source table “mytest”. The data browsing area is collapsed by default. Click **Next**.

3.  Select a target.

    The target must be a previously created MaxCompute table. You can also create a new table by clicking **Quick Table Creation**.

    Configuration item descriptions:

    -   Partition information: You must specify every level of partition. When writing data to a table with three levels of partitions, you must configure the last partition level, for example, pt=20150101, type=1, biz=2. This item is unavailable for non-partitioned tables.
    -   Data clearing rules:
        -   Clear existing data before writing: Before data is imported to a table or partition, all data in the table or partition is cleared, which is equivalent to Insert Overwrite.
        -   Retain existing data before writing: Existing data is not cleared before new data is imported. Each operation appends new data, which is equivalent to Insert Into.
4.  Map the fields.

    Select the mapping between fields. You must configure the field mapping relationships. The **Source Table Fields** on the left correspond one to one with the **Target Table Fields** on the right.

5.  Control the channel.

    Click **Next** to configure the maximum job rate and dirty data check rules.

    Configurations:

    -   Maximum job rate: Determines the highest rate possible for data synchronization jobs. The actual rate of the job may vary with the network environment, database configuration, and other factors.
    -   Concurrent job count: For a single synchronization job, Concurrent job count \* Individual job transmission rate = Total job transmission rate.
    **When a maximum job rate is specified, how do you select the concurrent job count?**

    -   If your data source is an online business database, we recommend that you do not set a large value for the concurrent job count to avoid interfering with the online database.
    -   If you require a high data synchronization rate, we recommend that you select the highest job rate and a large concurrent job count.
6.  Preview and store.

    Make sure the configuration of the task is correct, and click **Save**.


## Run the synchronization task {#section_lfd_z2g_vdb .section}

**Run a synchronization task directly**

If system variable parameters are set in the synchronization task, the variable parameter configuration window is displayed during task operation.

After saving the task, click **Run** to run the task immediately.  You can also click **Submit** to submit the synchronization task to the scheduling system of DataWorks. The scheduling system automatically and periodically runs the task from the second day according to the configuration attributes. For more information on scheduling configurations, see [Scheduling configuration description](https://www.alibabacloud.com/help/doc-detail//50130.html).

## Configure a Synchronization Task in Script Mode {#section_ut3_mfg_vdb .section}

You can use the following script to configure synchronization tasks. Other configurations and job operation are the same as **Wizard Mode**.

```
{
  "type": "job",
  "version": "1.0",
  "configuration": {
    "reader": {
      "plugin": "mysql",
      "parameter": {
        "datasource": "mysql",
        "where": "",
        "splitPk": "id",
        "connection": [
          {
            "table": [
              "person"
            ],
            "datasource": "mysql"
          }
        ],
        "connectionTable": "person",
        "column": [
          "id",
          "name"
        ]
      }
    },
    "writer": {
      "plugin": "odps",
      "parameter": {
        "datasource": "odps_first",
        "table": "a1",
        "truncate": true,
        "partition": "pt=${bdp.system.bizdate}",
        "column": [
          "id",
          "col1"
        ]
      }
    },
    "setting": {
      "speed": {
        "mbps": "1",
        "concurrent": "1"
      }
    }
  }
}
```

## References { .section}

-   For the Writer configurations about different types of data sources, see [Configure Reader Plug-ins](https://www.alibabacloud.com/help/faq-list/49806.html).
-   For the Writer configurations about different types of data sources, see [Configure Writer Plug-ins](https://www.alibabacloud.com/help/faq-list/49807.html).

