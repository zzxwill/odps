# Reading guidance {#concept_fyb_cjb_5db .concept}

## For first time users {#section_h1k_2jb_5db .section}

The following sections give recommended reading for users of differing expertise.

-   [MaxCompute Summary](reseller.en-US/Product Introduction/What is MaxCompute.md): Introduces MaxCompute, including its main function modules. By reading this chapter, you can have a general knowledge of MaxCompute.
-   [Quick Start](../../../../reseller.en-US/Prepare/Install and configure a client.md): Provides a step-by-step guide including how to apply for an account, install the client, create a table, authorize a user, export/import data, run SQL tasks, run UDF, and run Mapreduce programs.
-   [Basic Introduction](reseller.en-US/Product Introduction/What is MaxCompute.md): Details key terms and frequently used commands of MaxCompute. You can be further familiar with how to operate MaxCompute.
-   [Tools](../../../../reseller.en-US/Tools and Downloads/Client.md): Before analyzing the data, you may need to master how to download, configure and use the frequently used tools. 

    We provide the following [tool](../../../../reseller.en-US/Tools and Downloads/Client.md): You can operate MaxCompute through this tool.


After you are familiar with those modules that mentioned preceding, you are recommended to perform a further study on other modules.

## For data analysts {#section_l1k_2jb_5db .section}

If you are a data analyst, it is recommended that you read the contents of the SQL module.

-   [MaxCompute SQL](../../../../reseller.en-US/User Guide/SQL/SQL summary.md):  Query and analyze massive volumes of data that are stored on MaxCompute. It includes the following functions:
    -   Use DDL statements CREATE, DROP, and ALERT to manage tables and partitions.
    -   Use a SELECT statement to select records in a table, and use a WHERE clause to view the records meeting the filter condition.
    -   Associate two tables through an Equijoin operation.
    -   Aggregate columns using a GROUP BY statement.
    -   You can insert the result records into another table through Insert overwrite/into syntax.
    -   You can use built-in functions and user-defined functions \(UDF\) to complete a variety of computations.

## For developers {#section_o1k_2jb_5db .section}

If you have a certain level of development experience, understanding the concept of distribution, and some data analysis may not be possible with SQL, you are advised to learn more about MaxCompute's advanced functional modules. As shown in the following:

-   [MapReduce](../../../../reseller.en-US/User Guide/MapReduce/Summary/MapReduce.md): Explains the MapReduce programming interface.  You can use the Java API, which is provided by MapReduce,  to write MapReduce program for processing data in MaxCompute.
-   [Graph](../../../../reseller.en-US/User Guide/Graph/Summary.md): Provides a set of frameworks for iterative graph computing. This function uses graphs to build models. Graphs are composed of vertices and edges. Vertices and edges contain values. This process outputs a result after performing iterative graph editing and evolution.
-   [Eclipse Plugin](../../../../reseller.en-US/Tools and Downloads/Eclipse Plugins/Install.md): Facilitates you to use the Java SDK of MapReduce, UDF, and Graph for development work.
-   [Tunnel](../../../../reseller.en-US/User Guide/Data upload and download/Tunnel SDK/Summary.md): Facilitates users to use the Tunnel service to upload batch offline data to MaxCompute, or download batch offline data from MaxCompute.
-   SDK:

    -   [Java SDK](../../../../reseller.en-US/User Guide/SDK/Java SDK.md): Provides developers with Java interfaces.
    -   [Python SDK](../../../../reseller.en-US/User Guide/SDK/Python SDK.md): Provides developers with Python interfaces.
    **Note:** [MapReduce](../../../../reseller.en-US/User Guide/MapReduce/Summary/MapReduce.md) and [Graph](../../../../reseller.en-US/User Guide/Graph/Summary.md) are still in open beta, and if you want to use this feature, applications can be submitted through the job system. Please specify the name of your project  when you apply, and we will process it within 7 working days.


## For project owners/administrators {#section_s1k_2jb_5db .section}

If you are a project owner or administrator, you are recommended to read:

-   [Security](../../../../reseller.en-US/User Guide/Security/Target users.md): Explains how to grant privileges to a user, share resource span projects, set project protection, and grant privilege by policy, and so on.
-   [Billing](../../../../reseller.en-US/Pricing/Billing.md#): Details the pricing of MaxCompute.
-   Commands that only the project owner can use. For example, the **SetProject** operation in [Others](../../../../reseller.en-US/User Guide/Common commands/Other operations.md) of [Common Commands](../../../../reseller.en-US/User Guide/Common commands/Overview of Common commands.md).

