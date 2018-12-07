# 创建MaxCompute Script Module {#concept_kcs_ll2_vdb .concept}

开发MaxCompute Script前，需要创建一个MaxCompute Script Module，创建时存在以下两种情况：

## 本地没有script文件 {#section_ens_sl2_vdb .section}

本地没有script文件时，可通过Intellij创建一个全新的Module。

**操作步骤**

1.  打开或者新建一MaxCompute Studio Project。本文以新建为例，单击菜单栏中的**File**，导航至**New** \> **Project** 。如下图所示：

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/12125/15441689111781_zh-CN.png)

2.  选择左侧导航栏中的**MaxCompute Studio**，单击**Next** 。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/12125/15441689111782_zh-CN.png)

3.  填写Project Name，单击**Finish**。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/12125/15441689111783_zh-CN.png)

    **说明：** 

    如果之前有打开的Project，将会提示您是否在当前窗口中打开（即关掉之前的Project），选择**This Window**。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/12125/15441689111784_zh-CN.png)

4.  创建完成后，页面如下图所示，您即可在此项目中开发SQL脚本。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/12125/15441689111785_zh-CN.png)


## 本地已有script文件 {#section_lns_sl2_vdb .section}

假如本地某个文件夹下已经有很多script，此时需要用MaxCompute studio来编辑这些 script，您可直接打开一个Module。

**操作步骤**

1.  在**scripts**文件夹下创建一个MaxCompute的连接配置文件 [odps\_config.ini](../../../../intl.zh-CN/准备工作/安装并配置客户端.md)，在里面配置与MaxCompute连接的鉴权信息：

    -   project\_name=xxxxxxxx
    -   access\_id=xxxxxxxxxx
    -   access\_key=xxxxxxxxx
    -   end\_point=xxxxxxxxx
     

2.  打开IDEA，导航至**File** \> **open**， 选择您的**scripts**文件夹。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/12125/15441689111787_zh-CN.png)

3.  MaxCompute Studio会探测该文件夹下是否存在odps\_config.ini文件，根据这个文件里的配置信息抓取服务端的meta，然后编译文件夹下的所有script。


