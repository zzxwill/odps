# Access services and perform analysis {#concept_znk_gyt_z2b .concept}

After the connection service is successful, you can view the data table under the specified MaxCompute project for BI analysis.

1.  Select PostgreSQL when establishing a connection to a server.

    Start Tableau Desktop. In the left-side navigation pane, select **Connection** \> **To Servers** \> **More** \> **PostgreSQL**.

2.  Enter service connection and user authentication information.

    |Parameter|Description|
    |:--------|:----------|
    |Server|Enter the MaxCompute Lightning endpoint of a specified region in the Server field. For example, enter the value `lightning.cn-shanghai.maxcompute.aliyun.com` as the endpoint for the China East 2 region.|
    |Port|443|
    |Database|MaxCompute project name|
    |ID Verification|User name and password|
    |Username/Password|User Access Key ID/Access Key Secret|
    |SSL connection|Select the **SSL** connection check box.|

3.  Obtain project table information and create a data source or model.

    After you configure the contact information and log on to the Tableau Desktop, this software loads tables of the connected MaxCompute project. You can choose tables to create data models and charts as required.

    The following figure shows an example of a chart created based on required dimensions and measures.


Now you have gained access to MaxCompute Lightning using Tableau Desktop. You can perform BI analysis on the data of the connected MaxCompute projects.

**Note:** For better performance, it is recommended that you customize the connection to the Lightning data source using the TDC file supported by Tableau. For more information, see [Tableau Desktop](reseller.en-US/User Guide/Lightning/ Access services using JDBC interfaces/Access services using common tools.md#section_p1m_3yk_z2b).

