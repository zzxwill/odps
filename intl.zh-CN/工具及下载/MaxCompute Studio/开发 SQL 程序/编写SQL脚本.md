# 编写SQL脚本 {#concept_nvh_s3d_5db .concept}

[MaxCompute Studio模块创建完成后](intl.zh-CN/工具及下载/MaxCompute Studio/开发 SQL 程序/创建MaxCompute Script Module.md)，即可开始编写MaxCompute SQL脚本。

## 操作步骤 {#section_vpm_rlf_vdb .section}

1.  右击**scripts** ，导航至**New** \> **MaxCompute Script** 。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/12126/15444291531845_zh-CN.png)

2.  填写弹出框中的相关内容，点击**OK**。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/12126/15444291531846_zh-CN.png)

    -   Script Name：脚本名称。
    -   Script type：脚本类型。
    -   Target Project：目标MaxCompute项目。单击后面的**+**即可新建一个MaxCompute Project，配置详情请参见 [新建项目空间连接](intl.zh-CN/工具及下载/MaxCompute Studio/项目空间连接管理.md)。
3.  在SQL文件编辑界面中，编写SQL。

    **说明：** 

    1.  实际SQL请根据自己的MaxCompute Project中的表进行编写。可单击toolbar右上角切换绑定的不同的MaxCompute项目，也支持跨project资源依赖。例如script绑定了ProjectA，同时还会用到ProjectB.table1，这时Studio会自动使用ProjectA的账号去抓取ProjectB的元数据。表的元数据Studio会保存在本机中类似下图的位置：

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/12126/15444291531850_zh-CN.png)

    2.  新建sql script时的代码模板是可以在Intellij Preferences页修改的：

## MaxCompute Studio功能 {#section_czx_y4f_vdb .section}

MaxCompute Studio不仅提供语法高亮，智能提醒，错误提示，还支持以下功能：

-   **schema annotator**： 当鼠标悬停在表上，可显示其schema。悬停在列上，可显示其类型；悬停在函数上，可显示其签名。
-   **code folding**： 可以将子查询等折叠起来，方便长SQL的阅读。
-   **brace matching**：鼠标单击高亮左括号，其匹配的右括号也会高亮，反之亦然。
-   **go to declaration**：按住Ctrl键，单击**table** ，即可查看table详情。单击**function** ，即可显示其源码。
-   **code formatting**：支持对当前脚本格式化，快捷键（Ctrl + Alt + L）。可在如下页面自定义格式化规则，譬如关键字大小写，是否换行等。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/12126/15444291531853_zh-CN.png)

-   **code inspect**：支持对当前脚本进行代码检查，某些检查还会提供quick fix，可通过快捷键Alt + Enter唤出。另外，可在**Settings** \> **Editor** \> **Inspections** \> **MaxCompute**处修改某条规则。
-   **find usages**：选中editor中的某张表（或函数），右键菜单选**Find Usages** ，则会在当前IntelliJ project下寻找所有使用该表的脚本。
-   **live template**：Studio内置了一些SQL live template，可以在编辑器中使用Ctrl + J \(Command + J on Mac OS X\) 快捷键唤出（例如忘记了insert into table的语法，便可唤出live template popup后搜索insert table）。
-   **builtin documentation**：支持在系统内置函数处通过Ctrl + Q（Ctrl + J on Mac OS X）唤出帮助文档。
-   **sql history**：所有通过studio提交运行的sql我们都记录在本地了，你可以点击toolbar上的图标，弹出sql history窗口，查询你曾经执行过的SQL。

详细的SQL编写命令介绍请参见[表操作](../../../../intl.zh-CN/用户指南/SQL/DDL语句/表操作.md#)。

