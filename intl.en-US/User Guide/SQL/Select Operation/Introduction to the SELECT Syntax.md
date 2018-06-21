# Introduction to the SELECT Syntax {#concept_i1q_lkb_wdb .concept}

**The command format is as follows**:

```
SELECT [ALL | DISTINCT] select_expr, select_expr, ...
FROM table_reference
[WHERE where_condition]
[GROUP BY col_list]
[ORDER BY order_condition]
[DISTRIBUTE BY distribute_condition [SORT BY sort_condition] ]
[LIMIT number]
```

**Note the following when using a SELECT statement:**

-   When using SELECT to read data from a table, you can specify the names of the columns to be read, or use an asterisk \(\*\) to represent all columns. A simple SELECT statement is shown as follows:

    ```
    select * from sale_detail;
    ```

    If you want to read only the shop\_name column in sale\_detail, use the following statement:

    ```
    select shop_name from sale_detail;
    ```

    You can use where to specify filtering conditions. For example:

    ```
    select * from sale_detail where shop_name like 'hang%';
    ```

    When a Select statement is used, a maximum of 10,000 rows of results can be displayed. But if the Select statement serves as a clause, all the results are returned to the upper-level query.

-   Full table scan is prohibited when you select a partitioned table.

    For new projects created after January 10, 2018,  20:00 \(UTC+8\) full table scan is not allowed for the partitioned table in the project by default When SQL runs. Partitions to be scanned must be specified in partition conditions, reducing unnecessary SQL I/O, waste of computing resources, and the unnecessary cost. Note: Using the Pay-As-You-Go billing method, the amount of data input is one of the billing parameters.

    If the table definition is `t1(c1,c2) partitioned by(ds)`, running the following statement in a new project is forbidden and an error may occur:

    ```
    Select * from t1 where c1=1;
    Select * from t1 where (ds=‘20180202’ or c2=3);
    Select * from t1 left outer join t2 on a.id =b.id and a.ds=b.ds and b.ds=‘20180101);  
    --When Join statement is running, if the partition clipping condition is placed in where clause, the partition clipping takes effect. If you put it in on clause, the partition clipping of sub table takes effect, and the main table performs a full table scan.
    ```

    If you perform a full table scan on the partitioned table, you can add a set statement `set odps.sql.allow.fullscan=true;` before the SQL statement that scans the entire table of the partitioned table. The set statement must be submitted along with the SQL statement. Suppose that the sales\_detail table is a partitioned table. Submit the following simple query statements at the same time for a full table scan:

    ```
    set odps.sql.allow.fullscan=true;
    select * from sale_detail;
    ```

    If the entire project is required to allow full table scanning, the switch can be turned on or off by itself \(true/false\), and the command is as follows:

    ```
    setproject odps.sql.allow.fullscan=true;
    ```

-   table\_reference supports nested subqueries, for example:

    ```
    select * from (select region from sale_detail) t where region = 'shanghai';
    ```

-   The filter conditions supported by ‘where’ clause are shown as follows:

    |Filter conditions|Description|
    |:----------------|:----------|
    |\> 、 < 、 =、 \>=、 <=、 <\>|Relational operators|
    |like、rlike|The source and pattern parameters of like and rlike can only be of the String type.|
    |in、not in|If a subquery is attached to the in or not in condition, only the values of one column are returned for the subquery, and the returned values cannot exceed 1,000 entries.|

    You can specify a partition scope in the where clause of a Select statement to scan specified partitions of a table instead of the whole table. As follows:

    ```
    SELECT sale_detail. * FROM sale_detail WHERE sale_detail.sale_date >= '2008' AND sale_detail.sale_date <= '2014';
    ```

    The where clause of MaxCompute SQL supports query by the between…and condition. The preceding SQL statement can be rewritten as follows:

    ```
    SELECT sale_detail. * FROM sale_detail WHERE sale_detail.sale_date BETWEEN '2008' AND '2014';
    ```

-   distinct: If duplicated data rows exist, you can use the Distinct option before the field to remove duplicates. In this case, only one value is returned. If you use the ALL option, or do not specify this option, all duplicated values in the fields are returned.

    If you use the Distinct option, only one row of record is returned, which is shown as follows:

    ```
    select distinct region from sale_detail;
    select distinct region, sale_date from sale_detail;
    -- Performs the Distinct option on multiple columns. The Distinct option has an effect on Select column sets rather than a single column.
    ```

-   group by: Query by group. It is generally used together with an aggregate function. A Select statement that contains an aggregate function follows these rules:

    -   The key using group by can be the name of a column in the input table.
    -   Alternatively, it can be an expression consisting of columns of the input table. The key cannot be the alias of an output column of the Select statement.
    -   Rule i takes precedence over rule ii. If rules i and ii conflict, that is, if the key using group by is a column or expression of the input table and an output column of Select, rule i prevails.

    **For example:**

    ```
    select region from sale_detail group by region;
    -- Runs successfully with the name of a column in the input table directly used as the group by column
    select sum(total_price) from sale_detail group by region;
    -- Runs successfully with the table grouped by the region value and returns the total sales of each group
    Select region, sum (total_price) from sale_detail group by region;
    -- Runs successfully with the table grouped by the region value and returns the region value (unique in the group) and total sales of each group
    select region as r from sale_detail group by r;
     -- Runs with the alias of the Select column and returns an error
    select 2 + total_price as r from sale_detail group by 2 + total_price;
    -- Requires a complete expression of the column
    Select region, total_price from sale_detail group by region;
    -- Returns an error; all columns not using an aggregate function in the Select statement must exist in group by
    select region, total_price from sale_detail group by region, total_price;
    -- Runs successfully
    ```

    These restrictions are imposed because group by operations come before Select operations during SQL parsing. Therefore, group by statements can only accept the columns or expressions of the input table as keys.

    **Note:** For more information about aggregate functions, see [Aggregate Functions](intl.en-US/User Guide/SQL/Builtin Function/Aggregate function.md).

-   order by: Globally sorts all data based on certain columns. To sort records in descending order, you can use the DESC keyword. For global sorting, **order by must be used together with limit**. When order by is used for sorting, NULL is considered to be smaller than any other value. This action is the same as that in MySQL but different from that in Oracle.

    Unlike group by, order by must be followed by the alias of the Select column. If the Select operation is performed on a column and the column alias is not specified, the column name is used as the column alias.

    ```
    select * from sale_detail order by region;
    -- Returns an error because order by is not used together with limit
    select * from sale_detail order by region limit 100;
    select region as r from sale_detail order by region limit 100;
    -- Returns an error because ORDER BY is not followed by a column alias
    select region as r from sale_detail order by r limit 100;
    ```

    `The number in [limit number]` is a constant to limit the number of output rows. If you want to directly view the result of a Select statement without LIMIT from the screen output, you can view a maximum of 10,000 rows. The upper limit of screen display varies with projects, which can be controlled through the setproject console.

-   Distribute by: Performs hash-based sharding on data by values of certain columns. Aliases of Select output columns must be used.

    ```
    select region from sale_detail distribute by region;
    -- Runs successfully because the column name is an alias
    select region as r from sale_detail distribute by region;
    -- Returns an error because DISTRIBUTE BY is not followed by a column alias
    select region as r from sale_detail distribute by r;
    ```

-   Sort by: for partial ordering, ‘distribute by’ must be added in front of the statement. sort by is used to partially sort the results of distribute by. Aliases of Select output columns must be used.

    ```
    select region from sale_detail distribute by region sort by region;
    select region as r from sale_detail sort by region;
    -- Returns an error and exits because no distribute by exists.
    ```

-   order by or group by cannot be used together with distribute by/sort\] by. Aliases of SELECT output columns must be used.

**Note:** 

-   The keys of order by/sort by/distribute by must be output columns \(namely, column aliases\) of Select statements.
-   In MaxCompute SQL parsing, order by/sort by/distribute by come after Select operations. Therefore, they can only accept the output columns of Select statements as keys.

