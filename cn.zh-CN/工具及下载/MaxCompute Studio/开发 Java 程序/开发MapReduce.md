# 开发MapReduce {#concept_nvh_s2c_5db .concept}

创建完成[MaxCompute Java Module](intl.zh-CN/工具及下载/MaxCompute Studio/开发 Java 程序/创建MaxCompute Java Module.md)后，即可以开始开发[MR](../../../../intl.zh-CN/用户指南/MapReduce/概要/MapReduce概述.md)了。

## 开发MR {#section_gr4_mgg_vdb .section}

1.  在module的源码目录即**src** \> **main**上右键**new** \> **java**，选择**MaxCompute Java**。
2.  分别创建Driver，Mapper，Reducer。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/12131/15447826441997_zh-CN.png)

3.  模板已自动填充框架代码，只需要设置输入/输出表，Mapper/Reducer类等即可。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/12131/15447826441998_zh-CN.png)


开发MR详情请参见[编写MapReduce](../../../../intl.zh-CN/快速入门/编写MapReduce.md#)。

## 调试MR {#section_jr4_mgg_vdb .section}

MR开发好后，下一步就是要测试自己的代码，看是否符合预期，我们支持两种方式:

单元测试：在examples目录下有WordCount的单测实例，可参考例子编写自己的UT。

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/12131/15447826441999_zh-CN.png)

本地运行MR：本地运行时，需要指定运行数据源，有两种方式设定测试数据源：

-   studio通过tunnel服务自动下载指定MaxCompute project的表数据到warehouse目录下。默认下载100条，如需更多数据测试，请自行使用console的tunnel命令或者studio的表下载功能。
-   提供mock项目\(example\_project\)及表数据，用户可参考warehouse下example\_project自行设置。

1.  运行MR: 在Driver类上右键，点击**运行**菜单，弹出run configuration对话框，配置MR需要在哪个MaxCompute Project上运行即可。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/12131/15447826442001_zh-CN.png)

2.  单击**OK**，如果指定MaxCompute project的表数据未被下载到warehouse中，则首先下载数据；如果采用mock项目或已被下载则跳过。接下来，MR local run框架会读取warehouse中指定表的数据作为MR的输入，开始本地运行MR，用户可以在控制台看到日志输出和结果打印。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/12131/15447826442002_zh-CN.png)


## 生产运行MR {#section_or4_mgg_vdb .section}

本地调试通过后，接下来就可以把MR发布到服务端，在MaxCompute分布式环境下运行了:

1.  首先，你需要将自己的MR程序打成jar包，并发布到服务端。详细操作步骤请参考[打包、上传及注册](intl.zh-CN/工具及下载/MaxCompute Studio/开发 Java 程序/开发MapReduce.md#)。
2.  通过MaxCompute console（无缝集成于studio），打开Project Explorer Window，右键单击**project**，选择**Open in Console**，可在console命令行中输入类似如下的jar命令：

    ```
    jar-libjars wordcount.jar -classpath D:\odps\clt\wordcount.jar com.aliyun.odps.examples.mr.WordCount wc_in wc_out;
    ```

    详细命令输入参请见[jar命令](../../../../intl.zh-CN/用户指南/MapReduce/功能介绍/作业提交.md)。


