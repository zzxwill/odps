# SDK Summary {#concept_ygm_k4m_vdb .concept}

Maven users can search for odps-sdk-graph in the [Maven database](http://search.maven.org/)  to get the required SDK \(available in different versions\). The configuration information is as follows:

```
<dependency>
    <groupId>com.aliyun.odps</groupId>
    <artifactId>odps-sdk-graph</artifactId>
    <version>0.20.7</version>
</dependency>
```

|Main interface|Description|
|:-------------|:----------|
|GraphJob|GraphJob is inherited from JobConf and is used to define, submit, and manage a MaxCompute Graph job.|
|Vertex|A vertex is a node that is defined by the ID, value, halted, and edges attributes.  A vertex is implemented by the setVertexClass interface of GraphJob.|
|Edge|Edge is the abstract of edges in a graph, including the attributes destVertexId and value.  Adjacency tables are used as the graph data structure, and outbound edges of a vertex are stored in edges of the vertex.|
|GraphLoader|GraphLoader is used to load graphs.  GraphLoader is implemented by using the setGraphLoaderClass interface of GraphJob.|
|VertexResolver|VertexResolver is used to customize the conflict processing logic for graph topology modification.  The setLoadingVertexResolverClass and setComputingVertexResolverClass interfaces of GraphJob provide the conflict processing logic for graph topology modification during graph loading and iteration calculation.|
|Partitioner|Partitioner is used to partition a graph so that the calculation can be fragmented. Partitioner is implemented by using the setPartitionerClass interface of GraphJob. HashPartitioner is used by default, that is, the hash value of a vertex ID is calculated  and then a modulo operation is performed for the number of Workers.|
|WorkerComputer|WorkerComputer allows a Worker to run custom logic during startup and exit. WorkerComputer is implemented by using the setWorkerComputerClass interface of GraphJob.|
|Aggregator|setAggregatorClass\(Class …\)  defines one or multiple  Aggregators.|
|Combiner|setCombinerClass sets a Combiner.|
|Counters|Indicates the counter. In job running logic, the WorkerContext interface can be used to obtain Counters and perform counting. The framework automatically sums results.|
|WorkerContext|Indicates the context object. It encapsulates functions provided by the framework, such as modifying a graph topology, sending a message, writing a result, and reading a resource.|

