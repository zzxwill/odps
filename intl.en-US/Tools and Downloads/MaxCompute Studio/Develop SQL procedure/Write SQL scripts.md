# Write SQL scripts {#concept_nvh_s3d_5db .concept}

[MaxCompute Studio module](intl.en-US/Tools and Downloads/MaxCompute Studio/Develop SQL procedure/Create MaxCompute Script module.md) is created, you can compile a MaxCompute SQL script.

## Procedure {#section_vpm_rlf_vdb .section}

1.  Right-click **scripts** and select **New** \> **MaxCompute Script** .

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/12126/1845_en-US.png)

2.  In the dialog box that appears, specify related content and click **OK**.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/12126/1846_en-US.png)

    -   Script Name: Indicates the script name.
    -   Script Type: Indicates the script type.
    -   Target Project: Indicates the target MaxCompute project. In the preceding dialog box, you can click **+** next to Target Project  to create a MaxCompute project. For more informatioan about on how to configure a MaxCompute project, see [Create a project connection](intl.en-US/Tools and Downloads/MaxCompute Studio/Project space connection management.md).
3.  Compile an SQL script on the SQL file editing page.

    **Note:** 

    1.  Compile the SQL script based on the tables of your MaxCompute project.  You can click the upper-right corner on the toolbar to switch to different bound  MaxCompute projects. Cross-project resource dependency is supported.  For example, if a script bound to Project A uses Project B Table1,  MaxCompute Studio automatically uses the account of Project A to capture the metadata of Project B.  MaxCompute Studio  stores the metadata of the table in a local directory similar to the following figure.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/12126/1850_en-US.png)

    2.  You can modify the code template used to compile an SQL script on the Intellij Preferences page.

## Functions of MaxCompute Studio {#section_czx_y4f_vdb .section}

MaxCompute Studio supports the syntax highlighting, smart reminders, and error prompting functions. It also supports:

-   **Schema annotator**: When you place the cursor over a table, its schema is displayed. When you place the cursor over a function, its signature is displayed.
-   **Code folding**: You can fold subqueries to read long SQL scripts.
-   **Brace matching**: If you click a left brace to highlight it, the matching right brace is also highlighted. If you click a right brace to highlight it, the matching left brace is highlighted.
-   **Go to declaration**: Press Ctrl and click **table** to view table  details.  Click **function** to view the source code.
-   **Code formatting**: Supports formatting for the current script. The keyboard shortcut is Ctrl+Alt+ L \). You can create custom formatting rules on the following page, such as keyword case sensitivity and line break.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/12126/1853_en-US.png)

-   **Code inspect**: Supports code checking for the current script, and some checks also provide quick fix, which can be used by pressing Alt + Enter. Alternatively, you can modify a rule through Preference - Editor - Inspections - MaxCompute.
-   **Find usages**: Select a table or function in the editor, right-click the table, and select **Find Usages**.  All scripts using the table or function will be searched for in the current IntelliJ project.
-   **Live template**: MaxCompute Studio has built-in SQL live templates, which can be opened by pressing Ctrl+J \(Command +J on macOS X\) in the compiler. For example, if you forget the syntax of insert into table, you can open the live template dialog box and search for  Insert table \).
-   **Built-in documentation**: Supports opening the help documentation by pressing Ctrl+Q \(Ctrl+J on macOS  X\).
-   **SQL history**: All the SQL statements submitted using the MaxCompute Studio are stored locally. Click an icon on the toolbar, and the SQL  History window appears, listing the SQL statements that have been executed.

