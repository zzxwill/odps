# Kmeans {#concept_svw_slm_vdb .concept}

The Kmeans algorithm is a typical clustering algorithm.

It performs clustering by using k number of vertices in the space as the centers and grouping the vertices closest to them.  The values of the clustering centers are successively updated through iterations until the optimal clustering result is obtained.

To divide a sample set into k classes, the algorithm operates as follows:

1.  Selects the initial centers of k classes.
2.  Calculates the distance from any sample to the k centers in iteration i, and groups the sample to the class of the nearest center.
3.  Updates the center value of the class using the mean and other methods.
4.  For all k clustering centers, if the value updated after iterations remains unchanged or is smaller than a threshold, the iteration ends. Otherwise, the iteration continues.

## Sample Code {#section_zmw_pxm_vdb .section}

Code for the K-means clustering algorithm is as follows:

```
import java.io.DataInput;
import java.io.DataOutput;
import java.io.IOException;

import org.apache.log4j.Logger;

import com.aliyun.odps.io.WritableRecord;
import com.aliyun.odps.graph.Aggregator;
Import com. aliyun. ODPS. graph. computercontext;
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
import com.aliyun.odps.io.Tuple;
import com.aliyun.odps.io.Writable;

public class Kmeans {
  private final static Logger LOG = Logger.getLogger(Kmeans.class);

  public static class KmeansVertex extends
  Vertex<Text, Tuple, NullWritable, NullWritable> {

    @ Override
    public void compute(
    ComputeContext<Text, Tuple, NullWritable, NullWritable> context,
    Iterable<NullWritable> messages) throws IOException {
      context.aggregate(getValue());
    }

  

  public static class KmeansVertexReader extends
  GraphLoader<Text, Tuple, NullWritable, NullWritable> {
    @Override
    public void load(LongWritable recordNum, WritableRecord record,
    MutationContext<Text, Tuple, NullWritable, NullWritable> context)
        throws IOException {
      KmeansVertex vertex = new KmeansVertex();
      vertex.setId(new Text(String.valueOf(recordNum.get())));
      vertex.setValue(new Tuple(record.getAll()));
      context.addVertexRequest(vertex);
    

  

  public static class KmeansAggrValue implements Writable {

    Tuple centers = new Tuple();
    Tuple sums = new Tuple();
    Tuple counts = new Tuple();

    @ Override
    public void write(DataOutput out) throws IOException {
      centers.write(out);
      sums.write(out);
      counts.write(out);
    

    @Override
    public void readFields(DataInput in) throws IOException {
      centers = new Tuple();
      centers.readFields(in);
      sums = new Tuple();
      sums.readFields(in);
      counts = new Tuple();
      counts.readFields(in);
    

    @Override
    public String toString() {
      return "centers " + centers.toString() + ", sums " + sums.toString()
          + ", counts " + counts.toString();
    

  

public static class KmeansAggregator extends Aggregator<KmeansAggrValue> {

    @SuppressWarnings("rawtypes")
    @Override
    public KmeansAggrValue createInitialValue(WorkerContext context)
        throws IOException {
      KmeansAggrValue aggrVal = null;
      if (context.getSuperstep() == 0) {
        aggrVal = new KmeansAggrValue();
        aggrVal.centers = new Tuple();
        aggrVal.sums = new Tuple();
        aggrVal.counts = new Tuple();

        byte[] centers = context.readCacheFile("centers");
        String lines[] = new String(centers).split("\n");

for (int i = 0; i < lines.length; i++) {
          String[] ss = lines[i].split(",");
          Tuple center = new Tuple();
          Tuple sum = new Tuple();
          for (int j = 0; j < ss.length; ++j) {
            center.append(new DoubleWritable(Double.valueOf(ss[j].trim())));
            sum.append(new DoubleWritable(0.0));
          
          LongWritable count = new LongWritable(0);
          aggrVal.sums.append(sum);
          aggrVal.counts.append(count);
          aggrVal.centers.append(center);
        
      } else {
        aggrVal = (KmeansAggrValue) context.getLastAggregatedValue(0);
      

      return aggrVal;
    

    @Override
    Public void aggregate (glasvalue, object item ){
      int min = 0;
      double mindist = Double.MAX_VALUE;
      Tuple point = (Tuple) item;

for (int i = 0; i < value.centers.size(); i++) {
        Tuple center = (Tuple) value.centers.get(i);
        // use Euclidean Distance, no need to calculate sqrt
        double dist = 0.0d;
        for (int j = 0; j < center.size(); j++) {
          double v = ((DoubleWritable) point.get(j)).get()
              - ((DoubleWritable) center.get(j)).get();
          dist += v * v;
        
        if (dist < mindist) {
          mindist = dist;
          min = i;
        
      

      // update sum and count
      Tuple sum = (Tuple) value.sums.get(min);
      for (int i = 0; i < point.size(); i++) {
        DoubleWritable s = (DoubleWritable) sum.get(i);
        s.set(s.get() + ((DoubleWritable) point.get(i)).get());
      
      LongWritable count = (LongWritable) value.counts.get(min);
      count.set(count.get() + 1);
    

    @Override
    public void merge(KmeansAggrValue value, KmeansAggrValue partial) {
    for (int i = 0; i < value.sums.size(); i++) {
        Tuple sum = (Tuple) value.sums.get(i);
        Tuple that = (Tuple) partial.sums.get(i);
        for (int j = 0; j < sum.size(); j++) {
          DoubleWritable s = (DoubleWritable) sum.get(j);
          s.set(s.get() + ((DoubleWritable) that.get(j)).get());
        
      

for (int i = 0; i < value.counts.size(); i++) {
        LongWritable count = (LongWritable) value.counts.get(i);
        count.set(count.get() + ((LongWritable) partial.counts.get(i)).get());
      
    

    @SuppressWarnings("rawtypes")
    @Override
    public boolean terminate(WorkerContext context, KmeansAggrValue value)
        throws IOException {

      // compute new centers
      Tuple newCenters = new Tuple(value.sums.size());
      for (int i = 0; i < value.sums.size(); i++) {
        Tuple sum = (Tuple) value.sums.get(i);
        Tuple newCenter = new Tuple(sum.size());
        LongWritable c = (LongWritable) value.counts.get(i);
        for (int j = 0; j < sum.size(); j++) {

          DoubleWritable s = (DoubleWritable) sum.get(j);
          double val = s.get() / c.get();
          newCenter.set(j, new DoubleWritable(val));

          // reset sum for next iteration
          s.set(0.0d);
        
        // reset count for next iteration
        c.set(0);
        newCenters.set(i, newCenter);
      

      // update centers
      Tuple oldCenters = value.centers;
      value.centers = newCenters;

      LOG.info("old centers: " + oldCenters + ", new centers: " + newCenters);

      // compare new/old centers
      boolean converged = true;
      for (int i = 0; i < value.centers.size() && converged; i++) {
        Tuple oldCenter = (Tuple) oldCenters.get(i);
        Tuple newCenter = (Tuple) newCenters.get(i);
        double sum = 0.0d;
        for (int j = 0; j < newCenter.size(); j++) {
          double v = ((DoubleWritable) newCenter.get(j)).get()
              - ((DoubleWritable) oldCenter.get(j)).get();
          sum += v * v;
        
        double dist = Math.sqrt(sum);
        LOG.info("old center: " + oldCenter + ", new center: " + newCenter
            + ", dist: " + dist);
        // converge threshold for each center: 0.05
        converged = dist < 0.05d;
      

      if (converged || context.getSuperstep() == context.getMaxIteration() - 1) {
        // converged or reach max iteration, output centers
        for (int i = 0; i < value.centers.size(); i++) {
          context.write(((Tuple) value.centers.get(i)).toArray());
        
        // true means to terminate iteration
        return true;
      

      // false means to continue iteration
      return false;
    
  

  private static void printUsage() {
  System. out. println ("Usage: <in> <out> [Max iterations (default 30)] ");
    System.exit(-1);
  

  public static void main(String[] args) throws IOException {
  if (args.length < 2)
      printUsage();

    GraphJob job = new GraphJob();

    job.setGraphLoaderClass(KmeansVertexReader.class);
    job.setRuntimePartitioning(false);
    job.setVertexClass(KmeansVertex.class);
    job.setAggregatorClass(KmeansAggregator.class);
    job.addInput(TableInfo.builder().tableName(args[0]).build());
    job.addOutput(TableInfo.builder().tableName(args[1]).build());

    // default max iteration is 30
    job.setMaxIteration(30);
    if (args.length >= 3)
      job.setMaxIteration(Integer.parseInt(args[2]));

    long start = System.currentTimeMillis();
    job.run();
    System.out.println("Job Finished in "
        + (System.currentTimeMillis() - start) / 1000.0 + " seconds");
  


```

The source code of Kmeans is described as follows:

-   Row 26: Defines KmeansVertex. The compute\(\) method is simple. It calls the aggregate\(\) method of the context object  and transmits the value of the current vertex \(in Tuple type and expressed by vector\).
-   Row 38: Defines the KmeansVertexReader class, loads a graph, and resolves each record in the table as a vertex. The vertex ID does not matter, and transmitted recordNum  is used as the ID. The vertex value is the Tuple consisting of all columns of the record.
-   Row 83: Defines KmeansAggregator. This class encapsulates the main logic of the Kmeans algorithm, where:
    -   createInitialValue creates an initial value for each iteration \(k-class  center point\). In first iteration \(superstep equals to 0\), the value is the initial center point. Otherwise, the value is the new center point when the last iteration ends.
    -   The aggregate\(\) method calculates the distance from each vertex to centers of different classes, classifies the vertex as the class of the nearest center, and updates sum and count of the class.
    -   The merge\(\) method combines sums and counts collected by each Worker.
    -   The terminate\(\) method calculates the new center point based on sum and count of each class.  If the distance between the new and old center points is smaller than a threshold value or the number of iterations reaches the upper limit, the iteration ends \(false is returned\).  The final center point is written to the result table.
-   Row 236: Runs the main program \(main function\), defines GraphJob, and specifies the implementation of Vertex/GraphLoader/Aggregator,  the maximum number of iterations \(30 by default\), and the input and output tables.
-   Row 243: Specifies job.setRuntimePartitioning\(false\). For the Kmeans algorithm, vertices do not have to be distributed during graph loading.  If RuntimePartitioning is set to false, the performance for graph loading is improved.

