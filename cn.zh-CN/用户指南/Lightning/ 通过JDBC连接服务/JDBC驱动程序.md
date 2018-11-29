# JDBC驱动程序 {#concept_gtx_215_z2b .concept}

MaxCompute提供完全兼容PostgreSQL消息协议的Java数据库连接（JDBC）接口，使用者可以通过JDBC将SQL客户端工具连接到MaxCompute Lightning服务。

MaxCompute Lightning支持PostgeSQL官方驱动连接，同时也提供了为Lightning服务而优化的驱动程序供选择。

1.  使用PostgeSQL官方提供的[JDBC](https://jdbc.postgresql.org/)驱动程序。

    **说明：** 很多客户端工具默认集成了PostgreSQL数据库的驱动，直接使用工具自带的驱动即可。如果未集成，可从官网下载。以SQL Workbench/J客户端为例，创建连接时可选择PostgreSQL官方驱动。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/20124/154324540911216_zh-CN.jpg)

2.  使用阿里云MaxCompute Lightning优化过的[JDBC驱动](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/attach/89778/cn_zh/1535960228920/MaxComputeLightningJDBC.jar)，以获取更好的性能。

    下载后的MaxCompute Lightning的JDBC驱动程序保存为MaxComputeLightningJDBC.jar文件。以SQL Workbench/J客户端为例，在**驱动管理**菜单中，添加MaxCompute Lightning JDBC驱动程序项。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/20124/154324540911243_zh-CN.jpg)

    在创建连接时，从Driver列表中选择刚才添加的MaxCompute Lightning JDBC驱动。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/20124/154324540911244_zh-CN.jpg)


