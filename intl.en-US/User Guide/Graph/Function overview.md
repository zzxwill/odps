# Function overview {#concept_w5k_fmm_vdb .concept}

## Running job {#section_lhz_gmm_vdb .section}

The MaxCompute console provides JAR commands to run MaxCompute Graph jobs. These command are used in the same way as to run [MapReduce](intl.en-US/User Guide/MapReduce/Summary/MapReduce.md) [JAR commands](intl.en-US/User Guide/MapReduce/Function Introduction/Command.md). 

This document introduces these commands.

```
Usage: jar [<GENERIC_OPTIONS>] <MAIN_CLASS> [ARGS]
                -conf <configuration_file> Specify an application configuration file
                -classpath <local_file_list> classpaths used to run mainClass
                -D <name>=<value> Property value pair, which is used to run mainClass
                -local Run job in local mode
                -resources <resource_name_list> file/table resources used in graph, separated by command

```

< GENERIC\_OPTIONS\> can be the following parameters \(all are optional\):

-   -conf < configuration file \>: Specifies the JobConf configuration file.
-   -classpath < local\_file\_list \>: Indicates the class path for local implementation. It is mainly used to specify the JAR  package containing the main function.

    The main function and Graph job are usually  written in the same package, for example, in the Single Source Shortest Path \(SSSP\) package. Therefore, the -resources and -classpath parameters in the sample code both contain the  JAR package.The difference is that -resources references the value of the Graph job and runs in a distributed environment, while -classpath references the main  function and runs locally. The specified JAR package path is also a local file path. Package names are separated using system default file delimiters. Generally,  the delimiter is a semicolon \(;\) in a Windows system and a comma \(,\) in a Linux system.

-   -D < prop\_name \> = < prop\_value \>: Specifies the Java attributes of < mainClass \> for  local implementation. Multiple attributes can be defined.
-   -local: Runs the Graph job in local mode, which is mainly used for program debugging.
-   -resources <resource\_name\_list \>: Indicates the resource statement used for Graph job running.  Generally, the name of the resource where the Graph job is located must be specified in resource\_name\_list. If you read other MaxCompute resources in the Graph job,  the resource names must be added to  resource\_name\_list.  Resource names are separated by commas. When resources are used across projects, PROJECT\_NAME/resources/ must be prefixed,  for example, `-resources otherproject/resources/resfile;`.

In addition, you can run the main function of the Graph job to directly submit the job to MaxCompute, rather than submitting the job through the MaxCompute console. The following section uses the [PageRank algorithm](intl.en-US/User Guide/Graph/Examples/PageRank.md)  as an example:

```
public static void main(String[] args) throws Exception {
                    if (args.length < 2)
    printUsage();
  Account account = new AliyunAccount(accessId, accessKey);
  Odps odps = new Odps(account);
  odps.setEndpoint(endPoint);
  odps.setDefaultProject(project);
  SessionState ss = SessionState.get();
  ss.setOdps(odps);
  ss.setLocalRun(false);
  String resource = "mapreduce-examples.jar";
  GraphJob job = new GraphJob();
  // Add the JAR file in use and other files to class cache resource, corresponding to resources specified by -libjars in the JAR command
  job.addCacheResourcesToClassPath(resource);
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
      + (System.currentTimeMillis() - startTime) / 1000.0
      + " seconds");

```

## Input and output {#section_ibh_rmm_vdb .section}

MaxCompute Graph jobs must be input and output using tables.You cannot customize input and output formats.

The following example shows how to define a job input. Multiple inputs are supported:

```
GraphJob job = new GraphJob();
job.addInput(TableInfo.builder().tableName(“tblname”).build()); //Table as input
job.addInput(TableInfo.builder().tableName(“tblname”).partSpec("pt1=a/pt2=b").build()); //Shard as input
//Read-only columns col2 and col0 of the input table. In the load() method of GraphLoader, column col2 is obtained by record.get(0), and the sequence is the same
job.addInput(TableInfo.builder().tableName(“tblname”).partSpec("pt1=a/pt2=b").build(), new String[]{"col2", "col0"});
```

**Note:** 

-   For more information about the job input definition, see the description of the addInput\(\) method in GraphJob. The framework reads records in the input table and transmits them to custom  GraphLoader to load data.
-   Restrictions: Currently, shard filtering conditions are not supported. For more information about restrictions on applications, see [Application restrictions](intl.en-US/User Guide/Graph/Restriction.md).

The following example shows how to define a job output. Multiple job outputs are supported.  Each output is marked using a label:

```
GraphJob job = new GraphJob();
//If the output table is a shard table, the last level of shards must be provided
job.addOutput(TableInfo.builder().tableName("table_name").partSpec("pt1=a/pt2=b").build());
// Parameter true indicates overwriting shards specified by tableinfo, that is, the meaning of INSERT OVERWRITE. Parameter false indicates the meaning of INSERT INTO
job.addOutput(TableInfo.builder().tableName("table_name").partSpec("pt1=a/pt2=b").lable("output1").build(), true);
```

**Note:** 

-   For more information about the job output definition, see the description of the addOutput\(\) method in GraphJob.
-   When a Graph job runs, records can be written to an output table using the write\(\) method of WorkerContext. Labels must be specified for multiple outputs, such as  “output1” in the preceding section.
-   For more restrictions on applications, see [Application restrictions](intl.en-US/User Guide/Graph/Restriction.md).

## Read resources {#section_azs_1nm_vdb .section}

-   **Add resources to the graph program**

    In addition to JAR commands, you can use  the following two methods of GraphJob to specify resources read by Graph:

    ```
    void addCacheResources(String resourceNames)
    void addCacheResourcesToClassPath(String resourceNames)
    ```

-   **Use resources in graph program**

    To read resources in the Graph program,  follow these steps:

    ```
    public byte[] readCacheFile(String resourceName) throws IOException;
                                        public Iterable<byte[]> readCacheArchive(String resourceName) throws IOException;
                                        public Iterable<byte[]> readCacheArchive(String resourceName, String relativePath)throws IOException;
                                        public Iterable<WritableRecord> readResourceTable(String resourceName);
    public BufferedInputStream readCacheFileAsStream(String resourceName) throws IOException;
    public Iterable<BufferedInputStream> readCacheArchiveAsStream(String resourceName) throws IOException;
    public Iterable<BufferedInputStream> readCacheArchiveAsStream(String resourceName, String relativePath) throws IOException;
    
    ```

    **Note:** 

    -   Resources are generally read using the setup\(\) method of WorkerComputer, stored in Worker Value,  and obtained using the getWorkerValue\(\) method.
    -   To reduce overall memory consumption, use the preceding stream APIs so that resources can be read and processed simultaneously.
    -   For more information about restrictions on applications, see [Application restrictions](intl.en-US/User Guide/Graph/Restriction.md).

