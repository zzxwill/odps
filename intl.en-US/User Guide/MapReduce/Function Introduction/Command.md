# Command {#concept_m3w_kyf_vdb .concept}

The MaxCompute console provides a JAR command to run MapReduce  job. The detailed syntax is shown as follows:

```
Usage:  
jar [<GENERIC_OPTIONS>] <MAIN_CLASS> [ARGS];
        -conf <configuration_file> Specify an application configuration file
        -resources <resource_name_list> file\table resources used in mapper or reducer, seperate by comma
        -classpath <local_file_list> classpaths used to run mainClass
        -D <name>=<value> Property value pair, which will be used to run mainClass
        -l Run job in local mode
For example:
    jar -conf /home/admin/myconf -resources a.txt,example.jar -classpath ../lib/example.jar:./other_lib.jar -Djava.library.path=./native -Xmx512M mycompany.WordCount -m 10 -r 10 in out;

```

  `<GENERIC_OPTIONS>` includes the following parameters \(optional parameters\):

-   -conf < configuration file \>: Specify an JobConf configuration file。
-   -resources < resource\_name\_list \>: Indicates the resource statement used in MapReduce running time.  Generally, the resource name in which Map/Reduce function is included must be specified in ‘resource\_name\_list’.

    **Note:** 

    If the user has read other MaxCompute resources in the Map/Reduce function, then these resource names also need to be added  in ‘source\_name\_list’. 

    Multiple resources are separated by commas. If you must use span project resources, then add the prefix `PROJECT/resources/`, for example: `-resources otherproject/resources/resfile`. 

    For more information about how to read the resource in the Map/Reduce function, see [Use Resource Example](reseller.en-US//MapReduce/Program Example/Resource samples.md).

-   -classpath < local\_file\_list \>: the classpath used to specify the local JAR package of ‘main’  class \(include relative paths and absolute paths\).

    Package names are separated using system default file delimiters. Generally, the delimiter is a semicolon \(;\) in a Windows system and  a comma \(,\) in a Linux system.

    **Note:** In most cases, users generally write the main class and Map/Reduce function in a package, such as [WordCount  Code Example](reseller.en-US//MapReduce/Program Example/WordCount samples.md). This means that, in the running period of the example program,  mapreduce-examples.jar appears in ‘-resources’ parameter and ‘-classpath’ parameter,  however, ‘-resources’ references the Map/Reduce function, and runs in a distributed environment,  while  ‘-classpath’ references ‘Main’ class, and runs locally. The specified path of the JAR package is also a local path.

-   -D < prop\_name \>=< prop\_value \> : Multiple Java properties of < mainClass \>  in local mode can be defined.
-   -l: run MapReduce job in local mode, mainly used for program debugging.

User can specify the configuration file ‘JobConf’ by option ‘-conf’. This file can modify the JobConf settings in the SDK. 

An example  of a configuration file ‘JobConf’ is as follows:

```
<configuration>
       <property>
          <name>import.filename</name>
          <value>resource.txt</value>
       </property>
    </configuration>

```

In the preceding example, the variable ‘import.filename’ is defined and its value is ‘resource.txt’. 

User can get this variable value through the JobConf interface in the MapReduce program. Alternatively, users can also get the value through the JobConf interface in the SDK. For a detailed example, see [Use Resource Example](reseller.en-US//MapReduce/Program Example/Resource samples.md).

Example:

```
add jar data\mapreduce-examples.jar;
    jar -resources mapreduce-examples.jar -classpath mapreduce-examples.jar
        org.alidata.odps.mr.examples.WordCount wc_in wc_out;
    add file data\src.txt;
    add jar data\mapreduce-examples.jar;
    jar -resources src.txt,mapreduce-examples.jar -classpath data\mapreduce-examples.jar
        org.alidata.odps.mr.examples.WordCount wc_in wc_out;
    add file data\a.txt;
    add table wc_in as test_table;
    add jar data\work.jar;
    jar -conf odps-mapred.xml -resources a.txt,test_table,work.jar
        -classpath data\work.jar:otherlib.jar
        -D import.filename=resource.txt org.alidata.odps.mr.examples.WordCount args;
```

