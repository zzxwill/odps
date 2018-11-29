# JDBC driver {#concept_gtx_215_z2b .concept}

MaxCompute provides JDBC interfaces that are fully compatible with the PostgreSQL protocol. Users can connect SQL client tools to the MaxCompute Lightning service using JDBC interfaces.

MaxCompute Lightning can be accessed using JDBC drivers from the PostgreSQL official website or other drivers optimized for MaxCompute Lightning.

1.  [JDBC](https://jdbc.postgresql.org/) drivers from the PostgreSQL official website.

    **Note:** Many client tools already have PostgreSQL database drivers built in, you can use the built-in drivers. If it is not integrated, you can download required drivers from the PostgreSQL official website. Take the SQL Workbench/J client as an example. You can choose the PostgreSQL official drivers when creating a connection.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/20124/153812960011216_en-US.jpg)

2.  [JDBC drivers](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/attach/89778/cn_zh/1535960228920/MaxComputeLightningJDBC.jar) optimized by Alibaba Cloud MaxCompute Lightning

    The downloaded MaxCompute Lightning JDBC driver is saved as a MaxComputeLightningJDBC.jar file. Take the SQL Workbench/J client as an example. In the **Driver Management** menu, add the MaxCompute Lightning JDBC driver entry.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/20124/153812960011243_en-US.jpg)

    When you create a connection, select the MaxCompute Lightning JDBC driver that you just added from the Driver list.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/20124/153812960011244_en-US.jpg)


