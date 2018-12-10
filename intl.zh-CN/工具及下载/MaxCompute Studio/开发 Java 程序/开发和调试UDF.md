# 开发和调试UDF {#concept_nvh_s2b_5db .concept}

创建完成[MaxCompute Java Module](intl.zh-CN/工具及下载/MaxCompute Studio/开发 Java 程序/创建MaxCompute Java Module.md)后，即可开发UDF。

## 操作步骤 {#section_aqr_m1g_vdb .section}

1.  展开已创建的MaxCompute Java Module目录，导航至**src** \> **main** \> **java** \> **new**，单击**MaxCompute Java** 。如下图所示：

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/12130/15444366381944_zh-CN.png)

2.  填写**name**和**kind**，单击**OK** 。如下图所示：

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/12130/15444366381947_zh-CN.png)

    -   Name：填写创建的MaxCompute Java Class名称，如果还没创建package，可以在此处填写 packagename.classname，会自动生成package。
    -   Kind：选择类型。目前支持的类型有：自定义函数（UDF/UDAF/UDTF）、MapReduce（Driver/Mapper/Reducer）、非结构化开发（StorageHandler/Extractor）等。
3.  创建成功后，即可进行开发、编辑、测试Java程序。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/12130/15444366391948_zh-CN.png)

    **说明：** 这里的代码模板可在Intellij中自定义，具体的：**Preference** \> **Editor** \> **File** \> **Code Templates**，然后在Code标签页中寻找MaxCompute对应的模板修改。


通常情况下，JAVA UDF的开发可以通过以下几种方式：

-   使用MaxCompute Studio完成JAVA UDF开发整个流程。
-   使用[Eclipse插件开发和调试JAVA UDF](intl.zh-CN/工具及下载/Eclipse开发插件/UDF开发插件介绍.md#)，导出Jar包，然后通过命令或者DataWorks[添加资源](../../../../intl.zh-CN/用户指南/常用命令/资源操作.md#)后再[注册函数](../../../../intl.zh-CN/用户指南/常用命令/函数操作.md#)。

了解详细开发步骤，请参见[JAVA UDF开发](../../../../intl.zh-CN/快速入门/JAVA UDF开发.md#)。

## 调试UDF {#section_vqx_3cg_vdb .section}

开发UDF完成后，即可通过单元测试和本地运行两种方式进行测试，看是否符合预期。

**单元测试**

在examples目录下有各种类型的单元测试示例，可参考示例编写自己的Unit Test。

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/12130/15444366391949_zh-CN.png)

**本地运行**

本地运行UDF时，需要指定运行数据源，有以下两种方式设定测试数据源：

-   MaxCompute Studio通过Tunnel服务自动下载指定项目下的表数据到warehouse目录下。
-   提供Mock项目及表数据，您可参考warehouse下的example\_project自行设置。

**操作步骤**

1.  右击UDF类，单击**运行**，弹出**run configuration**对话框。UDF/UDAF/UDTF一般作用于select子句中表的某些列，需要配置MaxCompute project，table和column（元数据来源于project explorer和warehouse下的Mock项目）。复杂类型的调试也是支持的，如下图：

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/12130/15444366391950_zh-CN.png)

2.  单击**OK**。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/12130/15444366391951_zh-CN.png)

    **说明：** 

    -   如果指定项目下的表数据未被下载到warehourse中，需要先下载数据，默认下载100条。默认下载100条，如需更多数据，可配置Download record limit项。
    -   如果采用Mock项目或已下载数据，则直接运行。
    -   UDF的local run框架会将warehouse中指定列的数据作为UDF的输入，开始本地运行UDF，您可以在控制台看到日志输出和结果打印。

**本地warehouse目录**

本地warehouse用于存储表（包括meta和数据）或资源，用于本地执行UDF或MR。warehouse目录如下图所示：

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/12130/15444366391952_zh-CN.png)

**说明：** 

-   warehouse目录下依次是项目名，tables，表名，表schema和sample data。
-   schema文件依次配置项目名，表名，以及列名和类型（冒号分隔），分区表还需配置分区列（非分区表参考wc\_in1，分区表参考wc\_in2）。
-   data文件采用标准csv格式存储表的sample数据：
    -   特殊字符为逗号，双引号和换行（\\n或\\r\\n）
    -   列分隔符为逗号，行分隔符为\\n或\\r\\n
    -   如果列内容里包含特殊字符，需要在该列内容前后加上双引号，例如：3,No -\> “3, No”
    -   如果列内容包含双引号，则每个双引号转义成两个双引号，例如：a”b”c -\> “a””b””c”
    -   \\N表示该列为null，如果该列内容（string 类型）就是\\N，需要转换为 “””\\N”””
    -   文件字符编码为UTF-8

