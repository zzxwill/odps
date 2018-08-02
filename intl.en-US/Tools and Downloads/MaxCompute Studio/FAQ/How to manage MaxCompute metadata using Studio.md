# How to manage MaxCompute metadata using Studio {#concept_p3g_jm5_vdb .concept}

During routine MaxCompute usage, you must browse and manage metadata \(including tables, functions, and resources\) of projects. The following describes how to browse and manage metadata using MaxCompute Studio.

-   **Add a project connection.**  For more information, see [Add connections](intl.en-US/Tools and Downloads/MaxCompute Studio/Project space connection management.md). Add a MaxCompute project connection in the Project Explorer window of MaxCompute.  After the connectivity test is successful, the added project node tree can be viewed.
-   **View the table list and schema** . For more information, see [Browse meta](intl.en-US/Tools and Downloads/MaxCompute Studio/Manage data and resources/View tables and UDF.md) . Expand Tables & Views in Project to view the table and view lists. Expand a specific table to view the column and type.
-   **Query the function code. ** Expand Functions to view the function list. Expand a specific function to view the method signature. Double-click the function to view the decompiled source code.
-   **View the sample data of a downloaded table**. For more information, see [Import and export table data](intl.en-US/Tools and Downloads/MaxCompute Studio/Manage data and resources/Import and export data.md). Double-click a table to view the detailed schema. In the Data  Preview window, right-click **Export Grid   Data** or right-click a table name and select Export to preview sample data.
-   **Update node metadata**. For example, if a column is added to a table, right-click the table and select **Refresh  Meta** or click **Refresh** on the toolbar to view the added column.  If tables are added in a project, select **Tables  & Views** and click **Refresh** on the toolbar to view the newly created tables.
-   **Perform the DDL operation on tables:**. The deletion operation can be performed in batches. Right-click the selected table and select **Delete table from  server**.  Table addition and edition cannot be performed on the interface. You can compile corresponding DDL statements in the editor to complete table addition and edition.

