# Migrate JSON data from OSS to MaxCompute {#concept_gyw_dhd_5fb .concept}

This topic describes how to use the data integration feature of DataWorks to migrate JSON data from OSS to MaxCompute and use the built-in string function GET\_JSON\_OBJECT of MaxCompute to extract JSON information.

## Upload data to OSS {#section_fmj_3hd_5fb .section}

Convert your JSON file to a TXT file and upload it to OSS. The following is a JSON file example:

```

{
    "store": {
        "book": [
             {
                "category": "reference",
                "author": "Nigel Rees",
                "title": "Sayings of the Century",
                "price": 8.95
             },
             {
                "category": "fiction",
                "author": "Evelyn Waugh",
                "title": "Sword of Honour",
                "price": 12.99
             },
             {
                 "category": "fiction",
                 "author": "J. R. R. Tolkien",
                 "title": "The Lord of the Rings",
                 "isbn": "0-395-19395-8",
                 "price": 22.99
             }
          ],
          "bicycle": {
              "color": "red",
              "price": 19.95
          }
    },
    "expensive": 10
}
```

Upload the applog.txt file to OSS. In this example, the OSS bucket is located in China East 2 \(Shanghai\).

## Use DataWorks to import data to MaxCompute {#section_zcj_s3d_5fb .section}

1.  Add an OSS data source.

    In the DataWorks console, go to the [Data Integration](../../../../intl.en-US/User Guide/Data integration/Data integration introduction/Data Integration Overview.md#) page and add an OSS data source. For more information, see [Configure OSS data source](../../../../intl.en-US/User Guide/Data integration/Data source configuration/Configure OSS data source.md#).

    Specific parameters are shown in the following figure. Click Finish after the data source connectivity test is successful. For more information about the endpoints, see [OSS Internet and intranet endpoints in different regions](https://help.aliyun.com/knowledge_detail/39585.html). The endpoints in this topic include http://oss-cn-shanghai.aliyuncs.com and http://oss-cn-shanghai-internal.aliyuncs.com. The OSS and DataWorks projects are located in the same region. Therefore, `http://oss-cn-shanghai-internal.aliyuncs.com` is used. This endpoint is connected through the intranet.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/62284/154382203831536_en-US.png)

2.  Create a data synchronization task.

    In the DataWorks console, create a **data synchronization** node. For more information, see [Configure OSS Reader](../../../../intl.en-US/User Guide/Data integration/Task Configuration/Configure Reader plug-in/Configure OSS Reader.md#).At the same time, create a table named mqdata in DataWorks to store the JSON data. For more information, see [Create a table](../../../../intl.en-US/User Guide/Data development/Table Management.md#).You can set the table parameters on the graphical interface. The mqdata table has only one column, which is named MQ data. The data type is string.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/62284/154382203831545_en-US.png)

    After creating a table, you can set the data synchronization task parameters on the graphical interface, as shown in the following figure. Set the destination data source to odps\_first and the destination table to mqdata. Set the original data source to OSS, and enter the file path and name as the object prefix. Set the column delimiter to caret \(^\) or any other characters that are not contained in the TXT file. \(DataWorks supports multiple column delimiters for the TXT data sources in OSS. Therefore, you can use characters such as %&%\#^$$^% to divide the data into a column.\)

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/62284/154382203831546_en-US.png)

    Set the mapping method to **same-line mapping**.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/62284/154382203831548_en-US.png)

    Click the script switching button in the upper-left corner to switch to the script mode. Set fileFormat to `"fileFormat":"binary"`. Then, after the JSON file is synchronized from OSS to MaxCompute, data in the JSON file is saved in the same row. That is, data in the JSON file shares the same field. Use the default values for other parameters. The following is an example of the code in script mode:

    ```
    
    {
        "type": "job",
        "steps": [
            {
                "stepType": "oss",
                "parameter": {
                    "fieldDelimiterOrigin": "^",
                    "nullFormat": "",
                    "compress": "",
                    "datasource": "OSS_userlog",
                    "column": [
                        {
                            "name": 0,
                            "type": "string",
                            "index": 0
                        }
                    ],
                    "skipHeader": "false",
                    "encoding": "UTF-8",
                    "fieldDelimiter": "^",
                    "fileFormat": "binary",
                    "object": [
                        "applog.txt"
                    ]
                },
                "name": "Reader",
                "category": "reader"
            },
            {
                "stepType": "odps",
                "parameter": {
                    "partition": "",
                    "isCompress": false,
                    "truncate": true,
                    "datasource": "odps_first",
                    "column": [
                        "mqdata"
                    ],
                    "emptyAsNull": false,
                    "table": "mqdata"
                },
                "name": "Writer",
                "category": "writer"
            }
        ],
        "version": "2.0",
        "order": {
            "hops": [
                {
                    "from": "Reader",
                    "to": "Writer"
                }
            ]
        },
        "setting": {
            "errorLimit": {
                "record": ""
            },
            "speed": {
                "concurrent": 2,
                "throttle": false,
                "dmu": 1
            }
        }
    }
    ```

    After completing the preceding configuration, click **run**.


## Obtain the JSON field information {#section_pbc_mf2_5fb .section}

Create an ODPS SQL node in your [Business Flow](../../../../intl.en-US/User Guide/Data development/Business flow/Business flow.md#).

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/62284/154382203831551_en-US.png)

Enter the `SELECT*from mqdata;` statement to view the data in the mqdata table. You can also run the command on the [MaxCompute client](../../../../intl.en-US/Tools and Downloads/Client.md#) to view the data and perform subsequent steps.

Verify that the data imported to the table is correct. You can then use the built-in string function [GET\_JSON\_OBJECT](../../../../intl.en-US/User Guide/SQL/Builtin functions/String functions.md#section_cdt_gxz_vdb) in MaxCompute to obtain the JSON data as needed. Use `SELECT GET_JSON_OBJECT(mqdata.MQdata,'$.expensive') FROM mqdata;` to obtain the value of expensive in the JSON file. Then, the data is obtained successfully, as shown in the following figure.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/62284/154382203831553_en-US.png)

