# Triangle Count {#concept_ojb_5cn_vdb .concept}

This algorithm is used to calculate the number of triangles passing through each vertex.

The algorithm is implemented using the following steps:

1.  Each vertex sends its ID to all outbound neighbors.
2.  Store inbound and outbound neighbors and sends them to the outbound neighbors.
3.  Calculate the number of endpoint intersections for each Edge, get the sum, and output the result to the table.
4.  Get the sum of the output result in the table, divide it by 3, and get the number of triangles.

## Sample code {#section_u5k_ycn_vdb .section}

Code for the triangle count algorithm are as follows:

```
import java.io.IOException;
import com.aliyun.odps.data.TableInfo;
import com.aliyun.odps.graph.ComputeContext;
import com.aliyun.odps.graph.Edge;
import com.aliyun.odps.graph.GraphJob;
import com.aliyun.odps.graph.GraphLoader;
import com.aliyun.odps.graph.MutationContext;
import com.aliyun.odps.graph.Vertex;
import com.aliyun.odps.graph.WorkerContext;
import com.aliyun.odps.io.LongWritable;
import com.aliyun.odps.io.NullWritable;
import com.aliyun.odps.io.Tuple;
import com.aliyun.odps.io.Writable;
import com.aliyun.odps.io.WritableRecord;

 * Compute the number of triangles passing through each vertex.
 
 * The algorithm can be computed in three supersteps:
 * I. Each vertex sends a message with its ID to all its outgoing
 * neighbors.
 * II. The incoming neighbors and outgoing neighbors are stored and
 * send to outgoing neighbors.
 * III. For each edge compute the intersection of the sets at destination
 * vertex and sum them, then output to table.
 
 * The triangle count is the sum of output table and divide by three since
 * each triangle is counted three times.
 
 
public class TriangleCount {
  public static class TCVertex extends
    Vertex<LongWritable, Tuple, NullWritable, Tuple> {
    @Override
    public void setup(
        WorkerContext<LongWritable, Tuple, NullWritable, Tuple> context)
      throws IOException {
      // collect the outgoing neighbors
      Tuple t = new Tuple();
      if (this.hasEdges()) {
        for (Edge<LongWritable, NullWritable> edge : this.getEdges()) {
          t.append(edge.getDestVertexId());
        }
      
      this.setValue(t);
    
    @Override
    public void compute(
        ComputeContext<LongWritable, Tuple, NullWritable, Tuple> context,
        Iterable<Tuple> msgs) throws IOException {
      if (context.getSuperstep() == 0L) {
        // sends a message with its ID to all its outgoing neighbors
        Tuple t = new Tuple();
        t.append(getId());
        context.sendMessageToNeighbors(this, t);
      } else if (context.getSuperstep() == 1L) {
        // store the incoming neighbors
        for (Tuple msg : msgs) {
          for (Writable item : msg.getAll()) {
            if (! this.getValue().getAll().contains((LongWritable)item)) {
              this.getValue().append((LongWritable)item);
            
          
        
        // send both incoming and outgoing neighbors to all outgoing neighbors
        context.sendMessageToNeighbors(this, getValue());
      } else if (context.getSuperstep() == 2L) {
        // count the sum of intersection at each edge
        long count = 0;
        for (Tuple msg : msgs) {
          for (Writable id : msg.getAll()) {
            if (getValue().getAll().contains(id)) {
              count ++;
            
          
        
        // output to table
        context.write(getId(), new LongWritable(count));
        this.voteToHalt();
      
    
  
  public static class TCVertexReader extends
  GraphLoader<LongWritable, Tuple, NullWritable, Tuple> {
    @Override
    public void load(
        LongWritable recordNum,
        WritableRecord record,
        MutationContext<LongWritable, Tuple, NullWritable, Tuple> context)
    throws IOException {
      TCVertex vertex = new TCVertex();
      vertex.setId((LongWritable) record.get(0));
      String[] edges = record.get(1).toString().split(",");
      for (int i = 0; i < edges.length; i++) {
        try {
          long destID = Long.parseLong(edges[i]);
          vertex.addEdge(new LongWritable(destID), NullWritable.get());
        } catch(NumberFormatException nfe) {
          System.err.println("Ignore " + nfe);
        
      
      context.addVertexRequest(vertex);
    
  
  public static void main(String[] args) throws IOException {
  if (args.length < 2) {
  System.out.println("Usage: <input> <output>");
      System.exit(-1);
    
    GraphJob job = new GraphJob();
    job.setGraphLoaderClass(TCVertexReader.class);
    job.setVertexClass(TCVertex.class);
    job.addInput(TableInfo.builder().tableName(args[0]).build());
    job.addOutput(TableInfo.builder().tableName(args[1]).build());
    long startTime = System.currentTimeMillis();
    job.run();
    System.out.println("Job Finished in "
        + (System.currentTimeMillis() - startTime) / 1000.0 + " seconds");
  


```

