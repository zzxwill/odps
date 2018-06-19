# Visualization of operating the tables {#concept_ngz_dg2_vdb .concept}

The Project Explorer of MaxCompute Studio provides the visualized table structure editor used to create and modify tables.

## Visualization of creating a table {#section_nqb_c32_vdb .section}

**Procedure**

1.  Right-click the target project and select **Create a new table**.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/12123/1707_en-US.png)

2.  In the dialog box that appears, enter a table name and column information. Click **Generate CreateTable  Statement**to generate a DDL statement. Click **Execute** to create the table.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/12123/1708_en-US.png)

    When you set the table name, column name, type, and lifecycle, observe the related requirements of MaxCompute. For more information, see [the DDL documentation](../../../../intl.en-US/User Guide/SQL/DDL SQL.md).

3.  After the table is created, view the table metadata in Tables & Views of the Project Explorer. If no metadata is displayed, refresh the list.


## Visualization of modifying a table {#section_rqb_c32_vdb .section}

**Procedure**

1.  In Tables & Views of the Project Explorer, right-click the expected table and select **Open table  editor**:

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/12123/1710_en-US.png)

2.  In the dialog box that appears, edit the table. You can modify the table comments, lifecycle, column name and description, and add columns.  When you edit the table, observe the table-related requirements of MaxCompute.  For more information, see [the DDL documentation](../../../../intl.en-US/User Guide/SQL/DDL SQL.md).

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/12123/1711_en-US.png)

3.  After you finish modification, click **Alter Table Statement** to generate an ALTER statement. Click **Execute** to apply the modification.  After successful execution, view the table metadata.


## Visualization of deleting a table {#section_vqb_c32_vdb .section}

In Tables & Views of the Project Explorer, right-click the expected table and select **Drop table from server**.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/12123/1712_en-US.png)

In the dialog box that appears, click **OK**. Then, the table is deleted from the MaxCompute instance.

