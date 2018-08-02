# BiPartiteMatchiing {#concept_hq1_sym_vdb .concept}

A Bipartite graph means all the graph vertices can be separated into 2 sets, and 2 vertices corresponding to each Edge belong to the 2 sets respectively. For bipartite graph G, M is one of its sub-graphs. If any two edges in the edge set of M  are not attached to the same vertex, M is called a matching. The bipartite graph matching is usually used for information matching in scenarios with clear supply and demand relationships.

The basic concept of the algorithm is as follows:

-   From the first vertex on the left, unmatched vertices are selected to search for the augmenting path.
-   If an unmatched vertex is found, the search is successful.
-   The path information is updated. If the number of matching edges is increased by 1, the search is stopped.
-   If the augmenting path is not found, the search is no longer started from this vertex.

## Sample Code {#section_tyy_vym_vdb .section}

BiPartiteMatchiing The code of the algorithm is as follows:

```
import java.io.DataInput;
import java.io.DataOutput;
import java.io.IOException;
import java.util.Random;
import com.aliyun.odps.data.TableInfo;
import com.aliyun.odps.graph.ComputeContext;
import com.aliyun.odps.graph.GraphJob;
import com.aliyun.odps.graph.MutationContext;
import com.aliyun.odps.graph.WorkerContext;
import com.aliyun.odps.graph.Vertex;
import com.aliyun.odps.graph.GraphLoader;
import com.aliyun.odps.io.LongWritable;
import com.aliyun.odps.io.NullWritable;
import com.aliyun.odps.io.Text;
import com.aliyun.odps.io.Writable;
import com.aliyun.odps.io.WritableRecord;
public class BipartiteMatching {
  private static final Text UNMATCHED = new Text("UNMATCHED");
  public static class TextPair implements Writable {
    public Text first;
    public Text second;
    public TextPair() {
      first = new Text();
      second = new Text();
    
    public TextPair(Text first, Text second) {
      this.first = new Text(first);
      this.second = new Text(second);
    
    @ Override
    public void write(DataOutput out) throws IOException {
      first.write(out);
      second.write(out);
    
    @ Override
    public void readFields(DataInput in) throws IOException {
      first = new Text();
      first.readFields(in);
      second = new Text();
      second.readFields(in);
    
    @ Override
    public String toString() {
      return first + ": " + second;
    
  
  public static class BipartiteMatchingVertexReader extends
      GraphLoader<Text, TextPair, NullWritable, Text> {
    @ Override
    public void load(LongWritable recordNum, WritableRecord record,
        MutationContext<Text, TextPair, NullWritable, Text> context)
        throws IOException {
      BipartiteMatchingVertex vertex = new BipartiteMatchingVertex();
      vertex.setId((Text) record.get(0));
      vertex.setValue(new TextPair(UNMATCHED, (Text) record.get(1)));
      String[] adjs = record.get(2).toString().split(",");
      for (String adj : adjs) {
        vertex.addEdge(new Text(adj), null);
      
      context.addVertexRequest(vertex);
    
  
  public static class BipartiteMatchingVertex extends
  Vertex <Text, TextPair, NullWritable, Text> {
    private static final Text LEFT = new Text("LEFT");
    private static final Text RIGHT = new Text("RIGHT");
    private static Random rand = new Random();
    @ Override
    public void compute (
    ComputeContext<Text, TextPair, NullWritable, Text> context,
        Iterable messages) throws ioexception {
      if (isMatched()) {
        voteToHalt();
        return;
      
      switch ((int) context.getSuperstep() % 4) {
      case 0:
        if (isLeft()) {
          context.sendMessageToNeighbors(this, getId());
        
        break;
      case 1:
        if (isRight()) {
          Text luckyLeft = null;
          for (Text message : messages) {
            if (luckyLeft == null) {
              luckyLeft = new Text(message);
            } else {
              if (rand.nextInt(1) == 0) {
                luckyLeft.set(message);
              
            
          
          if (luckyLeft ! = null) {
            context.sendMessage(luckyLeft, getId());
          
        
        break;
      case 2:
        if (isLeft()) {
          Text luckyRight = null;
          for (Text msg : messages) {
            if (luckyRight == null) {
              luckyRight = new Text(msg);
            } else {
              if (rand.nextInt(1) == 0) {
                luckyRight.set(msg);
              
            
          
          if (luckyRight ! = null) {
            setMatchVertex(luckyRight);
            context.sendMessage(luckyRight, getId());
          
        
        break;
      case 3:
        if (isRight()) {
          for (Text msg : messages) {
            setMatchVertex(msg);
          
        
        break;
      
    
    @ Override
    public void cleanup(
    WorkerContext<Text, TextPair, NullWritable, Text> context)
        throws IOException {
      context.write(getId(), getValue().first);
    
    private boolean isMatched() {
      return ! getValue().first.equals(UNMATCHED);
    
    private boolean isLeft() {
      return getValue().second.equals(LEFT);
    
    private boolean isRight() {
      return getValue().second.equals(RIGHT);
    
    private void setMatchVertex(Text matchVertex) {
      getValue().first.set(matchVertex);
    
  
  private static void printUsage() {
    System.err.println("BipartiteMatching <input> <output> [maxIteration]");
  
  public static void main(String[] args) throws IOException {
    if (args.length < 2) {
      printUsage();
    
    GraphJob job = new GraphJob();
    job.setGraphLoaderClass(BipartiteMatchingVertexReader.class);
    job.setVertexClass(BipartiteMatchingVertex.class);
    job.addInput(TableInfo.builder().tableName(args[0]).build());
    job.addOutput(TableInfo.builder().tableName(args[1]).build());
    int maxIteration = 30;
    if (args.length > 2) {
      maxIteration = Integer.parseInt(args[2]);
    
    job.setMaxIteration(maxIteration);
    job.run();
  


```

