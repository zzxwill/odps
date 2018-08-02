# Topology Sorting {#concept_gb4_pzm_vdb .concept}

In directed edge \(u,v\), all vertex sequences satisfying u < v are called topological sequences. Topological sorting is an algorithm used to calculate the topological sequence of a directed graph.

Specifically, the algorithm:

1.  Find a vertex that does not have any inbound edge from the graph and outputs the vertex.
2.  Delete the vertex and all outbound edges from the graph.
3.  Repeat the preceding steps until all vertices are output.

## Sample Code {#section_ync_tzm_vdb .section}

The code for the topology ordering algorithm is as follows:

```
import java.io.IOException;
import org.apache.commons.logging.Log;
import org.apache.commons.logging.LogFactory;
import com.aliyun.odps.data.TableInfo;
import com.aliyun.odps.graph.Aggregator;
import com.aliyun.odps.graph.Combiner;
import com.aliyun.odps.graph.ComputeContext;
Import com. aliyun. ODPS. graph. graphjob;
import com.aliyun.odps.graph.GraphLoader;
import com.aliyun.odps.graph.MutationContext;
import com.aliyun.odps.graph.Vertex;
import com.aliyun.odps.graph.WorkerContext;
import com.aliyun.odps.io.LongWritable;
import com.aliyun.odps.io.NullWritable;
import com.aliyun.odps.io.BooleanWritable;
import com.aliyun.odps.io.WritableRecord;
public class TopologySort {
  private final static Log LOG = LogFactory.getLog(TopologySort.class);
  public static class TopologySortVertex extends
  Vertex<LongWritable, LongWritable, NullWritable, LongWritable> {
    @Override
    public void compute(
    ComputeContext<LongWritable, LongWritable, NullWritable, LongWritable> context,
    Iterable<LongWritable> messages) throws IOException {
      // in superstep 0, each vertex sends message whose value is 1 to its
      // neighbors
      if (context.getSuperstep() == 0) {
        if (hasEdges()) {
          context.sendMessageToNeighbors(this, new LongWritable(1L));
        
      } else if (context.getSuperstep() >= 1) {
        // compute each vertex's indegree
        long indegree = getValue().get();
        for (LongWritable msg : messages) {
          indegree += msg.get();
        
        setValue(new LongWritable(indegree));
        if (indegree == 0) {
          voteToHalt();
          if (hasEdges()) {
            context.sendMessageToNeighbors(this, new LongWritable(-1L));
          
          context.write(new LongWritable(context.getSuperstep()), getId());
          LOG.info("vertex: " + getId());
        
        context.aggregate(new LongWritable(indegree));
      
    
  
  public static class TopologySortVertexReader extends
  GraphLoader<LongWritable, LongWritable, NullWritable, LongWritable> {
    @Override
    public void load(
        LongWritable recordNum,
        WritableRecord record,
        MutationContext<LongWritable, LongWritable, NullWritable, LongWritable> context)
        throws IOException {
      TopologySortVertex vertex = new TopologySortVertex();
      vertex.setId((LongWritable) record.get(0));
      vertex.setValue(new LongWritable(0));
      String[] edges = record.get(1).toString().split(",");
      for (int i = 0; i < edges.length; i++) {
        long edge = Long.parseLong(edges[i]);
        if (edge >= 0) {
          vertex.addEdge(new LongWritable(Long.parseLong(edges[i])),
              NullWritable.get());
        
      
      LOG.info(record.toString());
      context.addVertexRequest(vertex);
    
  
  public static class LongSumCombiner extends
  Combiner<LongWritable, LongWritable> {
    @Override
    public void combine(LongWritable vertexId, LongWritable combinedMessage,
        LongWritable messageToCombine) throws IOException {
      combinedMessage.set(combinedMessage.get() + messageToCombine.get());
    
  
  public static class TopologySortAggregator extends
  Aggregator<BooleanWritable> {
    @SuppressWarnings("rawtypes")
    @Override
    public BooleanWritable createInitialValue(WorkerContext context)
        throws IOException {
      return new BooleanWritable(true);
    
    @Override
    public void aggregate(BooleanWritable value, Object item)
        throws IOException {
      boolean hasCycle = value.get();
      boolean inDegreeNotZero = ((LongWritable) item).get() == 0 ? false : true;
      value.set(hasCycle && inDegreeNotZero);
    
    @Override
    public void merge(BooleanWritable value, BooleanWritable partial)
        throws IOException {
      value.set(value.get() && partial.get());
    
    @SuppressWarnings("rawtypes")
    @Override
    public boolean terminate(WorkerContext context, BooleanWritable value)
        throws IOException {
      if (context.getSuperstep() == 0) {
        // since the initial aggregator value is true, and in superstep we don't
        // do aggregate
        return false;
      
      return value.get();
    
  
  public static void main(String[] args) throws IOException {
    if (args.length ! = 2) {
    System.out.println("Usage : <inputTable> <outputTable>");
      System.exit(-1);
    
    // The input table is in the format of
    // 0 1，2
    // 1 3
    // 2 3
    // 3 -1
    // The first column is vertexid, and the second column is the destination vertexid of the vertex edge. If the value is –1, the vertex does not have any outbound edge
    // The output table is in the format of
    // 0 0
    // 1 1
    // 1 2
    // 2 3
    // The first column is the supstep value, in which the topological sequence is hidden. The second column is vertexid
    // TopologySortAggregator is used to determine if the graph has loops
    // If the input graph has a loop, the iteration ends when the indegree of vertices in the active state is not 0
    // You can use records in the input and output tables to determine if the graph has loops
    GraphJob job = new GraphJob();
    job.setGraphLoaderClass(TopologySortVertexReader.class);
    job.setVertexClass(TopologySortVertex.class);
    job.addInput(TableInfo.builder().tableName(args[0]).build());
    job.addOutput(TableInfo.builder().tableName(args[1]).build());
    job.setCombinerClass(LongSumCombiner.class);
    job.setAggregatorClass(TopologySortAggregator.class);
    long startTime = System.currentTimeMillis();
    job.run();
    System.out.println("Job Finished in "
        + (System.currentTimeMillis() - startTime) / 1000.0 + " seconds");
  


```

