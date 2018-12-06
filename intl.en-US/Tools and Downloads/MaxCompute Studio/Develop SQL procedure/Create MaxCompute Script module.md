# Create MaxCompute Script module {#concept_kcs_ll2_vdb .concept}

Before developing MaxCompute Script, you need to create a MaxCompute Script module in either of the following scenarios:

## No script file exists locally {#section_ens_sl2_vdb .section}

If no script file exists locally, you can use  Intellij IDEA to create a new module.

**Procedure**

1.  Open or create a MaxCompute Studio project.  This article uses creating a project as an example. Click **File** in the menu and select **New** \> **Project** . as shown in the following figure.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/12125/15440928761781_en-US.png)

2.  Select **MaxCompute Studio** on the left-side navigation pane,  and click **Next**.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/12125/15440928761782_en-US.png)

3.  Enter the project name, and click **Finish**.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/12125/15440928761783_en-US.png)

    **Note:** 

    If a project has been opened before, a dialog box appears, prompting whether to open the new project in the existing window \(closing the previous project\). Click **This Window**.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/12125/15440928761784_en-US.png)

4.  After the project is created, the page shown in the following figure appears. You can develop SQL scripts in the project.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/12125/15440928761785_en-US.png)


## Script files exist locally {#section_lns_sl2_vdb .section}

If many scripts have been stored in a local folder,  MaxCompute Studio is used to edit the scripts. You can open a module directly.

**Procedure**

1.  Create a connection configuration file [odps\_config.ini](../../../../reseller.en-US/Prepare/Install and configure a client.md) for MaxCompute in the **scripts** folder, and configure authentication information for connecting to MaxCompute.

    -   project\_name=xxxxxxxx
    -   access\_id=xxxxxxxxxx
    -   access\_key=xxxxxxxxx
    -   end\_point=xxxxxxxxx
     

2.  Open IntelliJ IDEA, select  **File \> ** \> **Open**, and select the **scripts** folder.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/12125/15440928771787_en-US.png)

3.  MaxCompute Studio detects whether the odps\_config.ini file exists in the folder, captures metadata on the server based on the configuration information in the file,  and compiles all scripts in the folder.


