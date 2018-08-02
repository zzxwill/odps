# Summary {#concept_xtj_fwl_vdb .concept}

MaxCompute Graph  is a processing framework designed for iterative graph computing. MaxCompute Graph jobs use graphs to build models. Graphs are composed of vertices and edges. Vertices and edges contain values. 

MaxCompute Graph supports the following graph editing operations:

-   Editing the value of Vertex or Edge.
-   Adding/deleting Vertex.
-   Adding/deleting Edge.

**Note:** When editing a vertex and an edge, you must maintain their relationship.

This process outputs a final solution after performing iterative graph editing and evolution. Typical applications include [PageRank](intl.en-US/User Guide/Graph/Examples/PageRank.md), [SSSP algorithm](intl.en-US/User Guide/Graph/Examples/SSSP.md), and [Kmeans algorithm](intl.en-US/User Guide/Graph/Examples/Kmeans.md). You can use Java SDK, an interface provided by MaxCompute Graph, to compile graph computing programs.

## Graph Data Structure {#section_thp_nwl_vdb .section}

Graphs processed by MaxCompute Graph must be directed graphs consisting of vertices and edges. As MaxCompute  only provides a two-dimensional storage structure, you must resolve graph data into two-dimensional tables and store them in MaxCompute.

During graph computing analysis, use custom GraphLoader to convert two-dimensional table data to  vertices and edges in the MaxCompute Graph engine. You can determine how to resolve graph data into two-dimensional tables based on your service scenarios. In the sample code, the table formats correspond to different graph data structures.

The vertex structure can be described as < ID, Value, Halted, Edges \>, which respectively indicate the vertex ID \(ID\), value \(Value\), status \(Halted,  indicating whether an iteration is to be stopped\), and edge set \(Edges, indicating lists of all edges starting from the vertex\).  The edge structure can be described as < DestVertexID, Value \>,  which respectively indicate the destination vertex \(DestVertexID\) and value \(Value\).

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/12045/2182_en-US.png)

For example, the preceding figure consists of the following vertices.

|Vertex|<ID, Value, Halted, Edges\>|
|:-----|:--------------------------|
|v0|<0, 0, false, \[ <1, 5 \>, <2, 10 \> \] \>|
|v1|<1, 5, false, \[ <2, 3\>, <3, 2\>, <5, 9\>\]\>|
|v2|<2, 8, false, \[<1, 2\>, <5, 1 \>\]\>|
|v3|<3, Long.MAX\_VALUE, false, \[<0, 7\>, <5, 6\>\]\>|
|v5|<5, Long.MAX\_VALUE, false, \[<3, 4 \> \]\>|

## Graph program logic {#section_dht_zwl_vdb .section}

**Graph loading**

Graph loading: The framework calls custom GraphLoader and resolves records of an input table to vertices or edges.

Distributed architecture: The framework calls custom Partitioner to partition vertices and distributes them to corresponding Workers. \(Default partitioning logic: Calculate the hash value of a vertex ID and perform the modulo operation on the number of Workers.\)

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/12045/2208_en-US.png)

For example, assume in the preceding figure that the number of Workers is 2. v0 and v2 are allocated to Worker 0 because the result of the ID mod 2 is 0. v1, v3, and v5 are allocated to  Worker 1 because the result of the ID mod 2 is 1.

**Iteration calculation**

-   An iteration is called a **superstep**. It traverses all vertices in the non-halted status \(the value of Halted is  false\) or all vertices that receive messages \(a vertex in halted status is automatically woken up after receiving a message\), and calls their compute \(ComputeContext context,  Iterable messages\) method.
-   You can follow these steps on your implemented compute \(ComputeContext context, Iterable messages\) method:
    -   Process Messages sent from previous SuperStep to current Vertex.
    -   Edit graph as needed:
        -   Revise value of Vertex/Edge.
        -   Send Messages to certain Vertices.
        -   Add/delete Vertex or Edge.
    -   Use Aggregator to collect information to global information.
    -   Set the current vertex to the halted or non-halted status.
    -   During iteration, the framework asynchronously sends messages to the corresponding Worker and processes the messages in the next superstep without your intervention.

**Iteration termination \(only if any of the following conditions is met\)**

If any of the following conditions is met, iteration becomes terminate.

-   All vertices are in the halted status \(the value of Halted is true\) and no new message is generated.

-   The maximum number of iterations is reached.

-   The terminate\(\) method of an Aggregator returns true.


The pseudocode is described as follows.

```
// 1. load
for each record in input_table {
  GraphLoader.load();

// 2. setup
WorkerComputer.setup();
for each aggr in aggregators {
  aggr.createStartupValue();

for each v in vertices {
  v.setup();

// 3. superstep
for (step = 0; step < max; step ++) {
  for each aggr in aggregators {
    aggr.createInitialValue();
  
  for each v in vertices {
     v.compute();
   

// 4. cleanup
for each v in vertices {
  v.cleanup();

WorkerComputer.cleanup();
```

