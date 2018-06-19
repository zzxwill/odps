# Linear Regression {#concept_fmf_xzm_vdb .concept}

In statistics, linear regression is a statistical analysis method used to determine the dependency between two or more variables. Different from the classification algorithm that processes discrete prediction, 

the regression algorithm can predict the continuous value type.  The linear regression algorithm defines the loss function as the sum of the least square errors of the sample set. It minimizes the loss function to calculate the weight vector.

A common solution is gradient descent that:

1.  Initialize the weight vector to give descent speed rate and iterations \(or iteration convergence condition\).
2.  Calculate the least square error for each sample.
3.  Get the sum of the least square error, update the weight based on the descent speed rate.
4.  Repeat iterations until convergence.

## Sample Code {#section_mql_qcn_vdb .section}

```
import java.io.DataInput;
import java.io.DataOutput;
import java.io.IOException;
import com.aliyun.odps.data.TableInfo;
import com.aliyun.odps.graph.Aggregator;
import com.aliyun.odps.graph.ComputeContext;
import com.aliyun.odps.graph.GraphJob;
import com.aliyun.odps.graph.MutationContext;
import com.aliyun.odps.graph.WorkerContext;
import com.aliyun.odps.graph.Vertex;
import com.aliyun.odps.graph.GraphLoader;
import com.aliyun.odps.io.DoubleWritable;
import com.aliyun.odps.io.LongWritable;
import com.aliyun.odps.io.NullWritable;
import com.aliyun.odps.io.Tuple;
import com.aliyun.odps.io.Writable;
import com.aliyun.odps.io.WritableRecord;

 * LineRegression input: y,x1,x2,x3,......
 
public class LinearRegression {
  public static class GradientWritable implements Writable {
    Tuple lastTheta;
    Tuple currentTheta;
    Tuple tmpGradient;
    LongWritable count;
    DoubleWritable lost;
    @Override
    public void readFields(DataInput in) throws IOException {
      lastTheta = new Tuple();
      lastTheta.readFields(in);
      currentTheta = new Tuple();
      currentTheta.readFields(in);
      tmpGradient = new Tuple();
      tmpGradient.readFields(in);
      count = new LongWritable();
      count.readFields(in);
      /* update 1: add a variable to store lost at every iteration */
      lost = new DoubleWritable();
      lost.readFields(in);
    
    @Override
    public void write(DataOutput out) throws IOException {
      lastTheta.write(out);
      currentTheta.write(out);
      tmpGradient.write(out);
      count.write(out);
      lost.write(out);
    
  
  public static class LinearRegressionVertex extends
  Vertex<LongWritable, Tuple, NullWritable, NullWritable> {
    @Override
    public void compute(
    ComputeContext<LongWritable, Tuple, NullWritable, NullWritable> context,
    Iterable<NullWritable> messages) throws IOException {
      context.aggregate(getValue());
    
  
  public static class LinearRegressionVertexReader extends
  GraphLoader<LongWritable, Tuple, NullWritable, NullWritable> {
    @Override
    public void load(LongWritable recordNum, WritableRecord record,
    MutationContext<LongWritable, Tuple, NullWritable, NullWritable> context)
        throws IOException {
      LinearRegressionVertex vertex = new LinearRegressionVertex();
      vertex.setId(recordNum);
      vertex.setValue(new Tuple(record.getAll()));
      context.addVertexRequest(vertex);
    
  
  public static class LinearRegressionAggregator extends
  Aggregator<GradientWritable> {
    @SuppressWarnings("rawtypes")
    @Override
    public GradientWritable createInitialValue(WorkerContext context)
        throws IOException {
      if (context.getSuperstep() == 0) {
        /* set initial value, all 0 */
        GradientWritable grad = new GradientWritable();
        grad.lastTheta = new Tuple();
        grad.currentTheta = new Tuple();
        grad.tmpGradient = new Tuple();
        grad.count = new LongWritable(1);
        grad.lost = new DoubleWritable(0.0);
        int n = (int) Long.parseLong(context.getConfiguration()
            .get("Dimension"));
            for (int i = 0; i < n; i++) {
          grad.lastTheta.append(new DoubleWritable(0));
          grad.currentTheta.append(new DoubleWritable(0));
          grad.tmpGradient.append(new DoubleWritable(0));
        
        return grad;
      } else
        return (GradientWritable) context.getLastAggregatedValue(0);
    
    public static double vecMul(Tuple value, Tuple theta) {
      /* perform this partial computing: y(i)−hθ(x(i)) for each sample */
      /* value denote a piece of sample and value(0) is y */
      double sum = 0.0;
      for (int j = 1; j < value.size(); j++)
        sum += Double.parseDouble(value.get(j).toString())
            * Double.parseDouble(theta.get(j).toString());
      Double tmp = Double.parseDouble(theta.get(0).toString()) + sum
          - Double.parseDouble(value.get(0).toString());
      return tmp;
    
    @Override
    public void aggregate(GradientWritable gradient, Object value)
        throws IOException {
      
       * perform on each vertex--each sample i：set theta(j) for each sample i
       * for each dimension
       
      double tmpVar = vecMul((Tuple) value, gradient.currentTheta);
      
       * update 2:local worker aggregate(), perform like merge() below. This
       * means the variable gradient denotes the previous aggregated value
       
      gradient.tmpGradient.set(0, new DoubleWritable(
          ((DoubleWritable) gradient.tmpGradient.get(0)).get() + tmpVar));
      gradient.lost.set(Math.pow(tmpVar, 2));
      
       * calculate (y(i)−hθ(x(i))) x(i)(j) for each sample i for each
       * dimension j
       
       for (int j = 1; j < gradient.tmpGradient.size(); j++)
        gradient.tmpGradient.set(j, new DoubleWritable(
            ((DoubleWritable) gradient.tmpGradient.get(j)).get() + tmpVar
                * Double.parseDouble(((Tuple) value).get(j).toString())));
    
    @Override
    public void merge(GradientWritable gradient, GradientWritable partial)
        throws IOException {
      /* perform SumAll on each dimension for all samples. 
      Tuple master = (Tuple) gradient.tmpGradient;
      Tuple part = (Tuple) partial.tmpGradient;
      for (int j = 0; j < gradient.tmpGradient.size(); j++) {
        DoubleWritable s = (DoubleWritable) master.get(j);
        s.set(s.get() + ((DoubleWritable) part.get(j)).get());
      
      gradient.lost.set(gradient.lost.get() + partial.lost.get());
    
    @SuppressWarnings("rawtypes")
    @Override
    public boolean terminate(WorkerContext context, GradientWritable gradient)
        throws IOException {
      
       * 1. calculate new theta 2. judge the diff between last step and this
       * step, if smaller than the threshold, stop iteration
       
      gradient.lost = new DoubleWritable(gradient.lost.get()
          / (2 * context.getTotalNumVertices()));
      
       * we can calculate lost in order to make sure the algorithm is running on
       * the right direction (for debug)
       
      System.out.println(gradient.count + " lost:" + gradient.lost);
      Tuple tmpGradient = gradient.tmpGradient;
      System.out.println("tmpGra" + tmpGradient);
      Tuple lastTheta = gradient.lastTheta;
      Tuple tmpCurrentTheta = new Tuple(gradient.currentTheta.size());
      System.out.println(gradient.count + " terminate_start_last:" + lastTheta);
      double alpha = 0.07; // learning rate
      // alpha =
      // Double.parseDouble(context.getConfiguration().get("Alpha"));
      /* perform theta(j) = theta(j)-alpha*tmpGradient */
      long M = context.getTotalNumVertices();
      
       * update 3: add (/M) on the code. The original code forget this step
       
       for (int j = 0; j < lastTheta.size(); j++) {
        tmpCurrentTheta
            .set(
                J,
                new DoubleWritable(Double.parseDouble(lastTheta.get(j)
                    .toString())
                    - alpha
                    / M
                    * Double.parseDouble(tmpGradient.get(j).toString())));
      
      System.out.println(gradient.count + " terminate_start_current:"
          + tmpCurrentTheta);
      // judge if convergence is happening.
      double diff = 0.00d;
      for (int j = 0; j < gradient.currentTheta.size(); j++)
        diff += Math.pow(((DoubleWritable) tmpCurrentTheta.get(j)).get()
            - ((DoubleWritable) lastTheta.get(j)).get(), 2);
      if (/*
      * Math.sqrt(diff) < 0.00000000005d ||
           */Long.parseLong(context.getConfiguration().get("Max_Iter_Num")) == gradient.count
          .get()) {
        context.write(gradient.currentTheta.toArray());
        return true;
      
      gradient.lastTheta = tmpCurrentTheta;
      gradient.currentTheta = tmpCurrentTheta;
      gradient.count.set(gradient.count.get() + 1);
      int n = (int) Long.parseLong(context.getConfiguration().get("Dimension"));
      
       * update 4: Important!!! Remember this step. Graph won't reset the
       * initial value for global variables at the beginning of each iteration
       
       for (int i = 0; i < n; i++) {
        gradient.tmpGradient.set(i, new DoubleWritable(0));
      
      return false;
    
  
  public static void main(String[] args) throws IOException {
    GraphJob job = new GraphJob();
    job.setGraphLoaderClass(LinearRegressionVertexReader.class);
    job.setRuntimePartitioning(false);
    job.setNumWorkers(3);
    job.setVertexClass(LinearRegressionVertex.class);
    job.setAggregatorClass(LinearRegressionAggregator.class);
    job.addInput(TableInfo.builder().tableName(args[0]).build());
    job.addOutput(TableInfo.builder().tableName(args[1]).build());
    job.setMaxIteration(Integer.parseInt(args[2])); // Numbers of Iteration
    job.setInt("Max_Iter_Num", Integer.parseInt(args[2]));
    job.setInt("Dimension", Integer.parseInt(args[3])); // Dimension
    job.setFloat("Alpha", Float.parseFloat(args[4])); // Learning rate
    long start = System.currentTimeMillis();
    job.run();
    System.out.println("Job Finished in "
        + (System.currentTimeMillis() - start) / 1000.0 + " seconds");
  


```

