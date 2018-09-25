# 开发PyODPS脚本 {#concept_ffm_wdd_xdb .concept}

## 开发PyODPS脚本 {#section_a5b_2zk_gfb .section}

PyODPS是MaxCompute Python版本的SDK， 它提供了对MaxCompute对象的基本操作，并提供了DataFrame框架，您可以在MaxCompute上进行数据分析。

1.  使用PyODPS开发脚本，首先需要[安装PyODPS及Python插件](cn.zh-CN/工具及下载/MaxCompute Studio/开发Python程序/Python开发使用须知.md)，安装完成后即可直接新建一个MaxCompute PyODPS脚本。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/13748/15378624063447_zh-CN.png)

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/13748/15378624063452_zh-CN.png)

2.  新建成功后，模板会通过PyODPS[room对象](../../../../cn.zh-CN/用户指南/PyODPS/交互体验增强/IPython增强.md#)，自动初始化odps和o这两个对象。

    **说明：** 在公共云Dataworks上运行时会自动在后台创建，所以为了IDEA编译通过，需要显式创建。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/13748/15378624073455_zh-CN.png)


