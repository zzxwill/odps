# Project space connection management {#concept_zrp_lyz_5db .concept}

MaxCompute One of the core features of MaxCompute Studio is to browse resources of a MaxCompute project, including **Table**, **UDF**, and **Resource**. To realize this feature, create a project connection first.

## Prerequisites {#section_obf_dkz_5db .section}

To display Tool Windows of IntelliJ IDEA, you must open an IntelliJ IDEA project, and the MaxCompute Project must be configured on IntelliJ IDEA using MaxCompute Project Explorer in Tool Windows. Therefore, before creating a MaxCompute Project connection, add or import an IntelliJ IDEA project. This document uses adding a project under Windows as an example.

Open IntelliJ IDEA, click **Create New Project**, select **MaxCompute Studio** on the displayed page, and click **Next**.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/12119/1596_en-US.png)

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/12119/1597_en-US.png)

Enter the project name, and click **Finish**.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/12119/1598_en-US.png)

## Create a MaxCompute Project Connection {#section_sbf_dkz_5db .section}

We recommend that you configure the MaxCompute project connection according to your region strictly. Otherwise, some errors may occur.

**Procedure**

1.  Click **view**, select **Tool Windows**.
2.  Click **Project Explorer**.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/12119/1599_en-US.png)

3.  Click plus sign **+** at the upper left corner to add a MaxCompute project.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/12119/1600_en-US.png)

4.  In the Add MaxCompute Project dialog box, set configuration options.

    **Note:** 

    -   Click question mark \(?\) at the lower left corner of the dialog box to go to the online document page.
    -   If the synchronization times out, you can consider increasing the time-out duration for synchronizing metadata to the local host on the Setting tab.
5.  After the preceding settings, click **OK**. Information about the MaxCompute project is displayed on the left of MaxCompute Project Explorer. You can click Tables, Views, Functions, and Resources to view tables, views, functions, and resources of the project. 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/12119/1602_en-US.png)


## View and modify a MaxCompute connection {#section_zbf_dkz_5db .section}

In MaxCompute Project Explorer, right-click a MaxCompupte project and select **Show|Modify project properties**.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/12119/1603_en-US.png)

In the displayed dialog box, you can view or modify connections and settings of the MaxCompute project. 

## Subsequent operations {#section_bcf_dkz_5db .section}

Now, you know how to create and manage a project connection. You can continue to the next tutorial. In the tutorial, you will learn how to query metadata, clear data, and upload and download data to manage data and resources. For more information, see [Manage data and resources](intl.en-US/Tools and Downloads/MaxCompute Studio/Manage data and resources/View tables and UDF.md#).

