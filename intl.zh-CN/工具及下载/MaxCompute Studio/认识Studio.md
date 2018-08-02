# 认识Studio {#concept_bmm_thx_5db .concept}

MaxCompute Studio是阿里云MaxCompute平台提供的安装在开发者客户端的大数据集成开发环境工具，是一套基于流行的集成开发平台[IntelliJ IDEA](https://www.jetbrains.com/idea/)的开发插件，可以帮助您方便地进行数据开发。本文将为您介绍MaxCompute Studio的功能界面和常用的应用场景。

## 基本用户界面 {#section_u5b_dvx_5db .section}

MaxCompute Studio是IntelliJ IDEA平台上的一套插件，共享了IntelliJ IDEA的基本开发界面。IDEA的界面详情请参见[界面操作文档](https://www.jetbrains.com/help/idea/2016.3/guided-tour-around-the-user-interface.html)。

MaxCompute Studio在IntelliJ的基础上提供了以下功能界面。

-   **SQL编辑器（SQL Editor）**：提供SQL语法高亮、代码补全、实时错误提示、本地编译、作业提交等功能。

    编译器视图（Compiler View）：显示本地编译的提示信息和错误信息，在编辑器中定位代码。

-   **项目空间浏览器（Project Explorer\)**：连接MaxCompute项目空间，浏览项目空间表结构、自定义函数、资源文件。

    表详情视图（Table Details View）：提供表、视图等资源的详情显示和示例数据（Sample Data）。

-   **作业浏览器（Job Explorer）**：浏览、搜索MaxCompute的历史作业信息。
    -   作业详情视图（Job Details View）：显示作业的运行详细信息，包括执行计划和每个执行任务的详细信息。
    -   作业输出视图（Job Output View）：显示正在运行的作业的输出信息。
    -   作业结果视图（Job Result View）：显示SELECT作业的输出结果。
-   **MaxCompute控制台（MaxCompute Console）**：集成了[MaxCompute客户端](intl.zh-CN/工具及下载/客户端.md)，可以输入和执行MaxCompute客户端命令。

## 连接MaxCompute项目空间 {#section_z5b_dvx_5db .section}

Studio的大部分功能需要您首先[创建项目空间连接](intl.zh-CN/工具及下载/MaxCompute Studio/项目空间连接管理.md)。建立项目空间连接后，即可在**项目空间浏览器**中查看相关的数据结构和资源信息。Studio会自动为每一个项目空间连接建立一个本地的元数据备份，以提高对MaxCompute元数据的访问频率和降低延时。

**说明：** 

-   您需要指定作为目标的项目连接，方可通过Studio进行编辑SQL脚本、提交作业、查看Job信息、打开MaxCompute控制台等操作，因此首先创建一个MaxCompute项目空间的连接是非常必要的。
-   MaxCompute项目空间的更多详情请参见[项目空间](../../../../intl.zh-CN/产品简介/基本概念/项目空间.md)。
-   在Studio中管理项目空间的更多详情请参见[项目空间连接](intl.zh-CN/工具及下载/MaxCompute Studio/项目空间连接管理.md)。

## 管理数据 {#section_bvb_dvx_5db .section}

您可以通过Studio的**项目空间浏览器**快速浏览项目空间的表结构、自定义函数、资源文件。通过树形控件，可以列出所有项目空间连接下的数据表、列、分区列、虚拟视图、自定义函数名称、函数签名、资源文件及类型等，并支持快速定位。

您双击某个数据表，即可打开**表详情视图**，查看数据表的元信息、表结构和示例数据。如果您没有项目空间的相应权限，Studio会提示对应的错误信息。

Studio集成了[MaxCompute Tunnel](../../../../intl.zh-CN/用户指南/数据上传下载/Tunnel命令操作.md)工具，可以支持本地数据的上传和下载，更多详情请参见[导入并导出数据](intl.zh-CN/工具及下载/MaxCompute Studio/管理数据和资源/导入导出数据.md)。

## 编写SQL脚本 {#section_cvb_dvx_5db .section}

您可以在Studio中编写MaxCompute SQL脚本，非常方便。

1.  打开Studio，导航至**File** \> **New** \> **Project**或者**File** \> **New** \> **Module…**。
2.  创建一个MaxCompute Studio类型的项目或者模块。
3.  导航至**File** \> **New** \> **MaxCompute Script** 或者右击菜单**New** \> **MaxCompute Script**，即可创建一个MaxCompute SQL脚本文件。

    **说明：** 创建MaxCompute SQL脚本时，Studio会提示您选择一个关联的MaxCompute项目空间，您也可以通过SQL编辑器上的工具条最右侧的**项目空间选取器**进行更改，编辑器会根据SQL脚本关联的项目空间对SQL语句自动进行元数据（比如表结构等）的检查并汇报错误，提交运行时也会发送到关联的项目空间执行。更多详情请参见[编写SQL脚本](intl.zh-CN/工具及下载/MaxCompute Studio/开发 SQL 程序/编写SQL脚本.md)。


## SQL代码智能提示 {#section_gvb_dvx_5db .section}

Studio提供的SQL编辑器可以根据您写入的代码，智能提示SQL语句的语法错误、类型匹配错误或者警告等，实时地标注在代码上。如下图所示：

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/12114/1433_zh-CN.png)

通过代码补全功能，Studio可以根据代码上下文，提示您项目空间名称、表、字段、函数、类型、代码关键词等，并根据您的选择，自动补全代码。如下图所示：

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/12114/1434_zh-CN.png)

## 编译和提交作业 {#section_jvb_dvx_5db .section}

-   编译作业

    单击SQL编辑器工具条上的![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/12114/6004_zh-CN.png) 图标，可以对SQL脚本执行本地编译，如果有语法或者语义错误，编译器窗口会报告错误。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/12114/1436_zh-CN.png)

-   提交作业

    单击SQL编辑器工具条上的![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/12114/1437_zh-CN.png)图标，会在本地编译之后，把SQL脚本提交到MaxCompute指定的项目空间排队执行。


## 查看历史作业 {#section_nvb_dvx_5db .section}

打开**作业浏览器**，您即可查看指定项目空间上近期执行的作业。

**说明：** 这个列表只能显示以当前连接使用的用户ID提交的作业。

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/12114/1438_zh-CN.png)

双击其中一个作业，便可查看作业的的详情信息。如下图所示：

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/12114/1439_zh-CN.png)

如果知道一个任务的Logview URL，可以导航至**MaxCompute** \> **Open Logview**，打开该任务的详情页面。

## 开发MapReduce和UDF {#section_pvb_dvx_5db .section}

Studio还支持[MapReduce](intl.zh-CN/工具及下载/MaxCompute Studio/开发 Java 程序/开发MapReduce.md)和[Java UDF](intl.zh-CN/工具及下载/MaxCompute Studio/开发 Java 程序/开发和调试UDF.md)的开发。

## 连接MaxCompute客户端 {#section_qvb_dvx_5db .section}

Studio集成了最新版本的MaxCompute[客户端](intl.zh-CN/工具及下载/客户端.md)，您也可以在Studio的[配置页面](intl.zh-CN/工具及下载/MaxCompute Studio/配置选项/配置 MaxCompute Studio.md)中指定本地已经安装好的MaxCompute客户端路径。

您在**项目空间浏览器**中选定一个项目空间，右键单击菜单选择**Open in Console**即可打开MaxCompute控制台窗口。

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/12114/1440_zh-CN.png)

## 后续步骤 {#section_svb_dvx_5db .section}

现在，您已经学习了MaxCompute Studio的功能界面和常用的应用场景，您可以继续学习下一个教程。在该教程中您将学习如何安装MaxCompute Studio。详情请参见[安装IntelliJ IDEA](intl.zh-CN/工具及下载/MaxCompute Studio/工具安装与版本信息/安装IntelliJ IDEA.md)。

