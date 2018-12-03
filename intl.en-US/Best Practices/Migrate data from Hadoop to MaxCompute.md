# Migrate data from Hadoop to MaxCompute {#concept_rn1_jcm_bfb .concept}

This topic describes how to use the data synchronization feature of DataWorks to migrate data from Hadoop to Alibaba Cloud MaxCompute.

## Prepare the environment {#section_vv3_pqy_bfb .section}

1.  Build a Hadoop cluster.

    Before data migration, you must ensure that your Hadoop cluster works properly. You can use Alibaba Cloud E-MapReduce to automatically build a Hadoop cluster.

    The version information of E-MapReduce Hadoop is as follows:

    E-MapReduce version: EMR-3.10.1 or 3.11.0

    Cluster type: Hadoop

    Software\(for EMR-3.11.0\): HDFS2.7.2 / YARN2.7.2 / Hive2.3.3 / Ganglia3.7.2 / Spark2.2.1 / HUE4.1.0 / Zeppelin0.7.3 / Tez0.9.1 / Sqoop1.4.6 / Pig0.14.0 / ApacheDS2.0.0 / Knox0.13.0

    The network type of the Hadoop cluster is classic. The region is China East 1 \(Hangzhou\). The ECS compute resource of the master instance group is configured with an Internet IP address and an intranet IP address. The high availability mode is set to No \(a non-HA mode\). The following figure shows the configuration for EMR-3.10.1.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/21113/154382098911593_en-US.png)

2.  MaxCompute

    For more information, see [Activate MaxCompute](../../../../intl.en-US/Prepare/Activate MaxCompute.md#).

    Activate MaxCompute and create a project. In this topic, create a project named bigdata\_DOC in China East 1 \(Hangzhou\) and enable the related DataWorks services for this project.


## Prepare data {#section_p2m_nty_bfb .section}

1.  Create test data on the Hadoop cluster.

    In the E-MapReduce console, go to the Hadoop cluster page and use**Notebook** to create a notebook task. The table creation Hive statements in this example are as follows:

    ```
    CREATE TABLE IF NOT
    EXISTS hive_doc_good_sale(
    
    create_time timestamp,
    
    category STRING,
    
    brand STRING,
    
    buyer_id STRING,
    
    trans_num BIGINT,
    
    trans_amount DOUBLE,
    
    click_cnt BIGINT
    
    )
    
    PARTITIONED BY (pt string) ROW FORMAT
    DELIMITED FIELDS TERMINATED BY ',' lines terminated by '\n'
    ```

    Click **run**. The test table `hive_doc_good_sale` is then successfully created on the E-MapReduce Hadoop cluster.

    Insert the test data. You can select data from OSS or other data sources, or manually insert a small amount of test data. The following data can be manually inserted:

    ```
    insert into
    hive_doc_good_sale PARTITION(pt =1 ) values('2018-08-21','Coat','Brand A','lilei',3,500.6,7),('2018-08-22','Fresh food','Brand B','lilei',1,303,8),('2018-08-22','Coat','Brand C','hanmeimei',2,510,2),(2018-08-22,'Toiletries','Brand A','hanmeimei',1,442.5,1),('2018-08-22','Fresh food','Brand D','hanmeimei',2,234,3),('2018-08-23','Coat','Brand B','jimmy',9,2000,7),('2018-08-23','Fresh food','Brand A','jimmy',5,45.1,5),('2018-08-23','Coat','Brand E','jimmy',5,100.2,4),('2018-08-24','Fresh food','Brand G','peiqi',10,5560,7),('2018-08-24','Sanitary ware','Brand F','peiqi',1,445.6,2),('2018-08-24','Coat','Brand A','ray',3,777,3),('2018-08-24','Sanitary ware','Brand G','ray',3,122,3),('2018-08-24','Coat','Brand C','ray',1,62,7) ;
    ```

    After inserting the data, you can use the `select * from hive_doc_good_sale where pt =1;`statement to check whether the data exists in the Hadoop cluster table for migration.

2.  Use DataWorks to create a destination table.

    In the DataWorks console, click the MaxCompute project, and choose **Data Development** \> **New** \> **Create Table**.

    In the displayed window, enter the following table creation SQL statements:

    ```
    CREATE TABLE IF NOT EXISTS hive_doc_good_sale(
       create_time string,
       category STRING,
       brand STRING,
       buyer_id STRING,
       trans_num BIGINT,
       trans_amount DOUBLE,
       click_cnt BIGINT
       )
       PARTITIONED BY (pt string) ;
    ```

    When creating a table, pay attention to the mapping between the Hive data type and the MaxCompute data type.

    The data synchronization feature of DataWorks does not support timestamp data. Therefore, in the DataWorks table creation statements, create\_time is set to a string value. You can also use the odpscmd Command Line \(CLI\) tool to create a table. For more information about how to install and configure the tool, see [Install and configure a client](../../../../intl.en-US/Prepare/Install and configure a client.md#). The table creation process is as follows.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/21113/154382098911598_en-US.png)

    **Note:** Considering the compatibility of the Hive and MaxCompute data types, we recommend that you run the following command on the **odpscmd** client:

    ```
    set odps.sql.type.system.odps2=true;set
    odps.sql.hive.compatible=true;
    ```

    After the table is created, you can choose **Data Development** \> **Table Query** in the DataWorks console to view the table created in MaxCompute, as shown in the following figure.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/21113/154382099011599_en-US.png)


## Synchronize data {#section_esz_swy_bfb .section}

1.  Create a custom resource group.

    In most cases, the network between the project data node of MaxCompute and the data node of the Hadoop cluster is not connected. You can customize a resource group to execute the synchronization task of DataWorks on the master node of the Hadoop cluster. \(In general, the network between the master node and the data node on the Hadoop cluster is connected\).

    1.  View the data node of the Hadoop cluster.

        On the home page of the E-MapReduce console, choose Cluster Management \> Cluster \> Hosts. You can view the data node of the Hadoop cluster. As shown in the following figure, the host name of the master node on the E-MapReduce Hadoop cluster \(non-HA mode\) is emr-header-1, and the host name of the data node is emr-worker-X.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/21113/154382099011600_en-US.png)

        You can also click the ECS ID of the master node, click Connect on the displayed ECS details page, and run the hadoop dfsadmin –report command to view the data node, as shown in the following figure.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/21113/154382099011601_en-US.png)

        As shown in the preceding figure, the data node has only an intranet address and cannot communicate with the default resource group of DataWorks. Therefore, you need to customize a resource group and set the master node to a node that executes the synchronization task of DataWorks.

    2.  Create a custom resource group.

        In the DataWorks console, go to the Data Integration page, select Resource Group, and click New Resource Groups, as shown in the following figure. For more information about how to customize a resource group, see [Add scheduling resources](https://help.aliyun.com/document_detail/72979.html).

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/21113/154382099011602_en-US.png)

        When you add a server, you need to enter the information such as the ECS UUID and machine IP address. \(For a classic network, enter the sever name. For a VPC network, enter the server UUID. You can add scheduling resources only for classic networks in China East 2 \(Shanghai\) in DataWorks V2.0. In other regions, select the VPC network type when you add a scheduling resource group regardless of whether your network type is classic or VPC.\) Set the machine IP address to the Internet IP address of the master node because the intranet IP address might be inaccessible. The ECS UUID must be connected to the master node management terminal, which can be obtained by running the dmidecode | grep UUID command, as shown in the following figure. \(You can use the same method if your Hadoop cluster is not built on E-MapReduce.\)

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/21113/154382099011603_en-US.png)

        After adding the server, make sure that the network between the master node and DataWorks is connected. If you are using an ECS server, you must set the server security group. If you are using an intranet IP address for communication, set the server security group. For more information, see [Add security group](https://help.aliyun.com/document_detail/72978.html).

        If you are using an Internet IP address, you can directly set the Internet ingress and egress under **Security Group Rules**\(In practical application scenarios, we recommend that you set detailed bypass rules for your data security.\)

        After completing the preceding steps, install the custom resource group agent as prompted. If the state is **available**, the custom resource group is added successfully.

        If the state is **unavailable**, you can log on to the master node, and run the `tail –f/home/admin/alisatasknode/logs/heartbeat.log` command to check whether the heartbeat message between DataWorks and the master node has timed out, as shown in the following figure.

2.  Create a data source.

    For more information about how to create a data source in DataWorks, see [Data source configuration](https://help.aliyun.com/knowledge_list/72788.html).

    After you create a project in DataWorks, the data source is set to odps\_first by default. Therefore, you only need to add a Hadoop cluster data source. To do so, perform the following steps: On the Data Integration page of DataWorks, choose **Data Source** \> **New Source**, and select **HDFS**.

    In the displayed window, enter the data source name and defaultFS. If the E-MapReduce Hadoop cluster is an HA cluster, the address is IP:8020 of hdfs://emr-header-1. If the E-MapReduce Hadoop cluster is a non-HA cluster, the address is IP:9000 of hdfs://emr-header-1. In this topic, emr-header-1 is connected to DataWorks through the Internet. Therefore, enter the Internet IP address and open the security group.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/21113/154382099011608_en-US.png)

    After the configuration is completed, click Test Connectivity. If **Test connectivity successfully** is displayed, the data source is added successfully.

    **Note:** If the network type of the E-MapReduce Hadoop cluster is VPC, the connectivity test is not supported.

3.  Configure the data synchronization task.

    On the Data Integration page of DataWorks, click **Sync Tasks** and create a script mode. In the displayed window, select a data source, as shown in the following figure.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/21113/154382099011609_en-US.png)

    After the template is imported, the synchronization task is converted to the script mode. For more information, see [Script mode configuration](https://help.aliyun.com/document_detail/74304.html).

    When you configure the data synchronization task script, the data types of the DataWorks synchronization task and the Hive table are as follows.

    |**Data type in the****Hive****table**|**Data type in DataX / DataWorks**|
    |TINYINT,SMALLINT,INT,BIGINT|Long|
    |FLOAT,DOUBLE,DECIMAL|Double|
    |String,CHAR,VARCHAR|String|
    |BOOLEAN|Boolean|
    |Date,TIMESTAMP|Date|
    |Binary|Binary|

    The code details are as follows:

    ```
    {
      "configuration": {
        "reader": {
          "plugin": "hdfs",
          "parameter": {
            "path": "/user/hive/warehouse/hive_doc_good_sale/", 
            "datasource": "HDFS1",
            "column": [
              {
                "index": 0,
                "type": "string"
              },
              {
                "index": 1,
                "type": "string"
              },
              {
                "index": 2,
                "type": "string"
              },
              {
                "index": 3,
                "type": "string"
              },
              {
                "index": 4,
                "type": "long"
              },
              {
                "index": 5,
                "type": "double"
              },
              {
                "index": 6,
                "type": "long"
              }
            ],
            "defaultFS": "hdfs://121.199.11.138:9000",
            "fieldDelimiter": ",",
            "encoding": "UTF-8",
            "fileType": "text"
          }
        },
        "writer": {
          "plugin": "odps",
          "parameter": {
            "partition": "pt=1",
            "truncate": false,
            "datasource": "odps_first",
            "column": [
              "create_time",
              "category",
              "brand",
              "buyer_id",
              "trans_num",
              "trans_amount",
              "click_cnt"
            ],
            "table": "hive_doc_good_sale"
          }
        },
        "setting": {
          "errorLimit": {
            "record": "1000"
          },
          "speed": {
            "throttle": false,
            "concurrent": 1,
            "mbps": "1",
            "dmu": 1
          }
        }
      },
      "type": "job",
      "version": "1.0"
    }
    ```

    The path parameter indicates the place where the data is stored in the Hadoop cluster. You can log on to the master node and run the `hdfs dfs –ls /user/hive/warehouse/hive_doc_good_sale` command to confirm the place. For a partition table, you do not need to specify the partitions. The data synchronization feature of DataWorks can automatically recurse to the partition path, as shown in the following figure.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/21113/154382099011611_en-US.png)

    After the configuration is completed, click **Run**. If a message is displayed indicating that the task is executed successfully, the synchronization task is completed. If a message is displayed indicating that the task failed to be executed, copy the logs for further troubleshooting.


## Verify the results {#section_txr_ybz_bfb .section}

In the DataWorks console, choose **Data Development** \> **Table Query** and select the hive\_doc\_good\_sale table. You can check whether the Hive data has been synchronized to MaxCompute. You can also create a table query task, enter the `select * FROM hive_doc_good_sale where pt =1;` script in the task, and click Run to query the results.

You can also enter `select * FROM hive_doc_good_sale where pt =1;` in the **odpscmd** CLI tool to query the table results.

## Migrate data from MaxCompute to Hadoop {#section_z44_tdb_3fb .section}

To migrate data from MaxCompute to Hadoop, perform the preceding steps but exchange the reader and writer objects in the synchronization script. The following is an example:

```

{
  "configuration": {
    "reader": {
      "plugin": "odps",
      "parameter": {
      "partition": "pt=1",
      "isCompress": false,
      "datasource": "odps_first",
      "column": [
        "create_time",
        "category",
        "brand",
      "buyer_id",
      "trans_num",
      "trans_amount",
      "click_cnt"
    ],
    "table": "hive_doc_good_sale"
    }
  },
  "writer": {
    "plugin": "hdfs",
    "parameter": {
    "path": "/user/hive/warehouse/hive_doc_good_sale",
    "fileName": "pt=1",
    "datasource": "HDFS_data_source",
    "column": [
      {
        "name": "create_time",
        "type": "string"
      },
      {
        "name": "category",
        "type": "string"
      },
      {
        "name": "brand",
        "type": "string"
      },
      {
        "name": "buyer_id",
        "type": "string"
      },
      {
        "name": "trans_num",
        "type": "BIGINT"
      },
      {
        "name": "trans_amount",
        "type": "DOUBLE"
      },
      {
        "name": "click_cnt",
        "type": "BIGINT"
      }
    ],
    "defaultFS": "hdfs://47.99.162.100:9000",
    "writeMode": "append",
    "fieldDelimiter": ",",
    "encoding": "UTF-8",
    "fileType": "text"
    }
  },
  "setting": {
    "errorLimit": {
      "record": "1000"
  },
  "speed": {
    "throttle": false,
    "concurrent": 1,
    "mbps": "1",
    "dmu": 1
  }
  }
},
"type": "job",
"version": "1.0"
}
```

Before executing the preceding synchronization task, you must set the Hadoop cluster. For more information, see [Configure HDFS Writer](../../../../intl.en-US/User Guide/Data integration/Task Configuration/Configure Writer plug-in/Configure HDFS Writer.md#). After executing the synchronization task, you need to manually copy the synchronized files.

