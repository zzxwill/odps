# 安装Eclipse插件 {#concept_p3g_jp5_vdb .concept}

本文向您介绍如何在Eclipse安装MaxCompute插件。

为了方便您使用[MapReduce](../../../../intl.zh-CN/用户指南/MapReduce/概要/MapReduce概述.md) 及[UDF](../../../../intl.zh-CN/用户指南/SQL/UDF/UDF概述.md)的Java SDK进行开发工作，MaxCompute（原 ODPS）提供了Eclipse开发插件。该插件能够模拟MapReduce及UDF的运行过程，为您提供本地调试手段，并提供了简单的模板生成功能。

**说明：** 

-   目前高版本的Eclipse Neon有可能会导致插件加载失败，请使用Eclipse Luna版本。
-   请单击 [此处](https://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/cn/odps/0.0.90/assets/download/odps-eclipse-plugin-bundle-0.16.0.zip) 下载插件。
-   与MapReduce提供的本地运行模式不同，Eclipse插件不能够与MaxCompute同步数据。您使用的数据需要手动拷贝到Eclipse插件的warehouse目录下。

下载Eclipse插件后，将软件包解压，会看到如下Jar内容：

```
odps-eclipse-plugin-bundle-0.16.0.jar
```

将插件放置在Eclipse安装目录的plugins子目录下。打开Eclipse，单击右上角的打开透视图（Open Perspective），如下图。

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/12150/15450137642933_zh-CN.png)

单击后出现对话框，如下图。

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/12150/15450137642934_zh-CN.png)

选择ODPS，单击**OK** 。同样在右上角会出现ODPS图标，表示插件生效。如下图。

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/12150/15450137642935_zh-CN.png)

