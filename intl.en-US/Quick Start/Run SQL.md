# Run SQL {#concept_tgf_v5y_5db .concept}

MaxCompute SQL is used to query and analyze massive data in MaxCompute. The main functions of SQL are as follows:

-   Supports a variety of operators.
-   Uses DDL statements to manage tables, partitions, and views.
-   Uses Select statements to query records in tables and Where statements to filter records in tables.
-   Uses Insert statements to insert and update data.
-   Uses Join operations to join two tables. Supports Mapjoin operations for multiple small tables.
-   Supports to use the built-in and user-defined functions for computing.
-   Supports regular expressions.

This article gives you a brief introduction of the issues that need to be noticed using MaxCompute SQL.

**Note:** 

-   MaxCompute SQL does not support transactions, indexes, update/delete operations, and so on. At the same time, the SQL Syntax of MaxCompute is different from that of Oracle and MySQL, so that you cannot seamlessly migrate SQL statements from other databases to MaxCompute.
-   After you submit a MaxCompute job, it will take several dozen seconds to several minutes to schedule the job. Therefore, MaxCompute is suitable for batch jobs, which processes a massive volume of data. It is not suitable for frontend business systems that must process several thousand or tens of thousands of transactions per second.
-   For a detailed example of SQL operations, see [SQL](../../../../intl.en-US/User Guide/SQL/SQL Summary.md#).

## DDL Statement {#section_kdh_cvy_5db .section}

Simple DDL operations include creating tables, adding partitions, viewing tables and partition information, modifying tables, delete tables and partitions.

**Select Statements**

-   The key of GROUP BY statement can be the column name of input table, and the expression consisted of input table columns, but it cannot be the output column of SELECT statements.

    ```
    select substr(col2, 2) from tbl group by substr(col2, 2); -- Yes, the key of ‘group by’ can be the expression consisted of input table column;
     select col2 from tbl group by substr(col2, 2); -- No, the key of ‘group by’ is not in the column of Select statement;
     select substr(col2, 2) as c from tbl group by c; -- No, the key of ‘group by’ cannot be the column alias, i.e., the output column of Select statement;
    ```

    For SQL parsing, GROUP BY operations are conducted before SELECT operations, which means GROUP BY can only use the column or expression of the input table as the key.

-   ORDER BY must be used in combination with LIMIT.
-   DISTRIBUTE BY must be added in front of SORT BY.
-   The key of ORDER BY/SORT BY/DISTRIBUTE BY must be the output column of SELECT statement, that is, the column alias. An example is shown as follows:

    ```
    select col2 as c from tbl order by col2 limit 100 -- No, the key of ‘order by’ is not the output column (column alias) of Select statement.
      select col2 from tbl order by col2 limit 100; -- Yes, use column name as the alases if the output column of Select statement has no alias.
    ```

    For SQL parsing, ORDER BY/SORT BY/DISTRIBUTE BY by operations are conducted after SELECT operations. Therefore, they can only use the output column of SELECT statements as the key.


## Insert Statement { .section}

-   To insert data into a specified partition, the partition column is not allowed in the SELECT list:

    ```
    insert overwrite table sale_detail_insert partition (sale_date='2013', region='china')  
        select shop_name, customer_id, total_price, sale_date, region from sale_detail;
      --  Return error; sale_date and region are partition columns, which are not allowed in Select statement in static partition.
    ```

-   To insert a dynamic partition, the dynamic partition column must be in the SELECT list:

    ```
    insert overwrite table sale_detail_dypart partition (sale_date='2013', region) 
    select shop_name,customer_id,total_price from sale_detail; 
    --Failed, to insert the dynamic partition, the dynamic partition column must be in Select list.
    ```


## Join { .section}

-   MaxCompute SQL supports the following JOIN operation types: \{LEFT OUTER|RIGHT OUTER|FULL OUTER|INNER\} JOIN.
-   MaxCompute SQL supports up to 16 concurrent JOIN operations.
-   MaxCompute supports the map JOIN up to 8 small tables.

## Union All {#section_rgd_vvy_5db .section}

Union All can combine the results returned from multiple Select operations into a data set. It returns all the results without deduplication. MaxCompute does not support union two main query results, but you can do it on two subquery results.

**Note:** 

-   The two Select queries connected by Union All, must have the same number of columns, column names, and column types.
-   If the original names are inconsistent, you can set the same name by the alias.

## Additional information {#section_nhv_xvy_5db .section}

-   MaxCompute SQL supports up to 128 concurrent union operations;
-   MaxCompute supports up to 128 concurrent insert overwrite/into operations.

For more restrictions on MaxCompute SQL, see SQL Restrictions Summary.

## SQL optimization example {#section_osj_bwy_5db .section}

-   **Where condition in Join statement**

    When you join two tables, the Where condition of the primary table can be written at the end of the statement, but the restriction condition of the partition in the secondary table cannot be written in the Where condition. We recommend that you write it in the ON condition or subquery. The partition restrictions of the primary table can be written in the Where condition \(it is better to filter by subquery first\).  Several SQL examples are as follows:

    ```
    select * from A join (select * from B where dt=20150301)B on B.id=A.id where A.dt=20150301； 
    select * from A join B on B.id=A.id where B.dt=20150301； --Not allowed. 
    select * from (select * from A where dt=20150301)A join (select * from B where dt=20150301)B on B.id=A.id；
    ```

    The Join operation in the second statement runs first, data volume becomes larger and the performance can be decreased. Therefore, the second statement must be avoided.

-   **Data skew**

    The root cause of data skew is that the amount of data processed by some Workers is much larger than that of other Workers. This means running hours of some Workers are higher than the average, which leads to job delay.

    .

    -   **Data skew caused by Join**

        Data can be skewed by a Join operation when the Join key distribution is uneven. For the preceding example, to join a large table A and a small table B, run the following statement: For the preceding example, to join a large table A and a small table B, run the following statement:

        ```
        select * from A join B on A.value= B.value;
        ```

        Copy the logview link to enter the web console page, and double-click the Fuxi job that runs the Join operation. You can see a long tail in the Long-Tails tab, which indicates that the data has been skewed, as shown in the following figure: 

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/11952/1560_en-US.PNG)

        You can optimize the statement by the following methods:

        -   Since table B is a small table and does not exceed 512 MB, you can optimize the preceding statement into mapjoin statement.

            ```
            select /*+ MAPJOIN(B) */ * from A join B on A.value= B.value;
            ```

        -   Handle the skewed key with a separate logic. For example, a large number of null key values in both tables will usually cause data skew.  It is necessary to filter out the null data or add a random number before the Join operation, for example:

            ```
            select * from A join B
            on case when A.value is null then concat('value',rand() ) else A.value end = B.value;
            ```

    If you know that the data is skewed, but you cannot work out what is causing it, a general solution can be used to test the data skew. See the following example:

    ```
    
    select * from a join b on a.key=b.key; --This Leads to data skew. 
    Now you can run the following statements:  
    ```sql
    select left.key, left.cnt * right.cnt from 
    (select key, count(*) as cnt from a group by key) left 
    join
    (select key, count(*) as cnt from b group by key) right
    on left.key=right.key;
    ```

    Check the distribution of keys to discover whether data skew happens when A joins B.

-   **Group by skew**

    Group by skewing can be caused when the key distribution of group by is uneven.

    Suppose a table A has two fields: key and value. The data volume in the table is large enough, and the value distribution of key is uneven. Run the following statement:

    ```
    select key,count(value) from A group by key;
    ```

    You can see the long tail on the web console page. To solve this problem, you must set the anti-skew parameters before running SQL statement `set odps.sql.groupby.skewindata=true` must be added into the SQL statement.

-   **Data skew caused by incorrect use of dynamic partitions**

    Dynamic partitions of SQL in MaxCompute add a Reduce function by default, which is used to merge the same partition data. The benefits are as following.

    -   Reduce small files generated by MaxCompute and improve the efficiency of processing.
    -   Reduce the memory occupied when a Worker outputs many files.
    When partition data is skewed, using the Reduce function lead to the appearance of long tails. The same data can only be processed by a maximum of 10 Workers, so large volume of data results in long tails, for example:

    ```
    
    insert overwrite table A2 partition(dt) 
    select
    split_part(value,'\t',1) as field1,
    split_part(value,'\t',2) as field2, 
    dt 
    from A 
    where dt='20151010';
    ```

    In this case, we recommend that you do not use dynamic partition, and modify the statement in the following way:

    ```
    
    insert overwrite table A2 partition(dt='20151010') 
    select
    split_part(value,'\t',1) as field1,
    split_part(value,'\t',2) as field2
    from A 
    where dt='20151010';
    ```

-   **Window function optimization**

    If you use window functions in your SQL statement, each window function typically forms a Reduce job. If window functions are too many, they consume resources. In some specific scenarios, you can optimize window functions.

    -   The content after the over keyword must be the same, with the similar grouping and sorting conditions.
    -   Multiple window functions must run on the same SQL layer.
    Window functions that meet these two conditions merge into Reduce implementation. An SQL example is as follows:

    ```
    
    select
    rank()over(partition by A order by B desc) as rank,
    row_number()over(partition by A order by B desc) as row_num
    from MyTable;
    ```

-   **Convert the subquery to Join**

    A subquery is shown as follows:

    ```
    SELECT * FROM table_a a WHERE a.col1 IN (SELECT col1 FROM table_b b WHERE xxx);
    ```

    If the number of col1 returned by the table\_b subquery in this statement exceeds 1,000, the system reports an error: `rrecords returned from subquery exceeded limit of 1,000`. In this case, you can use the Join statement instead:

    ```
    SELECT a. * FROM table_a a JOIN (SELECT DISTINCT col1 FROM table_b b WHERE xxx) c ON (a.col1 = c.col1)
    ```

    **Note:** 

    -   If there is no Distinct keyword in the statement, and the result of the subquery c returns the same col1 value, it may cause the larger number of results of table\_a.
    -   The Distinct subquery can lead the whole query to fall into one Worker. If the subquery data is large, it may cause the whole query to be slower.If you have already made sure the col1 values are distinct in the subquery from the business, for example, by querying the primary key field, then performance can only be improved by removing the Distinct keyword.
    -   If you have already made sure the col1 values are distinct in the subquery from the business, for example, querying by the primary key field, to improve performance the Distinct keyword can only be removed.

