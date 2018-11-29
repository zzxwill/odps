# Lightning常见问题 {#concept_dpw_cj5_z2b .concept}

本文为您整理应用MaxCompute Lightning过程中的常见问题。

-   Q：还没有建表的情况下，可以用MaxCompute Lightning查什么数据？

    A：您需要先通过DataWorks或odpscmd客户端工具，在MaxCompute项目中创建数据表，加载数据。然后通过MaxCompute Lightning连接到该项目，此时便可查看到项目内的表，并对这些表进行查询。

-   Q：MaxCompute Lightning是否限制查询的数据量？查询多大规模的数据性能较好？

    A：目前每次查询对单表的扫描数据量限制为1TB，数据量越小查询性能越好。

    **说明：** 建议不要扫描超过100GB的表数据。超过100GB的表数据虽然仍可查询，但查询性能会随数据规模增长逐渐下降。如果需要扫描量超过100GB的查询，建议您根据性能表现考虑使用MaxCompute SQL。

-   Q：使用BI工具，通过拖拽方式选择一张分区表进行分析时，提示报错：`ERROR: AXF Exception: specified partitions count in odps table: <project_name.table_name> is: xxx, exceeds the limitation of xxx, please add stricter partition filter`。

    A：MaxCompute Lightning为保障查询性能，对分区表的分区数量进行了限制，一次查询所扫描的单表最大分区数量不能超过1024。由于部分BI工具使用拖拽方式选择表直接进行分析，不能BI前端指定分区条件，导致请求扫描的分区数超限制、触发了Lightning限制而报错提示。建议先对查询的数据表进行加工处理，处理为非分区表或分区小于1024的表再进行分析。

-   Q：连接时提示创建数据连接失败:`ERROR: SSL required`。

    A：MaxCompute Lightning要求SSL连接服务，需要客户端指定以SSL方式连接。如果使用客户端工具，可以选择SSL连接选项。如果没有相关选项，可以在JDBC URL连接串中增加SSL参数，需要替换为您项目所在region的endpoint、连接的项目名称，例如`jdbc:postgresql://lightning.cn-shanghai.maxcompute.aliyun.com:443/myproject?ssl=true`。

-   Q：使用Workbench/J客户端查询时提示`Error:current transaction is aborted,commands ignored until end of transaction block`。

    A：使用的客户端请勾选**Autocommit**选项。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/20130/154324553211171_zh-CN.jpg)


 

