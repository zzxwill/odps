# MR限制项汇总 {#concept_m4c_4lg_vdb .concept}

为避免您因没注意限制条件，业务启动后才发现限制条件，而导致业务停止的现象发生，本文将对MaxCompute MR限制项进行汇总，以方便您查看。

MaxCompute MR限制项汇总，如下表所示：

|边界名|边界值|分类|配置项名称|默认值|是否可配置|说明|
|:--|:--|:-|:----|:--|:----|:-|
|instance内存占用|\[256MB,12GB\]|内存限制|odps.stage.mapper\(reducer\).mem和odps.stage.mapper\(reducer\).jvm.mem|2048M＋1024M|是|单个map instance或reduce instance占用memory，由框架memory（默认2048M）和jvm的heap memory（默认1024M）两部分 。|
|resource数量|256个|数量限制|不涉及|无|否|单个job引用的resource数量不超过256个，table、archive按照一个单位计算 。|
|输入路数和输出路数|1024个和256个|数量限制|不涉及|无|否|单个job的输入路数不能超过1024（同一个表的一个分区算一路输入，总的不同表个数不能超过64个），单个job的输出路数不能超过256 。|
|counter数量|64个|数量限制|不涉及|无|否|单个job中自定义counter的数量不能超过64，counter的group name和counter name中不能带有\#，两者长度和不能超过100。|
|map instance 　|\[1,100000\]|数量限制|odps.stage.mapper.num|无|是|单个job的map instance个数由框架根据split size计算得出，如果没有输入表，可以通过 odps.stage.mapper.num直接设置，最终个数范围\[1,100000\]。|
|reduce instance|\[0,2000\]|数量限制|odps.stage.reducer.num|无|是|单个job默认reduce instance个数为map instance个数的1/4，用户设置作为最终的reduce instance个数，范围\[0,2000\]。可能出现这样的情形：reduce处理的数据量会比map大很多倍，导致reduce阶段比较慢，而reduce只能最多2000 。|
|重试次数|3|数量限制|不涉及|无|否|单个map instance或reduce instance失败重试次数为3，一些不可重试的异常会直接导致作业失败。|
|local debug模式|instance个数不超100|数量限制|不涉及|无|否|local debug模式下，默认map instance个数为2，不能超过100；默认reduce instance个数为1，不能超过100；默认一路输入下载记录数100，不能超过10000。|
|重复读取resource次数|64次|数量限制|不涉及|无|否|单个map instance或reduce instance重复读一个resource次数限制<=64次 。|
|resource字节数|2G|长度限制|不涉及|无|否|单个job引用的resource总计字节数大小不超过2G。|
|split size|\[1,\)|长度限制|odps.stage.mapper.split.size|256M|是|框架会参考设置的split size值来划分map，决定map的个数 。|
|string列内容长度|8M|长度限制|不涉及|无|否|MaxCompute表string列内容长度不允许 。|
|worker运行超时时间|［1,3600］|时间限制|odps.function.timeout|600|是|map或者reduce worker在无数据读写且没有通过context.progress\(\)主动发送心态的情况下的超时时间，默认值是600s。|
|MR引用Table资源支持的字段类型|BIGINT、DOUBLE、STRING、DATETIME、BOOLEAN|数据类型限制|不涉及|无|否|MR任务引用到Table资源时，若table表字段有其他类型字段执行报错。|
|MR是否支持读取OSS数据|不涉及|功能限制|不涉及|无|否|MR不支持读取OSS数据。|
|MR是否支持MaxCompute2.0新类型|不涉及|功能限制|不涉及|无|否|MR不支持MaxCompute2.0新类型。|

