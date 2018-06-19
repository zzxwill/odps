# View tables and UDF {#concept_cbj_cmz_5db .concept}

## View tables and functionsView tables and functions {#section_wf5_mmz_5db .section}

In the **Project Explorer window**, you can view tables, functions, and resources with connections added.  For tables and functions to be viewed in the Project Explorer window, the MaxCompute project connections must be added, for more information, see [Add MaxCompute project connections](intl.en-US/Tools and Downloads/MaxCompute Studio/Project space connection management.md).

## Browse tables and functions {#section_xf5_mmz_5db .section}

To browse tables and functions in the project space, follow these steps.

1.  Open the **Project Explorer window** and you can view the added Project node tree.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/12121/1621_en-US.png)

    The toolbar is displayed at the top of the node tree, and includes:

    -   **Add Project**: Adds a connection to the MaxCompute project space.
    -   **Delete Project**: Deletes a connection from Project Explorer, which has no impact on the project space on the server end.
    -   **Update Metadata**: Updates metadata information from the project space on the server end and updates the locally buffered metadata.
    -   **Expand Node**: Expands all tree nodes.
    -   **Fold Node**: Folds all tree nodes.
    -   **User Feedback**: Submits user feedback.
    -   **Online Documentation**: Opens online documents.
2.  Double-click the **Tables** node or click the drop-down arrow to expand the Tables  node to list all tables in the project \(including virtual views\).  The table name list serves the same purpose as the show tables command. You must have the List  Table permission in the project.  The methods for the **Functions** and **Resources** nodes are similar to that of the Tables node.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/12121/1623_en-US.png)

3.  MaxCompute Studio downloads project metadata on the server to the local device. When metadata on the server end is updated, for example, a new table is added, you must manually trigger a refresh to reload changed metadata to the local device.  The refresh can be performed at the Project or Table level. The procedure is as follows:

    1.  Select a node.

    2.  Click the Refresh icon on the toolbar or right-click the node and select Refresh meta.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/12121/1624_en-US.png)


## View table details {#section_dg5_mmz_5db .section}

You can view data table information in Table Details View of MaxCompute Studio.

1.  In the node tree, expand a table node to view the column name and type.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/12121/1625_en-US.png)

2.  Double-click a table or right-click a table and choose **Show Table Detail** to view the table details.  The table details include metadata, such as owner, size, and column, table structure information, and data preview.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/12121/1626_en-US.png)

3.  Right-click **Tables & Views** and select **Open specific  entity ** to display the details of the specific table. Note that the complete table name must be specified.  If you do not have the List permission on the project and only have the permission on a specific table, you can also view details of the table using this method.  The methods for the Functions and Resources nodes are similar to that of the Tables node.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/12121/1627_en-US.png)

    Intellij IDEA supports searching by default. After a table is expanded, you can directly press keys on the keyboard to perform fuzzy match.

4.  MaxCompute Studio also supports quick search for the table, you can use the shortcut key \(Windows: Ctrl + Alt + Shift + N, macOS: ⌥ + ⌘ + O\) to call the navigation  bar, then enter the name of the table and press Enter. 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/12121/1628_en-US.png)

    You can narrow the search by using the pre-keyword \(table:, function:, or resource:\). For example, to search for the function count, enter function:count.

5.  To know the scripts in which the table is used, right-click the table and select Find usages.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/12121/1629_en-US.png)


## View function details {#section_wss_3pz_5db .section}

1.  Expand a function node under the UserDefined node of **Functions**   to display the method signature of this function. Double-click a function node under the Functions node. Alternatively, double-click the source code resource of the function under the **Resources** node.  In this case, codes of this function are displayed.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/12121/1630_en-US.png)

    **Note:** The Java code is obtained by decompiling JAR, which is not the source code. To enable the Python UDF to parse the signature, install PyODPS \(MaxCompute Python  SDK\) first. Install pip: sudo/usr/bin/python get-pip.py  \(Download get-pip.py from Google manually\) and then PyODPS: sudo/usr/bin/python -m pip install  PyODPS. Note that the Mac operating system has Python, which is stored in /usr/bin/python. Install PyODPS in this directory.

2.  The classification under the BuiltIn node of  **Functions**  shows the built-in functions of the system, expand it to display signature and double-click it to display function document.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/12121/1631_en-US.png)


