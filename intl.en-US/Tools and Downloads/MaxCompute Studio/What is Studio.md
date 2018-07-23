# What is Studio {#concept_bmm_thx_5db .concept}

MaxCompute Studio is a big data integrated development environment \(IDE\) tool that is provided by the Alibaba Cloud MaxCompute platform and installed on the developer’s client. It is a development plug-in based on the popular integrated development platform [IntelliJ IDEA](https://www.jetbrains.com/idea/), helping users develop data conveniently. This article describes functional interfaces and common application scenarios of MaxCompute Studio.

## Basic user interface {#section_u5b_dvx_5db .section}

MaxCompute Studio is a plug-in on the IntelliJ IDEA platform, which shares basic development interfaces with IntelliJ IDEA. For more information about the IntelliJ IDEA interfaces, see [the Interface operation guide](https://www.jetbrains.com/help/idea/2016.3/guided-tour-around-the-user-interface.html).

Based on the IntelliJ IDEA interfaces, MaxCompute Studio provides the following functional interfaces.

-   **SQL Editor**: Provides features such as SQL syntax highlighting, code complementing, real-time error prompting, local compilation, and job submission.

    Compiler View: Displays locally compiled prompts and error messages, and locates the code in the editor.

-   **Project Explorer**: Connects to a MaxCompute project, and browses table structures, custom functions, and resource files in the project.

    Table Details View: Displays details and sample data of tables, views, and other resources.

-   **Job Explorer**: Browses and searches for historical jobs of MaxCompute.
    -   Job Details View: Displays running details of a job, including the execution plan and details of each execution task.
    -   Job Output View: Displays output information of a running job.
    -   Job Result View: Displays the output result of the SELECT job.
-   **MaxCompute Console**: Integrates the [MaxCompute client](intl.en-US/Tools and Downloads/Client.md), on which MaxCompute client commands can be input and executed.

## Connect to MaxCompute project {#section_z5b_dvx_5db .section}

Before using most features of MaxCompute Studio, you must [Create a project connection](intl.en-US/Tools and Downloads/MaxCompute Studio/Project space connection management.md). After the project connection is created, you can view related data structures and resource information in the **Project Explorer**. MaxCompute Studio automatically creates a local metadata backup task for each project to increase the access frequency to MaxCompute metadata and reduce the latency.

**Note:** 

-   You must specify the target project connection to modify SQL scripts, submit jobs, view job information, open the MaxCompute console, and implement other functions using MaxCompute Studio. Therefore, creating a connection to the MaxCompute project is necessary.
-   For more information about MaxCompute projects, see [Project](../../../../intl.en-US/Product Introduction/Definition/Projects.md).
-   For more information about project management using MaxCompute Studio, see [Project space connection management](intl.en-US/Tools and Downloads/MaxCompute Studio/Project space connection management.md).

## Manage data {#section_bvb_dvx_5db .section}

You can use the **Project Explorer** of MaxCompute Studio to quickly browse table structures, custom functions, and resource files in the project. The tree control can be used to list data tables, columns, partition columns, virtual views, custom functions, function signatures, and resource files and types of all project connections. It also supports fast locating.

You can double-click a data table to open the **Table Details View** and view metadata, structure, and sample data of the data table. If you do not have the permission for a project, an error message is prompted.

MaxCompute Studio integrates [MaxCompute Tunnel](../../../../intl.en-US/User Guide/Data upload and download/Tunnel commands.md) and supports local data upload and download. For more information, see [Import and export data](intl.en-US/Tools and Downloads/MaxCompute Studio/Manage data and resources/Import and export data.md).

## Write SQL scripts {#section_cvb_dvx_5db .section}

You can easily compile a MaxCompute SQL script on MaxCompute Studio.

1.  Open MaxCompute Studio and select **File** \> **New** \> **Project** or **File** \> **New** \> **Module…**.
2.  Create a MaxCompute Studio project or module.
3.  Select **File** \> **New** \> **MaxCompute Script** or right-click the menu and select **New** \> **MaxCompute Script** , to create a maxcompute SQL script file.

    **Note:** When a MaxCompute SQL script is created, MaxCompute Studio prompts you to select an associated MaxCompute project. You can also modify the associated project using the **project selector** on the right of the toolbar on the SQL editor. The editor automatically checks metadata \(such as the table structure\) and reports errors of an SQL statement based on the project associated with the SQL script. The editor also sends the SQL statement to the associated project for execution when it submits the SQL statement for running. For more information, see [Compile an SQL script](intl.en-US/Tools and Downloads/MaxCompute Studio/Develop SQL procedure/Write SQL scripts.md).


## SQL code intelligent prompt {#section_gvb_dvx_5db .section}

After you enter the code, the SQL editor provided by MaxCompute Studio intelligently prompts the syntax errors, type matching errors, or warnings of SQL statements, and marks them on the code in real time. as shown in the following figure.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/12114/1433_en-US.png)

By using the code complementing function, MaxCompute Studio prompts you the name, table, field, function, type, and code keyword of a project based on the code context, and automatically complements the code based on your selections. as shown in the following figure.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/12114/1434_en-US.png)

## Compile and submit a job {#section_jvb_dvx_5db .section}

-   Compile a job

    Click the ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/12114/6004_en-US.png) icon on the toolbar of the SQL editor to locally compile an SQL script. If syntax or semantic errors occur, the editor reports it.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/12114/1436_en-US.png)

-   Submit a job

    Click the ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/12114/1437_en-US.png) icon on the toolbar of the SQL editor to submit an SQL script to the queue of the project specified by MaxCompute.


## View history jobs {#section_nvb_dvx_5db .section}

Open **Job Explorer** to view recently executed jobs in the specified project.

**Note:** List only displays jobs submitted by the user ID of the current connection.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/12114/1438_en-US.png)

Double-click a job to view the job details. as shown in the following figure.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/12114/1439_en-US.png)

If you have the Log view URL of a job, you can select **MaxCompute** \> **Open Logview** from the menu to go to the details page of the job.

## Develop a MapReduce program and UDF program {#section_pvb_dvx_5db .section}

MaxCompute Studio also allows you to develop [MapReduce](intl.en-US/Tools and Downloads/MaxCompute Studio/Developing Java/Develop MapReduce.md) and [Java UDF](intl.en-US/Tools and Downloads/MaxCompute Studio/Developing Java/Develop and debug UDF.md) programs.

## Connect to a MaxCompute client {#section_qvb_dvx_5db .section}

MaxCompute Studio is integrated with the [MaxCompute Client](intl.en-US/Tools and Downloads/Client.md) of the latest version. Alternatively, you can specify the path of the locally installed MaxCompute client on the [Configuration page](intl.en-US/Tools and Downloads/MaxCompute Studio/Configure options/Configure MaxCompute Studio.md) of MaxCompute Studio.

On the **Project Explorer**, right-click a project and select **Open in Console** to open the MaxCompute Console window.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/12114/1440_en-US.png)

## Next step {#section_svb_dvx_5db .section}

Now, you know the functional interfaces and common application scenarios of MaxCompute Studio. Continue to the next tutorial. In this tutorial, you will learn how to install MaxCompute Studio. For more information, see [Install IntelliJ IDEA](intl.en-US/Tools and Downloads/MaxCompute Studio/Tools Installation and version history/Install IntelliJ IDEA.md).

