# Join on condition in MaxCompute\(ODPS\) SQL {#concept_vms_4sv_1fb .concept}

One of the most common operations in MaxCompute\(ODPS\) SQL is join.

## Overview {#section_ugb_c1w_1fb .section}

Currently MaxCompute offers several join types:

|Type|Meaning|
|:---|:------|
|Inner join|Output data that matches the criteria of the Association|
|Left join|Outputs all records for the left table, and for the right table that matches the associated data, outputs the right table, there is no match, and the right table supplements null.|
|Right join|Outputs all records of the right table, for which the left table matches the associated data, for which the left table is output, no match, left table to fill in null.|
|Full join|Outputs all records for the left and right tables, for data that is not associated with, a null is added on the other side that is not associated.|
|Left Semi Join|For a single piece of data in the left table, if the right table has rows that match the criteria of the Association, the left table is output.|
|Left Anti Join|For a single piece of data in the left table, if for all rows in the right table, there is no data that matches the criteria of the Association, and the left table is output.|

**Note:** [User Defined Join](https://www.atatech.org/articles/100074) specifies both input streams, and the user himself implements the logic of the join, which is not discussed here.

Depending on the scenario, the user can use different Join types to implement the corresponding Association operation. But in the actual use process, it is often not clear to users that the filtering criteria are different in join on statements or in where, or think they're doing the same thing, for example, in a production environment, users can often be seen writing:

```
A (LEFT/RIGHT/FULL/LEFT SEMI/LEFT ANTI) JOIN B
ON a.key = b.key and A.ds='20180101' and B.ds='20180101';
```

The intention of the user here is to get the data for a partition in A and B for the join operation., that is:

```
(Select * from a where DS = '20180101 ')
(LEFT/RIGHT/FULL/LEFT SEMI/LEFT ANTI)  JOIN
(SELECT * FROM B WHERE ds='20180101') B
ON a.key = b.key
```

However, for different Join types, the two may not be equivalent, not only can not push the partition conditions, results In a full table scan, and it can cause correctness problems. Here is a brief analysis of the filter conditions in:

1.  Where condition of subquery
2.  JOIN ON condition
3.  Where condition after JOIN ON

The similarities and differences.

## Principle {#section_jqg_f1w_1fb .section}

Let's start with the computed order of a join and a where condition,:

```
(SELECT * FROM A WHERE {subquery_where_condition} A) A
JOIN
(SELECT * FROM B WHERE {subquery_where_condition} B) B
ON {on_condition}
WHERE {where_condition}
```

For example, the order of calculation is

1.  Subquery`{subquery_where_condition}`.
2.  The condition of the `{on_condition}` For the join.
3.  The calculation of the join result collection, `{where_condition}`.

For different Join types, filter statements are placed in `{subquery_where_condition}`,`{on_condition}`, and `{where_condition}`, sometimes the results are consistent, and sometimes the results are inconsistent. The following discussion takes place:

## Experiment {#section_lvl_j1w_1fb .section}

1.  Prepare

    First construct table:

    ```
    CREATE TABLE A AS SELECT * FROM VALUES (1, 20180101),(2, 20180101),(2, 20180102) t (key, ds);
    ```

    |key|ds|
    |:--|:-|
    |1|20180101|
    |2|20180101|
    |2|20180102|

    Table B:

    ```
    CREATE TABLE B AS SELECT * FROM VALUES (1, 20180101),(3, 20180101),(2, 20180102) t (key, ds);
    ```

    |key|ds|
    |:--|:-|
    |1|20180101|
    |3|20180101|
    |2|20180102|

    Then their product of Descartes is:

    |a.key|a.ds|b.key|b.ds|
    |:----|:---|:----|:---|
    |1|20180101|1|20180101|
    |1|20180101|3|20180101|
    |1|20180101|2|20180102|
    |2|20180101|1|20180101|
    |2|20180101|3|20180101|
    |2|20180101|2|20180102|
    |2|20180102|1|20180101|
    |2|20180102|3|20180101|
    |2|20180102|2|20180102|

2.  Inner Join

    Conclusion: the filter conditions are equivalent in`{subquery_where_condition}`, `{on_condition}`, and `{where_condition}`.

    The processing logic of inner join is to product the left and right tables to the Descartes, then select the traveling line output that meets the on expression.

    1.  In the first case, the subquery is filtered:

        ```
        Select a. *, B .*
        From
        (Select * from a where DS = '20180101 ')
        JOIN
        (SELECT * FROM B WHERE ds='20180101') B
        ON a.key = b.key;
        ```

        It's very simple, and there's only one result:

        |a.key|a.ds|b.key|b.ds|
        |:----|:---|:----|:---|
        |1|20180101|1|20180101|

    2.  In the second case, the JOIN condition is filtered:

        ```
        SELECT A.*, B.*
        FROM A JOIN B
        ON a.key = b.key and A.ds='20180101' and B.ds='20180101';
        ```

        There are nine results of Descartes, and there is only one result to satisfy the condition of on.

        |a.key|a.ds|b.key|b.ds|
        |1|20180101|1|20180101|

    3.  In the third case, the where condition filter after JOIN:

        ```
        Select a. *, B .*
        FROM A JOIN B
        ON a.key = b.key
        WHERE A.ds='20180101' and B.ds='20180101';
        ```

        For example, there are nine results of Descartes that meet the on `a.key = b.key` there are 3 results for key, respectively:

        |a.key|a.ds|b.key|b.ds|
        |:----|:---|:----|:---|
        |1|20180101|1|20180101|
        |2|20180102|2|20180102|
        |2|20180101|2|20180102|

        filter this result again`A.ds='20180101' and B.ds='20180101'`, results in only 1 Article.

        |a.key|a.ds|b.key|b.ds|
        |-----|----|-----|----|
        |1|20180101|1|20180101|

        As you can see, three different results have been obtained by placing the filter conditions in three different places.

3.  Left join

    Conclusion: The filtering conditions are not necessarily equivalent in `{subquery_where_condition}`, `{on_condition}`, and `{where_condition}`.

    For the filter criteria for the left table, the ones placed in `{subquery_where_condition}` and `{where_condition}` are equivalent.

    For the filter criteria for the right table, the ones placed in `{subquery_where_condition}` and `{on_condition}` are equivalent.

    The processing logic of left join is to make the left and right tables a Descartes product, then for the moving line output that satisfies the on expression, for the rows in the left table that do not meet the on expression, the left table is output, and the right table supplements null.

    1.  In the first case, the subquery is filtered:

        ```
        SELECT A.*, B.*
        From
        (SELECT * FROM A WHERE ds='20180101') A
        LEFT JOIN
        (SELECT * FROM B WHERE ds='20180101') B
        ON a.key = b.key;
        ```

        After filtering, there are two on the left and one on the right and two on the results:

        |a.key|a.ds|b.key|b.ds|
        |:----|:---|:----|:---|
        |1|20180101|1|20180101|
        |2|20180101|NULL|NULL|

    2.  In the second case, the JOIN condition is filtered:

        ```
        SELECT A.*, B.*
        FROM A JOIN B
        ON a.key = b.key and A.ds='20180101' and B.ds='20180101';
        ```

        There are nine results of Descartes, and only one result to satisfy the condition of on, the left table is null for the remaining two outputs of the Left table.

        |a.key|a.ds|b.key|b.ds|
        |:----|:---|:----|:---|
        |1|20180101|1|20180101|
        |2|20180101|NULL|NULL|
        |2|20180102|NULL|NULL|

    3.  In the third case, the where condition filter after JOIN:

        ```
        SELECT A.*, B.*
        FROM A JOIN B
        ON a.key = b.key
        WHERE A.ds='20180101' and B.ds='20180101';
        ```

        For example, there are nine results of Descartes that meet the ON `a.key = b.key` there are 3 results for key, respectively:

        |a.key|a.ds|b.key|b.ds|
        |:----|:---|:----|:---|
        |1|20180101|1|20180101|
        |2|20180101|2|20180102|
        |2|20180102|2|20180102|

        filter this result again`A.ds='20180101' and B.ds='20180101'`, results in only 1 Article.

        |a.key|a.ds|b.key|b.ds|
        |:----|:---|:----|:---|
        |1|20180101|1|20180101|

        As you can see, three different results have been obtained by placing the filter conditions in three different places.

4.  Right join

    The right join and left join are similar, just the difference between the left and right tables. Conclusion: The filtering conditions are not necessarily equivalent in `{subquery_where_condition}`, `{on_condition}`, and `{where_condition}`. For the filter criteria for the right table, the ones placed in `{subquery_where_condition}` and `{where_condition}` are equivalent. For the filter criteria for the left table, the ones placed in `{subquery_where_condition}` and `{on_condition}`are equivalent.

5.  Full join

    Conclusion: The filter conditions are written in `{subquery_where_condition}`, `{on_condition}`, and `{where_condition}` are not equivalent.

    The processing logic of full join is to make the left and right tables a Descartes product, then for the moving line output that satisfies the on expression, for the rows that do not meet the on expression in the tables on both sides, outputs a table with data, with null on the other side.

    1.  In the first case, the subquery is filtered:

        ```
        SELECT A.*, B.*
        From
        (SELECT * FROM A WHERE ds='20180101') A
        FULL JOIN
        (SELECT * FROM B WHERE ds='20180101') B
        ON a.key = b.key;
        ```

        After filtering, there are two on the left and two on the right, and three on the right:

        |a.key|a.ds|b.key|b.ds|
        |:----|:---|:----|:---|
        |1|20180101|1|20180101|
        |2|20180101|NULL|NULL|
        |NULL|NULL|3|20180101|

    2.  In the second case, the JOIN condition is filtered:

        ```
        SELECT A.*, B.*
        FROM A FULL JOIN B':
        ON a.key = b.key and A.ds='20180101' and B.ds='20180101';
        ```

        There are nine results of Descartes, and only one result to satisfy the condition of on, the left table is null for the remaining two outputs of the Left table. The remaining two outputs of the right table, the right table, and the left table, fill in null.

        |a.key|a.ds|b.key|b.ds|
        |:----|:---|:----|:---|
        |1|20180101|1|20180101|
        |2|20180101|NULL|NULL|
        |2|20180102|NULL|NULL|
        |NULL|NULL|3|20180101|
        |NULL|NULL|2|20180102|

    3.  In the third case, the where condition filter after JOIN:

        ```
        SELECT A.*, B.*
        FROM A FULL JOIN B:
        ON a.key = b.key
        WHERE A.ds='20180101' and B.ds='20180101';
        ```

        For example, there are nine results of Descartes that meet the on `a.key = b.key` there are 3 results for key, respectively:

        |a.key|a.ds|b.key|b.ds|
        |:----|:---|:----|:---|
        |1|20180101|1|20180101|
        |2|20180101|2|20180102|
        |2|20180102|2|20180102|

        Then the data on the other side of the JOIN is output, and NULL is added to the other side and the result is:

        |a.key|a.ds|b.key|b.ds|
        |:----|:---|:----|:---|
        |1|20180101|1|20180101|
        |2|20180101|2|20180102|
        |2|20180102|2|20180102|
        |NULL|NULL|3|20180101|

        filter this result again`A.ds='20180101' and B.ds='20180101'`, results in only 1 Article.

        |a.key|a.ds|b.key|b.ds|
        |:----|:---|:----|:---|
        |1|20180101|1|20180101|

        As you can see, like LEFT JOIN, there are three different results.

6.  Left Semi Join

    Conclusion: The filter conditions are written in `{subquery_where_condition}`, `{on_condition}`, and `{where_condition}` are not equivalent.

    The processing logic for LEFT SEMI Join is for each record in the left table, all go to the right table to match, and if the match succeeds, the left table is output. What you need to note here is that only the left table is output, therefore, the filter condition to the right cannot be written in the where condition after the JOIN. Left semi join is commonly used to implement the semantics of exists:

    1.  In the first case, the subquery is filtered:

        ```
        SELECT A.*
        FROM
        (SELECT * FROM A WHERE ds='20180101') A
        LEFT SEMI JOIN
        (SELECT * FROM B WHERE ds='20180101') B
        ON a.key = b.key;
        ```

        After filtering, there are two on the left and the right, which eventually fit`a.key = b.key` there is only one of the key:

        |a.key|a.ds|
        |:----|:---|
        |1|20180101|

    2.  In the second case, the JOIN condition is filtered:

        ```
        SELECT A.*
        FROM A LEFT SEMI JOIN B
        ON a.key = b.key and A.ds='20180101' and B.ds='20180101';
        ```

        For the three records on the left, there is also only one result that meets the on condition.

        |a.key|a.ds|
        |:----|:---|
        |1|20180101|

    3.  In the third case, the where condition filter after JOIN:

        ```
        Select .*
        FROM A LEFT SEMI JOIN
        (SELECT * FROM B WHERE ds='20180101') B
        ON a.key = b.key
        WHERE A.ds='20180101';
        ```

        There is one on that can meet the on condition on the left:

        |a.key|a.ds|
        |:----|:---|
        |1|20180101|

        A. filter this result again`A.ds='20180101'`, results remain 1 Article:

        |a.key|a.ds|
        |-----|----|
        |1|20180101|

        As you can see, the left semi join and inner join are similar, no matter where the filter conditions are placed, the results were consistent.

7.  Left Anti Join

    Conclusion: The filter conditions are written in `{subquery_where_condition}`, `{on_condition}`, and `{where_condition}` are not equivalent.

    For the filter criteria for the left table, the ones placed in `{subquery_where_condition}` and `{where_condition}` are equivalent.

    For the filter criteria for the right table, the ones placed in `{subquery_where_condition}` and `{on_condition}` are equivalent, the right table expression cannot be placed in `{where_condition}`.

    The processing logic for left anti join is for each record in the left table, all go to match the right table, and if none of the records on the right table are matched successfully, the left table is output. Similarly, since only the left table is output, therefore, the filter condition to the right cannot be written in the where condition after the join. LEFT SEMI JOIN are often used to implement the semantics of not exists.

    1.  In the first case, the subquery is filtered:

        ```
        SELECT A.*
        From
        (SELECT * FROM A WHERE ds='20180101') A
        LEFT ANTI JOIN
        (SELECT * FROM B WHERE ds='20180101') B
        ON a.key = b.key;
        ```

        After filtering, there are two on the left, two on the right and one on the results.

        |a.key|a.ds|
        |:----|:---|
        |2|20180101|

    2.  In the second case, the JOIN condition is filtered:

        ```
        SELECT A.*
        FROM A LEFT ANTI JOIN B
        ON a.key = b.key and A.ds='20180101' and B.ds='20180101';
        ```

        For the three records on the left, only the first one has the result of satisfying the ON condition, so output the remaining two records.

        |a.key|a.ds|
        |:----|:---|
        |2|20180101|
        |2|20180102|

    3.  In the third case, the WHERE condition filter after JOIN:

        ```
        SELECT A.*
        FROM A LEFT ANTI JOIN
        (SELECT * FROM B WHERE ds='20180101') B
        ON a.key = b.key
        WHERE A.ds='20180101';
        ```

        There are two on conditions that can be passed ON the left:

        |a.key|a.ds|
        |:----|:---|
        |2|20180101|
        |2|20180102|

        The result is filtered again for a `A.ds='20180101'` and the result is 1.

        |a.key|a.ds|
        |:----|:---|
        |2|20180101|

        As you can see, in LEFT ANTI JOIN, the filter condition is placed in the JOIN ON condition and in the where condition before and after, and the result is different.

        The above is a simple test of several different writing methods for a common scenario, without a specific deduction process, it will be more complex for scenarios involving expressions that are not equivalent to each other, interested students can try to derive them themselves.


## Online status {#section_cwv_ctw_1fb .section}

These results are derived from SQL standard semantic patterns. Some users will find that the results of the same statements in the online environment do not match expectations, this is due to some historical reasons and compatibility considerations. In the implementation of Outer Join, a flag is set at the project level, called `odps.sql.outerjoin.supports.filters`, if set to false, indicates that the on condition of Outer Join does not support filtering conditions, writing `{on_condition}` will be treated as if it were written in `{subquery_where_condition}`, which is a non-standard behavior. Some users switch between two projects, it is caused by this that the same SQL runs differently in both projects.

It is hoped that everyone can write SQL according to the standard SQL semantics, in this way, you can ensure the portability of subsequent SQL.

View Project settings you can find the corresponding project and view the properties in the `project administration` in [http://adminconsole.odps.aliyun-inc.com/inn.view](http://adminconsole.odps.aliyun-inc.com/inn.view).

## Conclusion {#section_qxs_jtw_1fb .section}

The semantics of the filter conditions in different locations may vary greatly for the user, if you are simply filtering the data and then joining, you can simply remember the following points.

1.  The inner join/left semi join can be written on both sides of the expression.
2.  Left join/left anti join the filter criteria for the left table are to be put in `{subquery_where_condition}`or `{where_condition}`, the filter criteria for the right table are to be placed in either `{subquery_where_condition}` or `{on_condition}`.
3.  Right join is opposite to left join, the filter criteria for the right table are to be placed either `{subquery_where_condition}` or `{where_condition}`, the filter criteria for the left table are to be placed on `{subquery_where_condition}` or `{on_condition}`.
4.  Full outer join can only be placed in`{subquery_where_condition}`.

Of course, if you still think the rules are complicated, the best way to do this is to write the filter criteria to the subquery every time.

