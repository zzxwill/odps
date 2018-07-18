# Basic differences with standard SQL and solutions {#concept_tcb_h12_5db .concept}

This article describes problems that occur frequently when users who are familiar with the relational database SQL use MaxCompute SQL.  For specific MaxCompute SQL syntax,  see [SQL  Overview](../../../../reseller.en-US/User Guide/SQL/SQL Summary.md).

## Basic differences of MaxCompute SQL {#section_zt4_pb2_5db .section}

**Scenarios **

-   Transactions are not supported. \(The commit and rollback actions are not supported. We recommend using the codes that have idempotence and support re-running. We also recommend  using Insert Overwrite, instead of Insert Into, to write data.\)

-   Indexes and primary and foreign key constraints are not supported.

-   Auto increment fields and default values are not supported.  If the default value exists, assign the value when writing the data.

**Table Partitioning**

-   A table supports 60,000 partitions.

-   [The number of partitions entered by a query cannot be greater than 10,000](https://help.aliyun.com/document_detail/43152.html).Otherwise an error is returned. If the partitions are list partitions and the query performs filtering based only on list partitions,  an error may be returned if the total number of partitions exceeds 10,000.

-   [The number of output partitions in a query cannot exceed 2048](https://help.aliyun.com/document_detail/44226.html).


**Precision**

-   The Double type has precision. Therefore, we do not recommend that you directly join two Double  fields using an equal sign \(=\).  We recommend deducting one number from the other, and consider the two numbers as the same if their difference is smaller than a preset value, for example, abs\(a1- a2\)< 0.000000001.

-   Currently, MaxCompute SQL supports the high-precision type Decimal.  If you have higher precision requirements, you can store the data as the string type and use UDF to implement such calculation.


**Data type conversion**

-   If two different field types need to be joined, to prevent unexpected errors, we recommend first converting the types before joining them. This also helps code maintenance.

-   For implicit conversions of dates and strings,  to input a string to a function that requires entering a date,  you can convert between the string and date according to the yyyy-mm-dd hh:mm:ss format.  For other formats, see [Date functions \> TO\_DATE](../../../../reseller.en-US/User Guide/SQL/Builtin Function/Date Functions.md).


## DDL difference and solutions { .section}

**Table structure**

-   The partition column name cannot be changed. Only the value corresponding to the partition column can be changed.  For the specific differences between partition columns and partitions, see  [FAQs](https://help.aliyun.com/document_detail/40278.html).

-   You can add columns but cannot delete columns and modify the data type of columns. For more information, see  [SQL FAQs](https://help.aliyun.com/document_detail/40292.html). If you must delete columns or modify the column data types, see Modify the structure of MaxCompute tables for the most secure procedure.


## DML difference and solutions {#section_bz4_hd2_5db .section}

**INSERT**

-   The most intuitive syntax difference is that Insert into/overwrite is followed by the keyword  **Table**.

-   Currently, only syntax of Insert Into/Overwrite Table TableName Select supports batch data insert. Syntax of Insert Into/Overwrite Table TableName Values\(xxx\) does not support the batch data insert. To write a single record of data, see Insert a single record of data using MaxCompute SQL.


**UPDATE/DELETE**

-   Currently, the Update/Delete statements are not supported. If you want to perform the update and delete actions, see  [Update and Delete data](https://help.aliyun.com/document_detail/40275.html).

**SELECT**

-   [The number of input tables cannot exceed 16](https://help.aliyun.com/document_detail/44309.html).

-   The Select field in the Group by query is either the group field of Group By or the aggregate function to be used.  From the logic perspective,  if a non-group column and Group By Key have multiple records of data, the data cannot be displayed without the aggregate function.

-   MaxCompute does not support Group by cube \(group by with rollup\). However, you can use the union clause to simulate Group by cube, for example,  `select  k1, null, sum(v) from t group by k1 union all select k1, k2, sum(v) from t  group by k1, k2;`.

 **Subquery** 

Subqueries must have aliases.  We recommend that all queries have aliases.

**IN/NOT IN**

-   The data volume in the subquery following In/Not In and Exist/Not Exist cannot exceed 1000. To solve the problem, see [this article](https://help.aliyun.com/document_detail/40282.html).  If the uniqueness of the results returned by the subquery is guaranteed in the business aspect, you can consider removing  Distinct to improve the query performance.

**10,000 results returned by MaxCompute SQL**

-   MaxCompute limits the number of data records returned by the separately executed Select clause. For the specific configurations, [ see Other operations](../../../../reseller.en-US/User Guide/Common commands/Other Operations.md). The maximum data number is set to 10,000. If the number of data records to be queried is large, see  [this article](https://help.aliyun.com/document_detail/40333.html) to obtain all data while using the Tunnel Command.

**MAPJOIN**

-   The Join clause does not support Cartesian products. The Join clause must use the on clause to set the joining conditions.  If you want to create broadcast tables for small tables, you must use MapJoin Hint.  For more information, see this [article](https://help.aliyun.com/document_detail/40268.html).

**ORDER BY**

-   **Order By must be followed by Limit n**. If you want to sort a large number of data records, or even sort the full table, you can  set N to a large number.  However, use this method with caution. Because the system does not have advantages of distributed systems, the performance may be affected.  For more information, see [Sort the queried data in MaxCompute](https://help.aliyun.com/document_detail/40302.html).

**UNION ALL**

-   The data types, quantities, and names of all columns involved in the UNION ALL calculation must be consistent. Otherwise, an exception is returned.

-   The UNION ALL query must be nested by a subquery.


