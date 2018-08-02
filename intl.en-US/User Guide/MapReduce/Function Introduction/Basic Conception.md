# Basic Conception {#concept_brv_lyf_vdb .concept}

## Map/Reduce {#section_jlv_lhg_vdb .section}

Map and Reduce support corresponding map/reduce method, setup method and cleanup method. The setup method is called before the map/reduce method, and each  worker only calls it only once. 

The cleanup method is called after the map/reduce method, and each worker calls it only once.

For a detailed example, see [Program Examples](intl.en-US/User Guide/MapReduce/Program Example/WordCount Sample.md).

## Sort/Group {#section_cr1_nhg_vdb .section}

Some columns in output key records can be taken as sort columns, but user-defined comparator is not supported.  You can select several columns from sort columns as Group  columns, but user-defined Group comparator is not supported.  Sort columns are used to sort your data while Group columns are used for secondarySort.

For more information, see [SecondarySort Example](intl.en-US/User Guide/MapReduce/Program Example/Secondary Sort Sample.md).

## Partition {#section_mg2_4hg_vdb .section}

Supports setting the partition column and customized partitioner. Partition columns have a higher priority than customized partitioners.

The partitioner is used to distribute the output data on Map terminal to different Reduce Workers according to Hash logic. 

## Combiner {#section_p44_phg_vdb .section}

The Combiner function combines adjacent records in Shuffle stage. You can choose whether to use Combiner according to different business logic.

Combiner helps optimize the MapReduce computing framework and the logic of Combiner is generally similar to Reduce.  After Map outputs the data, the framework performs a local combiner operation  for the data which has the same key value on Map terminal.

For more information, see [WordCount Code Examples](intl.en-US/User Guide/MapReduce/Program Example/WordCount Sample.md).

