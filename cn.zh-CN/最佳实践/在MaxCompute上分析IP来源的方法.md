# 在MaxCompute上分析IP来源的方法 {#concept_q25_ygl_5fb .concept}

本文介绍如何在MaxCompute上分析IP来源的方法，包括下载、上传淘宝IP地址库数据及编写UDF函数、编写SQL四个步骤。

[淘宝IP地址库](http://ip.taobao.com/)的查询接口为[IP地址字串](http://ip.taobao.com/service/getIpInfo.php?ip=[ip%E5%9C%B0%E5%9D%80%E5%AD%97%E4%B8%B2])。使用示例如下。

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/63437/154331952331905_zh-CN.png)

由于在MaxCompute中禁止使用HTTP请求，如何实现在MaxCompute中进行IP的查询？目前有三种方式：

1.  用SQL将数据查询到本地，再发起HTTP请求查询。

    **说明：** 效率低下，且淘宝IP库查询频率需小于10QPS，否则拒绝请求。

2.  下载IP地址库到本地，进行查询。

    **说明：** 同样效率低，且不利于数仓等分析使用。

3.  将IP地址库定期维护上传至MaxCompute，进行连接查询。

    **说明：** 比较高效，但是IP地址库需自己定期维护。


本文重点为您介绍第三种方式。

## 下载IP地址库 {#section_xwc_y3l_5fb .section}

1.  在[IP数据下载链接](http://ip.taobao.org:9999/ipdata_download.html)获取您需要的格式数据。本文以**基本数据格式**为例。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/63437/154331952331906_zh-CN.png)

2.  下载**utf8**数据到本地，检查数据格式。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/63437/154331952431907_zh-CN.png)

    前四个数据是IP地址的起始地址与结束地址：前两个是十进制整数形式，后两个是点分形式。这里我们使用整数形式，以便计算IP是否属于这个网段。


## 上传IP地址库数据 {#section_eqh_pjl_5fb .section}

1.  创建表DDL，您可以使用[MaxCompute客户端](../../../../cn.zh-CN/工具及下载/客户端.md#)进行操作，也可以使用DataWorks进行[图形化建表](../../../../cn.zh-CN/使用指南/数据开发/表管理.md#)。

    ```
    DROP TABLE IF EXISTS ipresource ;
    
    CREATE TABLE IF NOT EXISTS ipresource 
    (
        start_ip BIGINT
        ,end_ip BIGINT
        ,start_ip_arg string
        ,end_ip_arg string
        ,country STRING
        ,area STRING
        ,city STRING
        ,county STRING
        ,isp STRING
    );
    ```

2.  使用[Tunnel命令操作](../../../../cn.zh-CN/用户指南/数据上传下载/Tunnel命令操作.md#)上传您的文件，本例中ipdata.txt.utf8文件存放在D盘。

    ```
    odps@ workshop_demo>tunnel upload D:/ipdata.txt.utf8 ipresource;
    ```

    可以通过SQL语句`select count(*) from ipresource;`查看到表中上传的数据条数（不断更新增长中）。

3.  使用SQL语句`select count(*) from ipresource limit 0,10;`查看ipresource表前10条的样本数据。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/63437/154331952431909_zh-CN.png)


## 编写UDF函数 {#section_uht_3kl_5fb .section}

通过编写Python UDF将点号分割的IP地址转化为int类型的IP，本例中利用DataWorks的[PyODPS节点](../../../../cn.zh-CN/最佳实践/在PyODPS任务中调用第三方包.md#)完成，详细说明如下。

1.  首先您需要在**数据开发** \> **业务流程** \> **资源**中右键新建Python类型资源。在弹框中输入新建的Python资源名称，勾选**上传为ODPS资源**，完成创建。。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/63437/154331952431910_zh-CN.png)

2.  在您新建的Python资源内编写Python资源代码，示例如下。

    ```
    from odps.udf import annotate
    @annotate("string->bigint")
    class ipint(object):
    	def evaluate(self, ip):
    		try:
    			return reduce(lambda x, y: (x << 8) + y, map(int, ip.split('.')))
    		except:
    			return 0
    ```

    点击提交并解锁。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/63437/154331952431911_zh-CN.png)

3.  在**数据开发** \> **业务流程** \> **函数**中右键新建自定义函数。

    填写函数的类名，本例中为`ipint.ipint`,资源列表填写刚刚我们提交的资源名称，提交并解锁。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/63437/154331952431913_zh-CN.png)

4.  验证ipint函数是否生效并满足预期值，您可以在DataWorks上新建一个ODPS SQL类型节点运行SQL语句查询，示例如下。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/63437/154331952431914_zh-CN.png)


## 在SQL中使用 {#section_rmm_bml_5fb .section}

**说明：** 本例中以一个随机的具体IP 1.2.24.2 地址为例，您在正常使用时可以用具体表的字段来读入。

测试使用的SQL代码如下，点击运行即可查看查询结果。。

```
select * from ipresource
WHERE ipint('1.2.24.2') >= start_ip
AND ipint('1.2.24.2') <= end_ip
```

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/63437/154331952431915_zh-CN.png)

通过为保证数据准确性，您可以定期从淘宝IP库获取数据来维护ipresource表。

