# The glossary of Maxcompute {#concept_wxh_rxv_tdb .concept}

This article lists the common concepts and terminologies of MaxCompute. For detailed description, please refer to the link in this article.

## A {#section_utx_txv_tdb .section}

-   AccessKey

    Access Key \(AK for short, including Access Key Id and Access Key Secret\) is the key to access the Aliyun API. After registering cloud account on Ali cloud official website,it can be generated on the Accesskeys management page to identify users and do signature verification for accessing MaxComputer or other cloud products. Access Key Secret must be kept secret.

-   Security

    MaxCompute multi-tenant data security system mainly includes user authentication, user and authorization management in project space, resource sharing across project space and data protection in project space. For more details on MaxCompute security operation, see [Safety Guide](../../../../reseller.en-US/User Guide/Security/Target users.md#).


## C {#section_rhc_vxv_tdb .section}

-   Console

    MaxCompute Console is a client tool running under Windows/Linux. It can submit commands to complete project management, DDL, DML and other operations through Console. For tool installation and common parameters, See the [Client](../../../../reseller.en-US/Tools and Downloads/Client.md#) for tool installations and common parameters.


## D {#section_o1d_vxv_tdb .section}

-   Data Type

    The data type corresponding to all columns in the MaxCompute table. For data types currently supported, see [Basic Concepts\>Data types](../../../../reseller.en-US/User Guide/Definition/Data types.md#).

-   DDL

    Data Definition Language.Like creating tables, creating views, and so on, MaxCompute Div syntax see [User's Guide\>DDL](../../../../reseller.en-US/User Guide/SQL/DDL SQL/Table Operations.md#).

-   DML

    Data Manipulation Language. For example, INSERT operations, MaxCompute DML syntax, please see [Insert Operation](../../../../reseller.en-US/User Guide/SQL/Insert Operation/INSERT OVERWRITE__INTO.md#) .


## F {#section_ptd_vxv_tdb .section}

-   Fuxi

    Fuxi is the module responsible for resource management and task scheduling in the core of Flying Platform. It also provides a basic programming framework for application development. The bottom task scheduling module of MaxCompute is the scheduling module of Fuxi.


## I {#section_l5d_3ts_xfb .section}

-   Instance \(Instance\)

    A specific instance of a job that represents a job that actually runs, similar to the concept of a job in hadoop. See [Basic Concepts\>Task Instance](../../../../reseller.en-US/User Guide/Definition/Instance.md#) for details.


## M {#section_f2f_vxv_tdb .section}

-   MapReduce

    MaxCompute a programming model for processing data, usually used for parallel operation of large data sets. You can use the interface provided by MapReduce \(Java API\) to write MapReduce programs to process data in MaxCompute. The idea of programming is to divide data processing methods into Map \(mapping\) and Reduce \(Protocol\).

    Before formally executing Map, a partition is required. A slice is a cut of input data into equal-size blocks, each of which acts as a single map. The input of the worker is processed so that multiple Map Worker can work together.Each Map Worker reads in its own data, calculates and processes them, and finally integrates the intermediate results through the Reduce function to get the final results. For details, refer to the [User's guide\>MapReduce](../../../../reseller.en-US/User Guide/MapReduce/Summary/MapReduce.md#).


## O {#section_xwf_vxv_tdb .section}

-   ODPS

    ODPS is the original name of MaxCompute.


## P {#section_wpg_vxv_tdb .section}

-   Partition \(partition\)

    Partition partition refers to a table, based on the partition field \(one or more combinations\) divide the data store. That is, if the table does not have a partition, the data is placed directly under the directory where the table is located. If a table has a partition, each partition corresponds to one of the directories in the table, the data is stored separately in a different partition directory. For more information about partitions, see [Basic Concepts\>Partitions](../../../../reseller.en-US/User Guide/Definition/Partition.md#).

-   Project \(Project\)

    Project is the basic organizational unit of MaxCompute. It is similar to the concept of database or Scheme in traditional database, and is the main boundary of multi-user isolation and access control. For details, please see [Basic Concepts\>Project](../../../../reseller.en-US/User Guide/Definition/Projects.md#) .


## R {#section_tp2_vxv_tdb .section}

-   Role \(role\)

    Roles are concepts used in MaxCompute security functions and can be seen as a collection of users with the same privileges. Multiple users can appear at one role at the same time, and a user can also belong to multiple roles. After all roles are authorized, all users under the role have the same permissions. For more information about role management, please see [User's guide\>Role management](../../../../reseller.en-US/User Guide/Security/Role management.md#) .

-   Resource \(resources\)

    Resource \(Resource\) is a unique concept in MaxCompute. If you want to use MaxCompute's custom function \(UDF\) or MapReduce function, you need to rely on resources to complete it. For details, please see [Basic Concepts\>Resource](../../../../reseller.en-US/User Guide/Definition/Resource.md#).


## S {#section_n3h_vxv_tdb .section}

-   SDK

    Software Development Kits. Generally, it is a collection of development tools used by software engineers to build application software for specific software packages, software instances, software frameworks, hardware platforms, operating systems, document packages, etc. MaxCompute currently supports [Java SDK](../../../../reseller.en-US/SDK Reference /Java SDK.md#)and Python SDK.

-   Authorization

    The project space administrator or project owner gives you permission to perform certain operations on Objects \(or objects, such as tables, tasks, resources, etc.\) in MaxCompute, including reading, writing, viewing, etc. For the specific operation of authorization, see [User management](../../../../reseller.en-US/User Guide/Security/User management.md).

-   Sandbox

    Security restrictions: MaxCompute MapReduce and UDF programs are restricted by Java Sandbox while running in a distributed environment.


## T {#section_ib3_vxv_tdb .section}

-   Table \(table\)

    The table is the data storage unit of MaxCompute. See [Basic Concepts\>Table](../../../../reseller.en-US/User Guide/Definition/Resource.md#).

-   Tunnel

    MaxCompute's data channels provide high concurrency offline data upload and download service. You can use the tunnel service to bulk upload or download data to MaxCompute. Please refer to the tunnel command operation or bulk data channel SDK for relevant commands.


## U {#section_e53_vxv_tdb .section}

-   UDF

    Generalized UDF, user defined Function, the Java programming interface provided by MaxCompute develops custom functions, for more information, refer to [User 's guide\>UDF](../../../../reseller.en-US/User Guide/SQL/UDF/UDF Summary.md#).

    In a narrow sense, UDF refers to user-defined scalar function, whose input and output are one-to-one, that is, reading in a row of data and writing out an output value.

-   UDAF

    User Defined Aggregation Function, Custom aggregation function, whose input and output are many-to-one, aggregates multiple input records into one output value. It can be combined with the Group By statement in SQL. For details, please see [Java UDF\>UDAF](../../../../reseller.en-US/User Guide/SQL/UDF/Java UDF.md#).

-   UDTF

    User Defined Table Valued Function,customictablevaluedFunction, the userDefinedTablevaluedFunction, the customictablevaluedFunction, the function of the function called to the function, the set to the the function to the function, the function to the set to the value to the returned the function, the the function of the function of the the function of the function call to the value of the [Java UDF\>UDAF](../../../../reseller.en-US/User Guide/SQL/UDF/Java UDF.md#).


