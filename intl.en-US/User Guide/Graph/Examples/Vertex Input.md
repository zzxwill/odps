# Vertex Input {#concept_w3n_ddn_vdb .concept}

Sample code

```
import java.io.IOException;
import com.aliyun.odps.conf.Configuration;
import com.aliyun.odps.data.TableInfo;
import com.aliyun.odps.graph.ComputeContext;
import com.aliyun.odps.graph.GraphJob;
import com.aliyun.odps.graph.GraphLoader;
import com.aliyun.odps.graph.Vertex;
import com.aliyun.odps.graph.VertexResolver;
import com.aliyun.odps.graph.MutationContext;
import com.aliyun.odps.graph.VertexChanges;
import com.aliyun.odps.graph.Edge;
import com.aliyun.odps.io.LongWritable;
import com.aliyun.odps.io.WritableComparable;
import com.aliyun.odps.io.WritableRecord;

 * The following example describes how to compile a graph job program to load data of different types.  It mainly describes how GraphLoader
 * and VertexResolver are cooperated to build the graph.
 
 * A MaxCompute Graph job uses MaxCompute tables as the input. Assume that a job has two tables as the input, one storing vertices and the other storing edges.
 * The format of the table storing vertex information is as follows:
 
 * | VertexID | VertexValue |
 
 * | id0| 9|
 
 * | id1| 7|
 
 * | id2| 8|
 
 
 * The format of the table storing edge information is as follows:
 
 * | VertexID | DestVertexID| EdgeValue|
 
 * | id0| id1| 1|
 
 * | id0| id2| 2|
 
 * | id2| id1| 3|
 
 
 * The preceding two tables show that id0 has two outbound edges pointing to id1 and id2 respectively. id2 has an outbound edge pointing to id1, and id1 has no outbound edges.
 
 * For data of this type, in GraphLoader::load(LongWritable, Record, MutationContext),
 * MutationContext#addVertexRequest(Vertex) can be used to add vertices to the graph, while
 * link MutationContext#addEdgeRequest(WritableComparable, Edge) can be used to add edges to the graph. In
 * link VertexResolver#resolve(WritableComparable, Vertex, VertexChanges, boolean)
 * vertices and edges added in the load() method are combined to a vertex object, which is used as the return value and added to the graph for calculation.
 
 
public class VertexInputFormat {
  private final static String EDGE_TABLE = "edge.table";
  
   * Resolve a record to vertices and edges. Each record indicates a vertex or an edge according to its source.
   
   * Similar to com.aliyun.odps.mapreduce.Mapper#map,
   * enter a record to generate key-value pairs. The keys are vertex IDs,
   * and the values are vertices or edges written based on the context. These key-value pairs are summarized based on vertex IDs using LoadingVertexResolver.
   
   * Note: Vertices or edges added here are requests sent based on the record content, and are not used in calculation. Only
   * vertices or edges added using VertexResolver participate in calculation.
   
  public static class VertexInputLoader extends
  GraphLoader<LongWritable, LongWritable, LongWritable, LongWritable> {
    private boolean isEdgeData;
    
     * Configure VertexInputLoader.
     
     * @param conf
     * Indicates the configuration parameters of a job, which are configured in the main GraphJob or set on the console.
     * @param workerId
     * Indicates the serial number of the operating Worker, which starts from 0 and can be used to build a unique vertex ID.
     * @param inputTableInfo
     * Indicates information about the input table load to the current Worker, which can be used to determine the type of currently input data, that is, the record format.
     
    @Override
    public void setup(Configuration conf, int workerId, TableInfo inputTableInfo) {
      isEdgeData = conf.get(EDGE_TABLE).equals(inputTableInfo.getTableName());
    
    
     * Based on the record content, resolve corresponding edges and send a request to add them to the graph.
     
     * @param recordNum
     * Indicates the record serial number, which starts from 1 and is separately counted in each Worker.
     * @param record
     * Indicates the record in the input table. It contains three columns, indicating the first vertex, last vertex, and edge weight.
     * @param context
     * Indicates the context, requesting to add resolved edges to the graph.
     
    @Override
    public void load(
        LongWritable recordNum,
        WritableRecord record,
        MutationContext<LongWritable, LongWritable, LongWritable, LongWritable> context)
        throws IOException {
      if (isEdgeData) {
        
         * Data is from the table that stores edge information.
         
         * 1. The first column indicates the first vertex ID.
         
        LongWritable sourceVertexID = (LongWritable) record.get(0);
        
         * 2. The second column indicates the last vertex ID.
         
        LongWritable destinationVertexID = (LongWritable) record.get(1);
        
         * 3. The third column indicates the edge weight.
         
        LongWritable edgeValue = (LongWritable) record.get(2);
        
         * 4. Create an edge that consists of the last vertex ID and edge weight.
         
         Edge<LongWritable, LongWritable> edge = new Edge<LongWritable, LongWritable>(
            destinationVertexID, edgeValue);
        
         * 5. Send a request to add an edge to the first vertex.
         
        context.addEdgeRequest(sourceVertexID, edge);
        
        * 6. If each record indicates a bidirectional edge, repeat steps 4 and 5. Edge<LongWritable, LongWritable> edge2 = new
        * Edge<LongWritable, LongWritable>( sourceVertexID, edgeValue);
         * context.addEdgeRequest(destinationVertexID, edge2);
         
      } else {
        
         * Data comes from the table that stores vertex information.
         
         * 1. The first column indicates the vertex ID.
         
        LongWritable vertexID = (LongWritable) record.get(0);
        
         * 2. The second column indicates the vertex value.
         
        LongWritable vertexValue = (LongWritable) record.get(1);
        
         * 3. Create a vertex that consists of the vertex ID and vertex value.
         
        MyVertex vertex = new MyVertex();
        
         * 4. Initialize the vertex.
         
        vertex.setId(vertexID);
        vertex.setValue(vertexValue);
        
         * 5. Send a request to add a vertex.
         
        context.addVertexRequest(vertex);
      
    
  
  
   * Summarize key-value pairs generated using GraphLoader::load(LongWritable, Record, MutationContext), which is similar to 
   * reduce in com.aliyun.odps.mapreduce.Reducer.  For the unique vertex ID, all actions such as
   * adding/deleting vertices or edges on the ID is stored in VertexChanges.
   
   * Note: Not only conflicting vertices or edges added by using the load() method are called. (A conflict occurs when multiple same vertex objects or duplicate edges are added.)
   * All IDs requested to be generated using the load() method are called.
   
  public static class LoadingResolver extends
  VertexResolver<LongWritable, LongWritable, LongWritable, LongWritable> {
    
     * Process a request about adding/deleting vertices or edges for an ID.
     
     * VertexChanges has four APIs, which correspond to the four APIs of MutationContext:
     * VertexChanges::getAddedVertexList() corresponds to 
     * MutationContext::addVertexRequest(Vertex).
     * In the load() method, if vertex objects with the same ID are requested to be added, such vertex objects are collected to the return list.
     * VertexChanges::getAddedEdgeList() corresponds to 
     * MutationContext::addEdgeRequest(WritableComparable, Edge)
     * If edge objects with the same first vertex ID are requested to be added, such edge objects are collected to the return list.
     * VertexChanges::getRemovedVertexCount() corresponds to 
     * MutationContext::removeVertexRequest(WritableComparable)
     * If vertices with the same ID are requested to be deleted, the number of total deletion requests is returned.
     * VertexChanges#getRemovedEdgeList() corresponds to 
     * MutationContext#removeEdgeRequest(WritableComparable, WritableComparable)
     * If edge objects with the same first vertex ID are requested to be deleted, such edge objects are collected to the return list.
     
     * By processing ID changes, you can state whether the ID participates in calculation using the return value. If the returned vertex is not null,
     * the ID participates in subsequent calculation. If the returned vertex is null, the ID does not participate in subsequent calculation.
     
     * @param vertexId
     * Indicates the ID of the vertex requested to be added or first vertex ID of the edge requested to be added.
     * @param vertex
     * Indicates an existing vertex object. Its value is always null in the data loading phase.
     * @param vertexChanges
     * Indicates the set of vertices or edges requested to be added/deleted on the ID.
     * @param hasMessages
     * Indicates whether the ID has any input message. Its value is always false in the data loading phase.
     
    @Override
    public Vertex<LongWritable, LongWritable, LongWritable, LongWritable> resolve(
        LongWritable vertexId,
        Vertex<LongWritable, LongWritable, LongWritable, LongWritable> vertex,
        VertexChanges<LongWritable, LongWritable, LongWritable, LongWritable> vertexChanges,
        boolean hasMessages) throws IOException {
      
       * 1. Obtain the vertex object for calculation.
       
      MyVertex computeVertex = null;
      if (vertexChanges.getAddedVertexList() == null
          || vertexChanges.getAddedVertexList().isEmpty()) {
        computeVertex = new MyVertex();
        computeVertex.setId(vertexId);
      } else {
        
         * Assume that each record indicates a unique vertex in the table storing vertex information.
         
        computeVertex = (MyVertex) vertexChanges.getAddedVertexList().get(0);
      
      
       * 2. Add the edge requested to be added to the vertex to the vertex object. If data is duplicated, perform deduplication based on the algorithm needs.
       
      if (vertexChanges.getAddedEdgeList() ! = null) {
      for (Edge<LongWritable, LongWritable> edge : vertexChanges
            .getAddedEdgeList()) {
          computeVertex.addEdge(edge.getDestVertexId(), edge.getValue());
        
      
      
       * 3. Return the vertex object and add it to the final graph for calculation.
       
      return computeVertex;
    
  
  
   * Determine actions of the vertex that participates in calculation.
   
   
  public static class MyVertex extends
  Vertex<LongWritable, LongWritable, LongWritable, LongWritable> {
    
     * Write the vertex edge to the result table according to the format of the input table.  Ensure that the format and data of the input and output tables are the same.
     
     * @param context
     * Indicates the context during running.
     * @param messages
     * Indicates the input message.
     
    @Override
    public void compute(
    ComputeContext<LongWritable, LongWritable, LongWritable, LongWritable> context,
    Iterable<LongWritable> messages) throws IOException {
      
       * Write the vertex ID and value to the result table storing vertices.
       
      context.write("vertex", getId(), getValue());
      
       * Write the vertex edge to the result table storing edges.
       
      if (hasEdges()) {
      for (Edge<LongWritable, LongWritable> edge : getEdges()) {
          context.write("edge", getId(), edge.getDestVertexId(),
              edge.getValue());
        
      
      
       * Perform one round of iteration.
       
      voteToHalt();
    
  
  
   * @param args
   * @throws IOException
   
  public static void main(String[] args) throws IOException {
  if (args.length < 4) {
      throw new IOException(
      "Usage: VertexInputFormat <vertex input> <edge input> <vertex output> <edge output>");
    
    
     * GraphJob is used to configure Graph jobs.
     
    GraphJob job = new GraphJob();
    
     * 1. Specify input graph data and the table storing edge data.
     
    job.addInput(TableInfo.builder().tableName(args[0]).build());
    job.addInput(TableInfo.builder().tableName(args[1]).build());
    job.set(EDGE_TABLE, args[1]);
    
     * 2. Specify the data loading mode, resolve the record as edges. Similar to the map, the generated key is the vertex ID, and the value is the edge.
     
    job.setGraphLoaderClass(VertexInputLoader.class);
    
     * 3. Specify the data loading phase, and generate the vertex for calculation.  Similar to reduce, edges generated by map are combined to a vertex.
     
    job.setLoadingVertexResolverClass(LoadingResolver.class);
    
     * 4. Specify actions of the vertex that participates in calculation.  The vertex.compute() method is used for each round of iteration.
     
    job.setVertexClass(MyVertex.class);
    
     * 5. Specify the output table of the Graph job, and write the calculation result to the result table.
     
    job.addOutput(TableInfo.builder().tableName(args[2]).label("vertex").build());
    job.addOutput(TableInfo.builder().tableName(args[3]).label("edge").build());
    
     * 6. Submit the job for execution.
     
    job.run();
  


```

