# SSSP {#concept_nts_tlm_vdb .concept}

Dijkstra is a typical algorithm that calculates the Single Source Shortest Path \(SSSP\) in a directed graph.

For weighted directed graph G=\(V,E\), many paths are routed from source vertex s to sink vertex v. In these paths, the one that has the smallest edge weight sum is called the shortest distance from s to v.

The basic concept of the algorithm is as follows:

-   Initialization: The distance from source vertex s to s itself is zero \(d\[s\] = 0\), and the distance from another vertex u to s is infinite \(d\[u\]=∞\).
-   Iteration: If an edge exists from u to v, the shortest distance from s to v is updated as: d\[v\] = min\(d\[v\], d\[u\] + weight\(u, v\)\). The iteration ends until the distance from all  vertices to s does not change.

The basic concept shows that the algorithm is applicable to solutions using the MaxCompute Graph program.  Each vertex maintains the current shortest distance to the source vertex. If the value changes, a message containing the new value and the edge weight is sent to the adjacent vertex. In the next iteration, the adjacent vertex updates the current shortest distance based on the received message. The iteration ends when the current shortest distance of all vertices does not change.

## Sample Code {#section_sfz_kvm_vdb .section}

Code of SSSP is as follows:

```
import java.io.IOException;

import com.aliyun.odps.io.WritableRecord;
import com.aliyun.odps.graph.Combiner;
import com.aliyun.odps.graph.ComputeContext;
import com.aliyun.odps.graph.Edge;
import com.aliyun.odps.graph.GraphJob;
import com.aliyun.odps.graph.GraphLoader;
import com.aliyun.odps.graph.MutationContext;
import com.aliyun.odps.graph.Vertex;
import com.aliyun.odps.graph.WorkerContext;
import com.aliyun.odps.io.LongWritable;
import com.aliyun.odps.data.TableInfo;

public class SSSP {

  public static final String START_VERTEX = "sssp.start.vertex.id";

  public static class SSSPVertex extends
      Vertex<LongWritable, LongWritable, LongWritable, LongWritable> {

    private static long startVertexId = -1;

    public SSSPVertex() {
      this.setValue(new LongWritable(Long.MAX_VALUE));
    

    public boolean isStartVertex(
        ComputeContext<LongWritable, LongWritable, LongWritable, LongWritable> context) {
      if (startVertexId == -1) {
        String s = context.getConfiguration().get(START_VERTEX);
        startVertexId = Long.parseLong(s);
      
      return getId().get() == startVertexId;
    

    @Override
    public void compute(
        ComputeContext<LongWritable, LongWritable, LongWritable, LongWritable> context,
        Iterable<LongWritable> messages) throws IOException {
      long minDist = isStartVertex(context) ? 0 : Integer.MAX_VALUE;
      for (LongWritable msg : messages) {
        if (msg.get() < minDist) {
          minDist = msg.get();
        
      

      if (minDist < this.getValue().get()) {
        this.setValue(new LongWritable(minDist));
        if (hasEdges()) {
          for (Edge<LongWritable, LongWritable> e : this.getEdges()) {
            context.sendMessage(e.getDestVertexId(), new LongWritable(minDist
                + e.getValue().get()));
          
        
      } else {
        voteToHalt();
      
    

    @Override
    public void cleanup(
        WorkerContext<LongWritable, LongWritable, LongWritable, LongWritable> context)
        throws IOException {
      context.write(getId(), getValue());
    
  

  public static class MinLongCombiner extends
      Combiner<LongWritable, LongWritable> {

    @Override
    public void combine(LongWritable vertexId, LongWritable combinedMessage,
        LongWritable messageToCombine) throws IOException {
      if (combinedMessage.get() > messageToCombine.get()) {
        combinedMessage.set(messageToCombine.get());
      
    

  

  public static class SSSPVertexReader extends
      GraphLoader<LongWritable, LongWritable, LongWritable, LongWritable> {

    @Override
    public void load(
        LongWritable recordNum,
        WritableRecord record,
        MutationContext<LongWritable, LongWritable, LongWritable, LongWritable> context)
        throws IOException {
      SSSPVertex vertex = new SSSPVertex();
      vertex.setId((LongWritable) record.get(0));
      String[] edges = record.get(1).toString().split(",");
      for (int i = 0; i < edges.length; i++) {
        String[] ss = edges[i].split(":");
        vertex.addEdge(new LongWritable(Long.parseLong(ss[0])),
            new LongWritable(Long.parseLong(ss[1])));
      

      context.addVertexRequest(vertex);
    

  

  public static void main(String[] args) throws IOException {
    if (args.length < 2) {
      System.out.println("Usage: <startnode> <input> <output>");
      System.exit(-1);
    

    GraphJob job = new GraphJob();
    job.setGraphLoaderClass(SSSPVertexReader.class);
    job.setVertexClass(SSSPVertex.class);
    job.setCombinerClass(MinLongCombiner.class);

    job.set(START_VERTEX, args[0]);
    job.addInput(TableInfo.builder().tableName(args[1]).build());
    job.addOutput(TableInfo.builder().tableName(args[2]).build());

    long startTime = System.currentTimeMillis();
    job.run();
    System.out.println("Job Finished in "
        + (System.currentTimeMillis() - startTime) / 1000.0 + " seconds");
  


```

The source code of SSSP is described as follows:

-   Row 19: Defines SSSPVertex, where:
    -   The vertex value indicates the current shortest distance from this vertex to source vertex startVertexId.
    -   The compute\(\) method uses the iteration formula d\[v\] = min\(d\[v\], d\[u\] + weight\(u, v\)\) to update the vertex value.
    -   The cleanup\(\) method writes the vertex and its shortest distance to the source vertex to the result table.
-   Row 58: If the vertex value does not change, voteToHalt\(\) is called to notify the framework that this vertex enters the halt status. The calculation ends when all vertices enter the  halt state.
-   Row 70: Defines MinLongCombiner and combines messages sent to the same vertex to optimize performance and reduce memory usage.
-   Row 83: Defines the SSSPVertexReader class, loads a graph,  and resolves each record in the table into a vertex. The first column of the record is the vertex ID, and the second column stores all edge sets starting from the vertex, such as 2:2, 3:1, 4:4.
-   Row 106: Runs the main program \(main function\), defines GraphJob, and specifies the implementation of  Vertex/GraphLoader/Combiner, and the input and output tables.

