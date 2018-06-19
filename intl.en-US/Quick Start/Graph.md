# Graph {#concept_gzg_1c2_vdb .concept}

This article uses [SSSP Algorithm](../../../../intl.en-US/User Guide/Graph/Examples/SSSP.md) as an example to show how to submit Graph jobs.

Submitting a [Graph](../../../../intl.en-US/User Guide/Graph/Summary.md) job is similar to submitting a job using [MapReduce](../../../../intl.en-US/User Guide/MapReduce/Function Introduction/Command.md).  Maven users can search `odps-sdk-graph` from [Maven Library](http://search.maven.org/) to get the Java SDK \(available in different versions\). The related configuration information is as follows:

```
<dependency>
    <groupId>com.aliyun.odps</groupId>
    <artifactId>odps-sdk-graph</artifactId>
    <version>0.20.7</version>
</dependency>
```

## Procedure {#section_esm_lc2_vdb .section}

1.  Enter the console and run odpscmd.
2.  Create Input Table and Output Table .

    ```
    create table sssp_in (v bigint, es string);
    create table sssp_out (v bigint, l bigint);
    ```

    For the statement to create table, see [SQL Create](../../../../intl.en-US/User Guide/SQL/DDL SQL.md).

3.  Upload Data.

    The contents of local data is as follows:

    ```
    2, 2, 3, 4, 4
    2 1:2,3:2,4:1
    3 1:1,2:2,5:1
    4 1:4,2:1,5:1
    5 3:1,4:1
    ```

    tab button is taken as the separator of two columns, Upload the data.

    ```
    tunnel u -fd " " sssp.txt sssp_in;
    ```

4.  Write SSSP example.

    According to the introduction in [Graph Eclipse Plug-in](../../../../intl.en-US/Tools and Downloads/Eclipse Plugins/Detailed introduction.md), compile and debug [SSSP Example](../../../../intl.en-US/User Guide/Graph/Examples/SSSP.md) on local. In this example, we assume the code is packaged as odps-graph-example-sssp.jar.

    **Note:** You only need to package the SSSP code. You do not need to package the SDK in odps-graph-example-sssp.jar.

5.  Add JAR package.

    ```
    add jar $LOCAL_JAR_PATH/odps-graph-example-sssp.jar
    ```

    **Note:** For resource creation, see [Resource Operation](../../../../intl.en-US/User Guide/Common commands/Resource.md).

6.  Run SSSP.

    ```
    jar -libjars odps-graph-example-sssp.jar -classpath $LOCAL_JAR_PATH/odps-graph-example-sssp.jar com.aliyun.odps.graph.example.SSSP 1 sssp_in sssp_out;
    ```

    Jar command is used to run MaxCompute Graph. Its use method is consistent with [MapReduce](../../../../intl.en-US/User Guide/MapReduce/Function Introduction/Command.md).

    When Graph job is running, corresponding instance ID, execution schedule and result summary are printed on the command line,

    as follows:

    ```
    ID = 20130730160742915gl205u3
    2013-07-31 00:18:36 SUCCESS
    Summary:
    Graph Input/Output
    Total input bytes=211
    Total input records = 5
    Total output bytes=161
    Total output records=5
    Graph_input _ [BSP. sssp_in] _ bytes = 211
    Graph_input _ [BSP. sssp_in] _ records = 5
    graph_output_[bsp.sssp_out]_bytes=161
    Graph_output _ [BSP. sssp_out] _ records = 5
    Graph statistics
    Total edges=14
    Total halted vertices=5
    Total sent messages=28
    Total supersteps=4
    Total vertices=5
    Total workers=1
    Graph timers
    Average superstep time (milliseconds) = 7
    Load time (milliseconds)=8
    Max superstep time (milliseconds) =14
    Max time superstep=0
    Min superstep time (milliseconds) = 5
    Min time superstep=2
    Setup Time (milliseconds) = 277
    Shutdown Time (milliseconds) = 20
    Total superb time (milliseconds) = 30
    Total time (milliseconds)=344
    OK
    ```

    **Note:** If you need to use the Graph function, simply open the submit Graph calculation job.


