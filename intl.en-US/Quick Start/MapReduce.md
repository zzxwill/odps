# MapReduce {#concept_blb_kxd_vdb .concept}

The following section explains how to use the MapReduce coding model with MaxCompute. For the example code in the procedure, it is assumed that the MaxCompute console is installed. 

**Note:** Maven users can search odps-sdk-mapred from the [Maven Library](http://search.maven.org/) to get the required SDK \(available in different versions\). The configuration is as follows:

```
<dependency>
    <groupId>com.aliyun.odps</groupId>
    <artifactId>odps-sdk-mapred</artifactId>
    <version>0.26.2-public</version>
 
</dependency>
```

## Preparation {#section_ht1_byd_vdb .section}

-   JDK1.7 is required to compile and run MapReduce.
-   For inforation about [installing the MaxCompute console](../../../../intl.en-US/Prepare/Install and configure a client.md), see Quick Start. For infromation about using the MaxCompute client, see[Console](../../../../intl.en-US/Tools and Downloads/Client.md).

## Procedure {#section_mcx_3yd_vdb .section}

1.  After installing and configuring the client, open odpscmd.bat and enter the appropriate project space.
2.  Input build table statements to create Input and Output tables. An example is shown as follows:

    ```
    CREATE TABLE wc_in (key STRING, value STRING);
    CREATE TABLE wc_out (key STRING, cnt BIGINT);
    -- Create input table and output table
    ```

    For information about using SQL statements to create tables, see [Table Operations](../../../../intl.en-US/User Guide/Common commands/Table Operations.md).

3.  Upload data.

    You can upload data in two ways.

    -   Use Tunnel Commands to upload data:

        ```
        tunnel upload kv.txt wc_in
        --  Upload example data
        ```

        The data is shown in kv.txt as follows:

        ```
        238,val_238
        186,val_86
        186,val_86
        ```

    -   You can also insert data directly using the INSERT statement as follows:

        ```
        insert into table wc_in select '238',' val_238' from (select count(*) from wc_in) a;
        ```

4.  Write MapReduce program and compile it.

    MaxCompute supports an Eclipse development plug-in to help quickly develop MapReduce programs and provide a local debugging MapReduce function.

    Users must create a MaxCompute project in Eclipse first, and then write the MapReduce program.  After the local debugging is run successfully, users can upload the compiled program to MaxCompute.  For more information, see [MapReduce Eclipse Plug-in](../../../../intl.en-US/Tools and Downloads/Eclipse Plugins/Install.md).

5.  Add .jar package into the project. \(in this example, the name of the JAR package is “word-count-1.0.jar”\):

    ```
    add jar word-count-1.0.jar;
    ```

6.  Run the “jar” command on the MaxCompute console:

    ```
    jar -resources word-count-1.0.jar -classpath /home/resources/word-count-1.0.jar com.taobao.jingfan.WordCount wc_in wc_out;
    ```

7.  Check the running result on the MaxCompute console:

    ```
    select * from wc_out;
    ```

    **Note:** If other resources are used in a Java program, you must add -resources parameters.  For more information about JAR commands, see [Jar Commands](../../../../intl.en-US/User Guide/MapReduce/Function Introduction/Command.md)*.*


