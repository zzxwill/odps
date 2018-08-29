# Aggregator {#concept_cv5_ndn_vdb .concept}

This article explains the implementation and related APIs of Aggregator and uses KmeansClustering as an example to illustrate the use of Aggregator.

In MaxCompute Graph, Aggregator helps to collect and process global information. In MaxCompute Graph,  Aggregator is used to summarize and process global information.

## Aggregator implementation {#section_scb_rdn_vdb .section}

The logic of Aggregator is divided into the following two parts:

-   One part is run on all Workers in distributed mode.
-   The other part is only run on the Worker where AggregatorOwner is located in a single vertex mode.

Operations run on all Workers include creating an initial value and partial aggregation. The partial aggregation result is sent to the Worker where AggregatorOwner is located. The Worker then aggregates partial aggregation objects sent by common Workers to obtain a global aggregation result, and determines whether the iteration is ended or not. The global aggregation result is sent to all Workers over the next round of supersteps for the next iteration, as shown in the following figure.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/12062/15355058822317_en-US.png)

## Aggregator APIs {#section_st2_xdn_vdb .section}

Aggregator provides five APIs for user implementation. The following section describes the call time and application of the five APIs.

-   createStartupValue\(context\)

    This API runs once on all Workers. It is called before all supersteps start, and is generally used to initialize AggregatorValue. In the first superstep iteration \(superstep equals 0\), the AggregatorValue object  initialized by the API can be obtained by the call of WorkerContext.getLastAggregatedValue\(\) or ComputeContext.getLastAggregatedValue\(\).

-   createInitialValue\(context\)

    This API is called once on all Workers when each superstep is initiated. It is used to initialize  AggregatorValue for the current iteration. Generally, the result of the previous iteration is obtained through WorkerContext.getLastAggregatedValue\(\),  and partial initialization is run.

-   aggregate\(value, item\)

    This API runs on all Workers. It is triggered by an explicit call of  ComputeContext\#aggregate\(item\), while the preceding two APIs are automatically called by the framework. This API is used to run partial aggregation. The first parameter value indicates the result that the Worker has aggregated in the current superstep. \(The initial value is the object  returned by createInitialValue\). The second parameter is transmitted when the user code calls ComputeContext\#aggregate\(item\).  In this API, item is usually used to update value for aggregation. After all the aggregate operations are executed, the obtained value is the partial aggregation result of the Worker. Then, the result is sent by the framework to the Worker where AggregatorOwner is located.

-   merge\(value, partial\)

    This API runs by the Worker where AggregatorOwner is located. It is used to merge partial aggregation results of Workers to obtain the global aggregation object. Similar to aggregate, value indicates aggregated results, while partial indicates objects to be aggregated. Partial is used to update value.

    For example, assume that three Workers w0, w1, and w2 exist with the partial aggregation results of  p0, p1, and p2. If p1, p0, and p2 in sequence are sent to the Worker where the AggregatorOwner is located, then the merge  sequence will be as follows:

    1.  merge\(p1, p0\) runs first, and p1 and p0 are aggregated as p1’.
    2.  merge\(p1’, p2\) runs, and p1’ and p2 are aggregated as p1’’, which is the global aggregation result in this superstep.
    The preceding example shows that execution of the merge\(\) operation is not required when only one Worker exists. That is, merge\(\) is not called.

-   terminate\(context, value\)

    After the Worker where AggregatorOwner is located runs merge\(\), the framework calls terminate\(context, value\) to perform the final processing. The second parameter value indicates the global aggregation result obtained by merge\(\). The global aggregation can be modified further in this method. After terminate\(\) is run, the framework distributes global aggregation objects to all Workers for the next superstep. A special feature of terminate\(\) is that if true is returned, iteration of the entire job ends. Otherwise, iteration continues.  In machine learning scenarios, it is usually determined that a job ends when true is returned after convergence.


## KmeansClustering example {#section_x53_c2n_vdb .section}

The following section uses typical KmeansClustering as an example to describe how to use Aggregator. The following section uses KmeansClustering as an example to describe how to use the Aggregator.

**Note:** The complete code is provided in the Kmeans attachment. Here, the code is resolved in the following sequence.

-   **GraphLoader section**

    GraphLoader: The GraphLoader part is used to load an input table and convert it to a vertex or edge of a graph. Each row of data in the input table is a sample, a sample constructs a vertex, and VertexValue is used to store samples.

    Initially, a writable class KmeansValue is defined as the VertexValue  type:

    ```
    public static class KmeansValue implements Writable {
      DenseVector sample;
      public KmeansValue() { 
      
      public KmeansValue(DenseVector v) {
        this.sample = v;
      
      @Override
      public void write(DataOutput out) throws IOException {
        wirteForDenseVector(out, sample);
      
      @Override
      public void readFields(DataInput in) throws IOException {
        sample = readFieldsForDenseVector(in);  
      
    
    ```

    KmeansValue: A DenseVector object is encapsulated in KmeansValue to store a sample. The DenseVector type is from [matrix-toolkits-java](https://github.com/fommil/matrix-toolkits-java/). wirteForDenseVector\(\)  and readFieldsForDenseVector\(\) are used for serialization and deserialization. For more information, see the complete code in the Kmeans attachment.

    The custom KmeansReader code is as follows:

    ```
    public static class KmeansReader extends 
                                    GraphLoader<LongWritable, KmeansValue, NullWritable, NullWritable> {
      @Override
      public void load(
          LongWritable recordNum,
          WritableRecord record,
          MutationContext<LongWritable, KmeansValue, NullWritable, NullWritable> context)
          throws IOException {
        KmeansVertex v = new KmeansVertex();
        v.setId(recordNum);
        int n = record.size();
        DenseVector dv = new DenseVector(n);
        for (int i = 0; i < n; i++) {
          dv.set(i, ((DoubleWritable)record.get(i)).get());
        
        v.setValue(new KmeansValue(dv));
        context.addVertexRequest(v);
      
    
    
    ```

    In KmeansReader, a vertex is created when each row of data \(a record\) is read. recordNum is used as the vertex ID, and the record content is converted to the DenseVector object and encapsulated in VertexValue.

-   **Vertex**

    Custom KmeansVertex code:Regarding its logic, partial aggregation is performed for samples maintained in each iteration. For more information about its logic, see implementation of Aggregator in the following section:

    ```
    public static class KmeansVertex extends
                                    Vertex<LongWritable, KmeansValue, NullWritable, NullWritable> {
      @Override
      public void compute(
      ComputeContext<LongWritable, KmeansValue, NullWritable, NullWritable> context,
      Iterable<NullWritable> messages) throws IOException {
        context.aggregate(getValue());
      
    
    
    ```

-   **Aggregator**

    The main logic of entire Kmeans is centralized in Aggregator. Custom KmeansAggrValue is used to maintain the content to be aggregated and distributed.

    ```
    public static class KmeansAggrValue implements Writable {
      DenseMatrix centroids;
      DenseMatrix sums; // used to recalculate new centroids
      DenseVector counts; // used to recalculate new centroids
      @Override
      public void write(DataOutput out) throws IOException {
        wirteForDenseDenseMatrix(out, centroids);
        wirteForDenseDenseMatrix(out, sums);
        wirteForDenseVector(out, counts);
      
      @Override
      public void readFields(DataInput in) throws IOException {
        centroids = readFieldsForDenseMatrix(in);
        sums = readFieldsForDenseMatrix(in);
        counts = readFieldsForDenseVector(in);
      
    
    ```

    Three objects are maintained in KmeansAggrValue.  centroids indicates the existing K centers. If the sample is m-dimensional, centroids is a matrix of K x m.  sums is a matrix of the same size as centroids, and each element records the sum of a specific dimension of the sample closest to a specific center. For example, sums\(i,j\) indicates the sum of dimension j of the sample closest to center i.

    counts is a K-dimensional vector, records the number of samples closest to each center. sums and counts are used together to calculate a new center, which is a main content of aggregation.

    The next is KmeansAggregator used for custom Aggregator implementation. The following describes implementation in order of the preceding APIs.

    1.  Run createStartupValue\(\), see the following:

        ```
        public static class KmeansAggregator extends Aggregator<KmeansAggrValue> {
        public KmeansAggrValue createStartupValue(WorkerContext context) throws IOException {
        KmeansAggrValue av = new KmeansAggrValue();
        byte[] centers = context.readCacheFile("centers");
        String lines[] = new String(centers).split("\n");
        int rows = lines.length;
        int cols = lines[0].split(",").length; // assumption rows >= 1 
        av.centroids = new DenseMatrix(rows, cols);
        av.sums = new DenseMatrix(rows, cols);
        av.sums.zero();
        av.counts = new DenseVector(rows);
        av.counts.zero();
        for (int i = 0; i < lines.length; i++) {
          String[] ss = lines[i].split(",");
          for (int j = 0; j < ss.length; j++) {
            av.centroids.set(i, j, Double.valueOf(ss[j]));
          
        
        return av;
        
        
        ```

        In the preceding method, a KmeansAggrValue object is initialized, the initial center is read from the resource file centers, and a value is granted to centroids. The initial values of sums and counts are 0.

    2.  Run createInitialValue\(\), see the following:

        ```
        @Override
        public void aggregate(KmeansAggrValue value, Object item)
          throws IOException {
        DenseVector sample = ((KmeansValue)item).sample;
        // find the nearest centroid
        int min = findNearestCentroid(value.centroids, sample);
        // update sum and count
        for (int i = 0; i < sample.size(); i ++) {
          value.sums.add(min, i, sample.get(i));
        
        value.counts.add(min, 1.0d);
        
        ```

        In the createInitialValue\(\) method, findNearestCentroid\(\) is called to find the index of the center that has the shortest Euclidean distance with the sample item. Then, each dimension is added to sums, and the value of counts is plus 1. For more information about how to implement findNearestCentroid\(\), see the Kmeans attachment.


The preceding three functions run on all Workers to implement partial aggregation. The following describes global aggregation-related operations that run on the Worker where AggregatorOwner is located.

1.  Run merge:

    ```
    @Override
    public void merge(KmeansAggrValue value, KmeansAggrValue partial)
      throws IOException {
    value.sums.add(partial.sums);
    value.counts.add(partial.counts);
    
    ```

    The implementation logic of merge is to add values of sums and counts aggregated by each Worker .

2.  Run terminate\(\):

    ```
    @Override
    public boolean terminate(WorkerContext context, KmeansAggrValue value)
       throws IOException {
     // Calculate the new means to be the centroids (original sums)
     DenseMatrix newCentriods = calculateNewCentroids(value.sums, value.counts, value.centroids);
     // print old centroids and new centroids for debugging
     System.out.println("\nsuperstep: " + context.getSuperstep() + 
         "\nold centriod:\n" + value.centroids + " new centriod:\n" + newCentriods);
     boolean converged = isConverged(newCentriods, value.centroids, 0.05d);
     System.out.println("superstep: " + context.getSuperstep() + "/" 
         + (context.getMaxIteration() - 1) + " converged: " + converged);
     if (converged || context.getSuperstep() == context.getMaxIteration() - 1) {
       // converged or reach max iteration, output centriods
       for (int i = 0; i < newCentriods.numRows(); i++) {
         Writable[] centriod = new Writable[newCentriods.numColumns()];
         for (int j = 0; j < newCentriods.numColumns(); j++) {
           centriod[j] = new DoubleWritable(newCentriods.get(i, j));
         
         context.write(centriod);
       
       // true means to terminate iteration
       return true;
     
     // update centriods
     value.centroids.set(newCentriods);
     // false means to continue iteration
     return false;
    
    ```

    In terminate\(\), calculateNewCentroids\(\) is called based on sums and counts to calculate the average value and obtain the new center. Then, isConverged\(\) is called based on the Euclidean distance between the new and old centers to determine whether the center has been converged. If the number of convergences or iterations reaches the upper threshold, the new center is output, and true is returned to end the iteration. Otherwise, the center is updated, and false is returned to continue iteration.  For more information about how to implement calculateNewCentroids\(\) and isConverged\(\), see the attachment.


-   **main\(\) method**

    The main\(\) method is used to build GraphJob, perform related settings, and submit a job. The code is as follows:

    ```
    public static void main(String[] args) throws IOException {
                                    if (args.length < 2)
        printUsage();
      GraphJob job = new GraphJob();
      job.setGraphLoaderClass(KmeansReader.class);
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

    **Note:** If job.setRuntimePartitioning\(false\) is set to false, data loaded by each worker will not be partitioned based on Partitioner. That is, who loads the data maintains it.


## Conclusion {#section_sgj_wfn_vdb .section}

This article introduces the aggregator features in the MaxCompute  graph, the API meaning, and the kmeans Clustering example. To sum it up, Aggregator can be implemented as follows:

1.  Each Worker runs createStartupValue during startup to create AggregatorValue.
2.  Each Worker runs createInitialValue before each iteration initializes AggregatorValue in the current round.
3.  In an iteration, each vertex uses context.aggregate\(\) to run aggregate\(\), implementing partial iteration in the Worker.
4.  Each Worker sends the partial iteration result to the Worker where AggregatorOwner is located.
5.  The Worker where AggregatorOwner is located runs merge several times to implement global aggregation.
6.  The Worker where AggregatorOwner is located runs terminate to process the global aggregation result and determines whether to end the iteration.

## Attachment {#section_wrq_yfn_vdb .section}

[Kmeans](https://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/cn/odps/0.0.90/assets/graph/Kmeans.gz)

