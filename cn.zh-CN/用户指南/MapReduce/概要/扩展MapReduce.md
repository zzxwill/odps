# 扩展MapReduce {#concept_ngt_sxf_vdb .concept}

相比于传统的MapRudece，MaxCompute提供的扩展MapReduce模型改变了底层的调度和IO模型，可避免作业时冗余的IO操作。

传统的MapReduce模型要求每一轮MapReduce操作之后，数据必须落地到分布式文件系统上（比如HDFS或MaxCompute表）。而一般的MapReduce应用通常由多个MapReduce作业组成，每个作业结束之后需要写入磁盘，接下去的Map任务很多情况下只是读一遍数据，为后续的Shuffle阶段做准备，这样其实造成了冗余的IO操作。

MaxCompute的计算调度逻辑可以支持更复杂的编程模型， 针对上述情况，可以在Reduce后直接执行下一次的Reduce操作，而不需要中间插入一个Map操作。因此，MaxCompute提供了扩展的MapReduce模型，即可以支持Map后连接任意多个Reduce操作，比如Map\>Reduce\>Reduce。

Hadoop Chain Mapper/Reducer也支持类似的串行化Map或Reduce操作，但和MaxCompute的扩展MapReduce（MR2）模型有本质的区别。

因为Chain Mapper/Reducer还是基于传统的MapReduce模型，只是可以在原有的Mapper或Reducer后面，再增加一个或多个Mapper操作（不允许增加Reducer）。这样的好处是：您可以复用之前的Mapper业务逻辑，可以把一个Map或Reduce拆成多个Mapper阶段，但本质上并没有改变底层的调度和I/O模型。

与[MaxCompute](intl.zh-CN/用户指南/MapReduce/概要/MapReduce概述.md)相比，MR2在Map/Reduce等函数编写方式上基本一致，较大的不同点发生在作业时。更多详情请参见[扩展MapReduce示例](intl.zh-CN/用户指南/MapReduce/示例程序/Pipeline示例.md)。

