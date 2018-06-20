# Extended MapReduce {#concept_ngt_sxf_vdb .concept}

The traditional MapReduce model requires that the data must be loaded to the distributed file system \(such as HDFS or MaxCompute table\) after each round of MapReduce operation.  However, a general  MapReduce application usually consists of multiple MapReduce jobs, and each job output needs to be written to a disk. The following Map task is an example of a task that was only to read data,  prepared for subsequent Shuffle stage, but which actually resulted in redundant I/O operations.

The calculation scheduling logic of MaxCompute can support more complex programming paradigm. In the preceding scenario, the next Reduce operation can be executed after Reduce operation and inserting a Map  operation is not necessary.  In this way, MaxCompute provides an extensional MapReduce model, that is, numerous Reduce operations can follow a Map operation, such as Map\>Reduce\> Reduce.

Hadoop Chain Mappper/Reducer also supports analogous serial Map or Reduce operations, but has  major differences compared with the extensional MaxCompute \(MR2\) model.

The Hadoop Chain Mapper/Reducer is based on traditional MapReduce model, and can only add one or multiple Mapper  operations \(it is not allowed to add Reducer operations\) after the original Mapper or Reducer. .  The benefits of extended MapReduce include that user can reuse previous business logic of Mapper and can split one Map stage or Reduce stage into multiple Mapper stages.  The underlying scheduling and I/O model are not changed essentially.

Compared with [MaxCompute](intl.en-US/User Guide/MapReduce/Summary/MapReduce.md) , MR2 is  basically consistent in the way map/reduce functions are written.  The main difference is in performance.  For more information, see [Extended MapReduce  example](intl.en-US/User Guide/MapReduce/Program Example/Pipeline Sample.md).

