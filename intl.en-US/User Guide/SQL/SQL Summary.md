# SQL Summary {#concept_awk_jmb_5db .concept}

MaxCompute SQL is suitable for various scenarios including the following: Massive data \(GB, TB, or EB level\) must be processed based on offline batch calculation. It takes several seconds or even minutes to schedule after you submit a job, therefore MaxCompute SQL is most suitable for services which need to process tens of thousands of transactions per second.

The syntax of MaxCompute SQL is similar to SQL. It can be considered as a subset of standard SQL. But MaxCompute SQL is not equivalent to a database, which has no database characteristics in many aspects, such as transaction, primary key constraints, index, and so on. The maximum size of SQL in MaxCompute is 2 MB.

## Reserved Word {#section_nzx_m2l_vdb .section}

MaxCompute SQL considers the keywords of SQL statement as reserved words.  If you use keywords to name tables, columns, or partitions, you need to escape the keywords with the ```` symbol, otherwise  an error occurs. Reserved words are case insensitive. The reserved words in common use are shown as follows, for the complete reserved word list, see [MaxCompute SQL Reserved Word](intl.en-US/User Guide/SQL/Appendix/Reserved Words.md).

```
% & && ( ) * +  
 - . / ; < <= <>  
 =    >    >=    ?    ADD ALL ALTER  
 AND AS ASC BETWEEN BIGINT BOOLEAN BY  
 CASE CAST COLUMN COMMENT CREATE DESC DISTINCT  
 DISTRIBUTE DOUBLE DROP ELSE FALSE FROM FULL  
 GROUP IF IN INSERT INTO IS JOIN  
 LEFT LIFECYCLE LIKE LIMIT MAPJOIN NOT NULL  
 ON OR ORDER OUTER OVERWRITE PARTITION RENAME  
 REPLACE RIGHT RLIKE SELECT SORT STRING TABLE  
 THEN TOUCH TRUE UNION VIEW WHEN WHERE
```

## Type Conversion {#section_k1n_qfl_vdb .section}

MaxCompute SQL allows conversion between data types. The conversion methods include **explicit type conversion** and **implicit type conversion**. For more information, see [Type Conversion](intl.en-US/User Guide/SQL/Type conversion.md).

-   Explicit conversions: Uses CAST to convert a value type to another one.
-   Implicit conversions: MaxCompute automatically performs implicit conversions during running based on the context environment and conversion rules. Implicit conversion scope includes various operators, built-in functions, and so on.

## Partitioned Table {#section_m1n_qfl_vdb .section}

MaxCompute SQL supports partitioned table. Specifying the partition can bring lot of conveniences to users. For example, improve SQL running efficiency, reduce the cost, and so on. For more information about partition, see [Partition](../../../../intl.en-US/Product Introduction/Definition/Partition.md).

## UNION ALL {#section_n1n_qfl_vdb .section}

To be involved in a UNION ALL operation, the data type of columns, column numbers and column names must be consistent, otherwise an error occurs.

