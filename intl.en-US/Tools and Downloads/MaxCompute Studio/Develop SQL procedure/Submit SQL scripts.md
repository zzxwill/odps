# Submit SQL scripts {#concept_iwn_lsf_vdb .concept}

MaxCompute Studio directly submits MaxCompute SQL scripts  to the server for running, and displays detailed information about the query result and execution plan.  Before submission, MaxCompute Studio compiles scripts to effectively prevent compilation errors that are detected after the scripts are submitted to the server.

## Prerequisite {#section_zrx_zsf_vdb .section}

-   [Create a MaxCompute  project connection](reseller.en-US/Tools and Downloads/MaxCompute Studio/Project space connection management.md) and bind it to the target project.

-   Create a [MaxCompute Studio  module](reseller.en-US/Tools and Downloads/MaxCompute Studio/Develop SQL procedure/Create MaxCompute Script module.md).

-   Before submission, perform setting as required. MaxCompute Studio provides various setting features. You can perform quick setting on the toolbar  at the top of the editor page. The following three types of setting can be performed:

    -   **Compiler Mode**: It can be set to Script Mode or Statement Mode.

        -   In statement mode, scripts are separated by `;` and submitted to the server one by one.

        -   The script mode is newly developed. A whole script can be submitted to the server immediately. The server provides overall optimization, which is more efficient. Therefore, this mode is recommended.

    -   **Type System**: It mainly solves the compatibility problem of SQL statements, which can be set to the following values:

        -   Legacy TypeSystem: Indicates the type system of original MaxCompute.

        -   MaxCompute TypeSystem: Indicates the new type system introduced by MaxCompute 2.0.

        -   Hive Compatible TypeSystem: Indicates the type system in Hive compatibility mode introduced by MaxCompute 2.0.

    -   **Compiler Version**: MaxCompute Studio provides the stable compiler and experimental compiler.

        -   Default Version: Indicates the stable version.

        -   Flighting Version: Includes the latest features of the compiler.

        **Note:** You can use **Global Settings** to set the submitted scripts. Select **File \> ** \> **Settings \> ** \> **MaxCompute** , select **MaxCompute SQL**, and choose  **Compiler \> ** \> **Submit ** to set the preceding attributes.


## Submit SQL scripts {#section_pyn_r5f_vdb .section}

  The top toolbar of the editor provides the Synchronize and Compile and Submit features.

```
* **Synchronize**: Updates metadata in SQL scripts, including table names and UDFs.  If MaxCompute Studio prompts that a table or function cannot be found, but the table or function obviously exists on the server, you can use this function to update metadata.
* **Compile and Submit**: SQL scripts are compiled or submitted to the server in compliance with pre-released MaxCompute SQL rules. Details of compilation errors are displayed in the **MaxCompute Compiler** window.
```

**Procedure**

1.  After SQL statements are compiled, click the green running icon on the toolbar, or right-click **Script  Editor** and select **Run MaxCompute SQL Script**  to submit the SQL statement to the server. If a variable exists in the SQL statement \(such as $\{bizdate\} in the following figure\), a dialog box is displayed, prompting you to enter the variable value.

2.  The script will be locally compiled \(depending on the project metadata you added in the **Project  Explorer** window\). If no compilation error exists, the script is submitted to the server for execution.  When the SQL script is being executed, the running logs are displayed. If the script is running on the server, the Job  Details page is displayed, showing the basic information about job running and the execution diagram.

3.  You can view SQL results on the Results page. If there are multiple statements in the single-sentence mode, the result of each statement is displayed.  You can select rows or columns in the table, and copy them to the Clipboard.


