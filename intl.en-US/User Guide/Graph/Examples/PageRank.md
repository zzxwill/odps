# PageRank {#concept_jvb_slm_vdb .concept}

PageRank is a typical algorithm used to calculate the web page ranking. In the input directed graph G, vertices indicate web pages. If a link exists between web pages A and B, an edge connecting A and B exists.

The basic concept of the algorithm is as follows:

-   Initialization: The vertex value indicates the rank value \(of the double type\) of PageRank. In the initial phase, the value of all vertices is 1/TotalNumVertices.
-   Iteration formula: PageRank\(i\) = 0.15/TotalNumVertices + 0.85 x sum. Sum indicates the sum of  PageRank\(j\)/out\_degree\(j\). \(j indicates all vertices pointing to vertex i.\)

The basic concept shows that the algorithm is applicable to solutions using the MaxCompute Graph program. Each vertex j maintains the value of PageRank. PageRank\(j\)/out\_degree\(j\) is sent to the adjacent vertex \(for voting\) in each iteration. In the next iteration, each vertex recalculates the PageRank value using the iteration formula.

## Sample Code {#section_jlb_4wm_vdb .section}

```
import java.io.IOException;

import org.apache.log4j.Logger;

import com.aliyun.odps.io.WritableRecord;
import com.aliyun.odps.graph.ComputeContext;
import com.aliyun.odps.graph.GraphJob;
import com.aliyun.odps.graph.GraphLoader;
import com.aliyun.odps.graph.MutationContext;
import com.aliyun.odps.graph.Vertex;
import com.aliyun.odps.graph.WorkerContext;
import com.aliyun.odps.io.DoubleWritable;
import com.aliyun.odps.io.LongWritable;
import com.aliyun.odps.io.NullWritable;
import com.aliyun.odps.data.TableInfo;
import com.aliyun.odps.io.Text;
import com.aliyun.odps.io.Writable;

public class PageRank {

  private final static Logger LOG = Logger.getLogger(PageRank.class);

  public static class PageRankVertex extends
      Vertex<Text, DoubleWritable, NullWritable, DoubleWritable> {

    @Override
    public void compute(
        ComputeContext<Text, DoubleWritable, NullWritable, DoubleWritable> context,
        Iterable<DoubleWritable> messages) throws IOException {
      if (context.getSuperstep() == 0) {
        setValue(new DoubleWritable(1.0 / context.getTotalNumVertices()));
      } else if (context.getSuperstep() >= 1) {
        double sum = 0;
        for (DoubleWritable msg : messages) {
          sum += msg.get();
        
        DoubleWritable vertexValue = new DoubleWritable(
            (0.15f / context.getTotalNumVertices()) + 0.85f * sum);
        setValue(vertexValue);
      
      if (hasEdges()) {
        context.sendMessageToNeighbors(this, new DoubleWritable(getValue()
            .get() / getEdges().size()));
      
    

    @Override
    public void cleanup(
        WorkerContext<Text, DoubleWritable, NullWritable, DoubleWritable> context)
        throws IOException {
      context.write(getId(), getValue());
    
  

  public static class PageRankVertexReader extends
      GraphLoader<Text, DoubleWritable, NullWritable, DoubleWritable> {

    @Override
    public void load(
        LongWritable recordNum,
        WritableRecord record,
        MutationContext<Text, DoubleWritable, NullWritable, DoubleWritable> context)
        throws IOException {
      PageRankVertex vertex = new PageRankVertex();
      vertex.setValue(new DoubleWritable(0));
      vertex.setId((Text) record.get(0));
      System.out.println(record.get(0));

      for (int i = 1; i < record.size(); i++) {
        Writable edge = record.get(i);
        System.out.println(edge.toString());
        if (!( edge.equals(NullWritable.get()))) {
          vertex.addEdge(new Text(edge.toString()), NullWritable.get());
        
      
      LOG.info("vertex edgs size: "
          + (vertex.hasEdges() ? vertex.getEdges().size() : 0));
      context.addVertexRequest(vertex);
    

  

  private static void printUsage() {
    System.out.println("Usage: <in> <out> [Max iterations (default 30)]");
    System.exit(-1);
  

  public static void main(String[] args) throws IOException {
    if (args.length < 2)
      printUsage();

    GraphJob job = new GraphJob();

    job.setGraphLoaderClass(PageRankVertexReader.class);
    job.setVertexClass(PageRankVertex.class);
    job.addInput(TableInfo.builder().tableName(args[0]).build());
    job.addOutput(TableInfo.builder().tableName(args[1]).build());

    // default max iteration is 30
    job.setMaxIteration(30);
    if (args.length >= 3)
      job.setMaxIteration(Integer.parseInt(args[2]));

    long startTime = System.currentTimeMillis();
    job.run();
    System.out.println("Job Finished in "
        + (System.currentTimeMillis() - startTime) / 1000.0 + " seconds");
  


```

The source code of PageRank is described as follows:

-   Row 23: Defines PageRankVertex, where:
    -   The vertex value indicates the current PageRank value of the vertex \(web page\).
    -   The compute\(\) method uses the iteration formula `PageRank(i) = 0.15/TotalNumVertices + 0.85 x sum` to update the vertex value.
    -   The cleanup\(\) method writes the vertex and its PageRank value to the result table.
-   Row 55: Defines the PageRankVertexReader class, loads a graph, and resolves each record in the table into a vertex. The first column of the record is the start vertex and other columns are the destination vertices.
-   Row 88: Runs the main program \(main function\), defines GraphJob, and specifies the implementation of Vertex/GraphLoader, the maximum number of iterations \(30 by default\),  and input and output tables.

