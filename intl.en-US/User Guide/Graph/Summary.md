# Summary {#concept_xtj_fwl_vdb .concept}

MaxCompute Graph is a processing framework designed for iterative graph computing. MaxCompute Graph jobs use graphs to build models. Graphs are composed of vertices and edges, which contain values. 

MaxCompute Graph supports the following graph editing operations:

-   Editing the value of Vertex or Edge.
-   Add/delete Vertices.
-   Add/delete Edges.

**Note:** When editing a vertex and an edge, you must maintain their relationship.

This process outputs a final solution after performing iterative graph editing and evolution. Typical applications include [PageRank](reseller.en-US/User Guide/Graph/Examples/PageRank.md), [SSSP algorithm](reseller.en-US/User Guide/Graph/Examples/SSSP.md), and [Kmeans algorithm](reseller.en-US/User Guide/Graph/Examples/Kmeans.md). Use Java SDK, an interface provided by MaxCompute Graph to compile graph computing programs.

## Graph Data structure {#section_thp_nwl_vdb .section}

Graphs processed by MaxCompute Graph must be directed graphs consisting of vertices and edges. As MaxCompute only provides a two-dimensional storage structure, you must resolve graph data into two-dimensional tables and store them in MaxCompute.

During graph computing analysis, use custom GraphLoader to convert two-dimensional table data to vertices and edges in the MaxCompute Graph engine. You can determine how to resolve graph data into two-dimensional tables based on your service scenarios. In the sample code, the table formats correspond to different graph data structures.

The vertex structure can be described as < ID, Value, Halted, Edges \>, which respectively indicates the vertex ID \(ID\), value \(Value\), status \(Halted,  indicating whether an iteration is to be stopped\), and edge set \(Edges, indicating lists of all edges starting from the vertex\). The edge structure is described as < DestVertexID, Value \>, which respectively indicates the destination vertex \(DestVertexID\) and value \(Value\).

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/12045/15353752552182_en-US.png)

For example, the preceding figure consists of the following vertices:

|Vertex|<ID, Value, Halted, Edges\>|
|:-----|:--------------------------|
|v0|<0, 0, false, \[ <1, 5 \>, <2, 10 \> \] \>|
|v1|<1, 5, false, \[ <2, 3\>, <3, 2\>, <5, 9\>\]\>|
|v2|<2, 8, false, \[<1, 2\>, <5, 1 \>\]\>|
|v3|<3, Long.MAX\_VALUE, false, \[<0, 7\>, <5, 6\>\]\>|
|v5|<5, Long.MAX\_VALUE, false, \[<3, 4 \> \]\>|

## Graph program logic {#section_dht_zwl_vdb .section}

**Graph loading**

The framework calls custom GraphLoader and resolves records of an input table to vertices or edges.

Distributed architecture: The framework calls custom Partitioner to partition vertices and distributes them to corresponding Workers. \(Default partitioning logic: Calculates the hash value of a vertex ID and performs the modulo operation on the number of Workers.\)

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/12045/15353752552208_en-US.png)

For example, assume in the preceding figure that the number of Workers is 2. v0 and v2 are allocated to Worker 0 because the result of the ID mod 2 is 0. v1, v3, and v5 are allocated to Worker 1 as the result of the ID mod 2 is 1.

**Iteration calculation**

-   An iteration is called a **superstep**. It traverses all vertices in the non-halted status \(the value of the halted is false\) or all vertices that receive messages \(a vertex in halted status is automatically activated after receiving a message\), and calls their compute \(ComputeContext context, Iterable messages\) method.
-   Follow these steps on your implemented compute \(ComputeContext context, Iterable messages\) method:
    -   Process messages sent from the previous SuperStep to the current Vertex.
    -   Edit graph as needed:
        -   Revise value of Vertex/Edge
        -   Send messages to certain Vertices
        -   Add/Delete Vertex or Edge
    -   Use Aggregator to collect information to update the global information.
    -   Set the current vertex to a halted or non-halted status.
    -   During iteration, the framework asynchronously sends messages to the corresponding Worker and processes the messages in the next SuperStep without your intervention.

**Iteration termination**

If any of the following conditions are met, iteration is terminated.

-   All vertices are in the halted state \(the value of Halted is true\) and no new message is generated.

-   A maximum number of iterations is reached.

-   The terminate method of an Aggregator returns true.


The pseudocode is as follows:

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

