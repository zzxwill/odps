# Access services using common tools {#concept_tvz_bb5_z2b .concept}

The following sections use major client tools, such as SQL Workbench/J, PSQL, and Tableau BI tools, as examples to describe how to access MaxCompute Lightning. Other commonly used tools can be connected to MaxCompute Lightning in the same way as to PostgreSQL databases.

## Alibaba Cloud Quick BI {#section_ldv_ylk_z2b .section}

1.  Log on Quick BI console, click **Data source** in the left-side navigation pane.
2.  On the data source management page, click the **Create data source** in the upper-right corner.
3.  Select PostgreSQL in the cloud database or external data source, and add a data source.
4.  In the dialog box that appears, enter the connection information for MaxCompute Lightning. Then, test the connection.

    |Parameter|Description|
    |:--------|:----------|
    |Database address|Enter the endpoint for the region of MaxCompute Lightning. You can enter the endpoint for a public network, classic network, or VPC network.|
    |Database|Enter the name of the to-be-accessed MaxCompute project followed by `? ssl=true`, for example, `lightning? ssl=true` in the previous figure.|
    |Schema|MaxCompute project name|
    |User name/Password|User Access Key ID/Access Key Secret.|


## SQL Workbench/J {#section_smd_xqk_z2b .section}

SQL Workbench/J is a widely used free and cross-platform SQL query tool. This tool can be connected to MaxCompute Lightning using the PostgreSQL driver.

1.  [Download](http://www.sql-workbench.eu/downloads.html) and install SQL Workbench/J.
2.  Start SQL Workbench/J. 1, establish a database connection.

    Select the PostgreSQL driver, connect SQL Workbench/J to the MaxCompute Lightning URL of a project. You must enter the Access Key ID and Access Key Secret of the user.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/20126/153631019111161_en-US.jpg)

    Alternatively, you can click Extended Properties and set ssl to true in the displayed dialog box.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/20126/153631019211162_en-US.jpg)

3.  After SQL Workbench/J is connected to MaxCompute Lightning, you can view, query, and analyze the table data in the SQL Workbench/J workspace.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/20126/153631019211163_en-US.jpg)


## psql {#section_fs1_kvk_z2b .section}

The psql is a PostgreSQL interactive terminal that enables you to perform queries using commands. The clients of psql are installed by default when PostgreSQL databases are installed in a local PC.

You can connect psql to MaxCompute Lightning using psql commands. The syntax for the connection is the same as that for the connection to the PostgreSQL database.

```
psql -h &lt;endpoint> -U &lt;userid> -d &lt;databasename> -p &lt;port>
```

Parameter description:

-   &lt;endpoint\>: The endpoint of MaxCompute Lightning. For more information, see [Access domain name](intl.en-US/User Guide/Lightning/Access domain name.md#).
-   &lt;userid\>: Access Key ID.
-   &lt;databasename\>: MaxCompute project name.
-   &lt;port\>: 443

After the command is executed, enter the &lt;userid\> password \(Access Key Secret\) in the command prompt.

Example:

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/20126/153631019211164_en-US.jpg)

**Note:** SSL connections are preferred for psql by default.

## Tableau Desktop {#section_p1m_3yk_z2b .section}

Start BI tools, select the PostgreSQL data source, and configure the connection.

When you configure the connection, select the **SSL Connection** check box.

After logging on to Tableau Desktop, you can create charts for visual analysis.

**Note:** For better performance, it is recommended that you customize the connection to the Lightning data source using the TDC file supported by Tableau. Procedure:

1.  Save the following xml content as a postgresql.tdc file.

    ```
    &lt;? xml version='1.0' encoding='utf-8'? >
    &lt;connection-customization class='postgres' enabled='true' version='8.10'>
    &lt;vendor name='postgres'/>
    &lt;driver name='postgres'/>
    &lt;customizations>
    &lt;customization name='CAP_CREATE_TEMP_TABLES' value='no' />
    &lt;customization name='CAP_STORED_PROCEDURE_TEMP_TABLE_FROM_BUFFER' value='no' />
    &lt;customization name='CAP_CONNECT_STORED_PROCEDURE' value='no' />
    &lt;customization name='CAP_SELECT_INTO' value='no' />
    &lt;customization name='CAP_SELECT_TOP_INTO' value='no' />
    &lt;customization name='CAP_ISOLATION_LEVEL_SERIALIZABLE' value='yes' />
    &lt;customization name='CAP_SUPPRESS_DISCOVERY_QUERIES' value='yes' />
    &lt;customization name='CAP_SKIP_CONNECT_VALIDATION' value='yes' />
    &lt;customization name='SQL_TXN_CAPABLE' value='0' />
    &lt;/customizations>
    &lt;/connection-customization>
    ```

2.  Save the file to the \\My Documents\\My Tableau Repository\\Datasources directory. If it is Tableau Server, save it in C:\\ProgramData\\Tableau\\Tableau Server\\data\\tabsvc\\vizqlserver\\Datasources under Windows, and save it in /var/opt/tableau/tableau\_server/data/tabsvc/vizqlserver/Datasources/ under Linux. .
3.  Reopen Tableau and use the PostgreSQL data source to connect to the MaxCompute Lightning service. For more information about custom data sources for tdc files, see [official Tableau documentation](https://onlinehelp.tableau.com/current/pro/desktop/en-us/odbc_customize.html#global_tdc).

## FineReport {#section_e2l_xyk_z2b .section}

1.  Start FineReport, and select **Server** \> **Define database connection**.
2.  Add a JDBC connection.

    The configurations are described as follows:

    |Parameter|Description|
    |:--------|:----------|
    |Database|Postgre|
    |Driver|org.postgresql.Driver that is integrated in FineReport|
    |URL| `jdbc:postgresql://&lt;MaxCompute Lightning Endpoint>:443/&lt;Project_Name>? ssl=true&amp;prepareThreshold=0`

 For example, `jdbc:postgresql://lightning.cn-shanghai.maxcompute.aliyun.com:443/lightning_demo? ssl=true&amp;prepareThreshold=0`

 |
    |User name/Password|User Access Key ID and Access Key Secret|


