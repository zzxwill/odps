# MapReduce {#concept_fml_smf_vdb .concept}

This article describes the MapReduce programming interface supported by MaxCompute and its limitations.

MaxCompute provides three versions of MapReduce programming interface:

-   MaxCompute MapReduce: Native interface for MaxCompute, which is faster than other interfaces. It is more convenient to develop a program without exposing file system.

-   [MR2](reseller.en-US/User Guide/MapReduce/Summary/Extended MapReduce.md) \(Extended MapReduce\): The extension to MaxCompute, which supports more complex job scheduling logic. MapReduce  is implemented in the same way as the  MaxCompute native interface.

-   [Hadoop compatible version](reseller.en-US/User Guide/MapReduce/Summary/Open-source MapReduce.md): Highly compatible with [Hadoop MapReduce](http://hadoop.apache.org/docs/r1.0.4/cn/mapred_tutorial.html) ,  but not compatible with MaxCompute native interface and MR2.


The preceding three versions are basically the same in the [Basic concepts](reseller.en-US/User Guide/MapReduce/Function Introduction/Basic concepts.md), [Job submission](reseller.en-US/User Guide/MapReduce/Function Introduction/Commands.md), [Input and output](reseller.en-US/User Guide/MapReduce/Function Introduction/Input and Output.md), and [Resource](reseller.en-US/User Guide/MapReduce/Function Introduction/Resources.md), and the only difference is the Java SDK. This article introduces the principle of MapReduce. For more detailed description of MapReduce, see [Hadoop MapReduce Course](http://hadoop.apache.org/docs/r1.0.4/cn/mapred_tutorial.html).

**Note:** You are not yet able to read or write data from the external tables through MapReduce.

## Scenarios {#section_m5s_rwf_vdb .section}

MapReduce was originally proposed by Google as a distributed data processing model and is now widely applied in multiple business scenarios. The following are the examples:

-   Search: web crawl, flip index, PageRank.
-   Web access log analytics:
    -   Analyze and mine the web access, shopping behavior characteristics to achieve personalized recommendation.
    -   Analyze user's access behavior.
-   Statistics and analysis for the text:
    -   The Wordcount and TFIDF analysis of Mo Yan novels.
    -   Reference analysis and statistics of academic papers and patent documents.
    -   Wikipedia data analysis, and so on.
-   Massive Data Mining: Unstructured data, spatial and temporal data, image data mining.
-   Machine Learning: Supervised learning, unsupervised learning, classification algorithm such as decision tree, SVM, and so on..
-   Natural Language Processing:
    -   Training and forecasting based on big data.
    -   Based on the corpus to construct the current matrix of words, frequent itemset data mining, repeated document detection and so on.
-   Advertisement recommendations: User-click \(CTR\) and purchase behavior \(CVR\) forecasts.

## Processing data process {#section_l5j_vwf_vdb .section}

The processing data process of MapReduce is divided into two stages: Map and Reduce. Map must be executed first, and then Reduce. The processing logic of Map and Reduce is defined by the user, but must comply with the MapReduce framework protocol. The process is as follows:

1.  Before executing Map, the input data must be **sliced**, that is, input data is divided into blocks of equal size. Each block is processed as the input of a single Map Worker, so that multiple Map Workers can work simultaneously.
2.  After the slice is split, multiple Map Worker can work together. Each Map Worker performs computing after reading the data and output the result to Reduce. Because Map Worker outputs the data, it must specify a key for each output record. The value of this Key determines which Reduce Worker the data has been sent to. The relationship  between key value and Reduce Worker is an any-to-one relationship. Data with the same key is sent to the same Reduce Worker, and a single Reduce Worker  may receive data of multiple key values.
3.  Before Reduce stage, MapReduce framework sorts the data according to their Key values, and make sure data with same Key value is grouped together.  If a user specifies **Combiner**, the framework calls Combiner to aggregate the same key data. The user  must define the logic of Combiner. Compared to the classical MapReduce framework, the input parameter and output parameter of Combiner must be consistent with the Reduce in MaxCompute. This processing is generally called as **Shuffle**.
4.  At Reduce stage, data with the same key is shuffled to the same Reduce Worker. A Reduce Worker receives data from multiple Map  Workers. Each Reduce Worker executes Reduce operation for multiple records of the same key.  Then these multiple records become  a value through Reduce processing.

**Note:** A brief introduction to the MapReduce framework is mentioned in the preceding process. For more information, see relevant documents.

The following example uses WordCount to explain the stages of MaxCompute MapReduce. 

Assumethat a text named ‘a.txt’, where each row is indicated by a number, and the frequency of appearance of each number must be counted.  The number in the text is called as‘Word’ and the number appearance occurrence is called as 'Count'.  To complete this function through MaxCompute MapReduce, the following figure illustrates the required steps:

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/12013/15446004141922_en-US.jpg)

**Procedure:**

1.  First, text is sliced and the data in each slice is entered into a single Map Worker.
2.  Map processes the input. Once Map gets a number, it sets the Count as 1. Then, output <Word,Count\>queues sequence is followed. Take ‘Word’ as the  Key of output data.
3.  In the initial actions of Shuffle stage, the output of each Map Worker is sorted according to Key value \(value of Word\). The Combine  operation is executed after sorting to accumulate the Count of same Key value \(Word value\) and constitute a new <Word,Count\> queue. This process is called as the combiner sorting.
4.  In the later actions of Shuffle, data is transmitted to Reduce.  Reduce Worker sorts the data based on the Key value again  after receiving the data.
5.  At the time of processing data, each Reduce Worker adopts that same logic as that of a Combiner by accumulating Count with the same  Key value \(Word value\) to get the output.
6.  Result.

**Note:** Because the data in MaxCompute is stored in tables, the input and output of MaxCompute MapReduce  can only be a table. User-defined output is not allowed and the corresponding file system interface is not provided.

