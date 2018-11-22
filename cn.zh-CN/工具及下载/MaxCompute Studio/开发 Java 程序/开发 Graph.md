# 开发 Graph {#concept_fpk_zzn_vdb .concept}

创建完成 [MaxCompute Java Module](cn.zh-CN/工具及下载/MaxCompute Studio/开发 Java 程序/创建 MaxCompute Java Module.md#)后，即可以开始开发[Graph](../../../../cn.zh-CN/用户指南/图模型/图模型概述.md#)了。

## 代码示例 {#section_v15_214_vdb .section}

在examples目录下有graph的一些代码示例，可参考示例熟悉Graph程序的结构。

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/12134/15428557262427_zh-CN.png)

## 编写Graph {#section_x15_214_vdb .section}

1.  在module的源码目录即**src** \> **main**上右键**new**，选择**MaxCompute Java**。

2.  选择GraphLoader/Vertex等类型，**Name**框中输入类名\(支持包名.类名\)。点击**OK**，模板会自动填充框架代码，可在此基础上继续修改。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/12134/15428557262428_zh-CN.png)


## 本地调试Graph {#section_z15_214_vdb .section}

Graph开发好后，下一步就是要测试自己的代码，看是否符合预期。我们支持本地运行Graph，具体的：

1.  运行Graph: 在驱动类\(有main函数且调用GraphJob.run方法\)上右键，点击**运行**菜单，弹出run configuration对话框，配置Graph需要在哪个MaxCompute Project上运行即可。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/12134/15428557262429_zh-CN.png)

2.  点击**OK**，如果指定MaxCompute project的表数据未被下载到warehouse中，则首先下载数据；如果采用mock项目或已被下载则跳过。接下来，graph local run框架会读取warehouse中指定表的数据作为输入，开始本地运行Graph，用户可以在控制台看到日志输出。每运行一次本地调试，都会在Intellij工程目录下新建一个临时目录，见下图：

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/12134/15428557262430_zh-CN.png)

    **说明：** 关于warehouse的详细介绍请参考[开发UDF](cn.zh-CN/工具及下载/MaxCompute Studio/开发 Java 程序/开发和调试UDF.md#warehous)中本地warehouse目录部分。


## 生产运行Graph {#section_cb5_214_vdb .section}

本地调试通过后，接下来就可以把Graph发布到服务端，在MaxCompute分布式环境下运行了:

1.  首先，将自己的Graph程序打成jar包，并发布到服务端。[如何打包发布?](cn.zh-CN/工具及下载/MaxCompute Studio/开发 Java 程序/打包、上传及注册.md#)
2.  通过Sstudio无缝集成的MaxCompute Console（在**Project Explore**r Window的Project上右键，选择**Open in Console**），在Console命令行中输入类似如下的 [jar命令](../../../../cn.zh-CN/用户指南/MapReduce/功能介绍/作业提交.md#)：

    ```
    jar -libjars xxx.jar -classpath /Users/home/xxx.jar com.aliyun.odps.graph.examples.PageRank pagerank_in pagerank_out;
    ```


更详细的Graph开发介绍请参见[编写Graph](../../../../cn.zh-CN/快速入门/编写Graph.md#)。

