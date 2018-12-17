# Install {#concept_p3g_jp5_vdb .concept}

To facilitate the development work with [Java SDK of MapReduce](../../../../reseller.en-US/User Guide/MapReduce/Summary/MapReduce.md) and [UDF](../../../../reseller.en-US/User Guide/SQL/UDF/UDF Summary.md), MaxCompute provides Eclipse Development Plug-in. This plug-in can simulate the running process of MapReduce and  UDF to provide local debugging methods and simple template generation.

**Note:** 

-   Current versions of Eclipse Neon are likely to cause plug-in loading to fail, please use the Eclipse Luna version.
-   To download this plug-in, click [Here](https://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/cn/odps/0.0.90/assets/download/odps-eclipse-plugin-bundle-0.16.0.zip).
-   Unlike the local running mode provided by MapReduce, Eclipse plug-in cannot synchronize data with MaxCompute.  Data must be manually copied to the  warehouse directory of Eclipse plug-in.

After downloading the Eclipse plug-in, decompress the software package to find the following jar:

```
odps-eclipse-plugin-bundle-0.16.0.jar
```

Place the plug-in into the subdirectory plugins in Eclipse installation directory.  Start the Eclipse plug-in, and click Open Perspective in the upper right corner.As follows.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/12150/15450137702933_en-US.png)

After clicking the button, the following dialog box is displayed.As follows.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/12150/15450137702934_en-US.png)

Select ODPS and click **OK**.  The MaxCompute icon appears in the upper right corner, indicating that the plug-in takes effect.As follows.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/12150/15450137702935_en-US.png)

