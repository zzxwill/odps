# Common table expression \(CTE\) {#concept_lh3_xkb_wdb .concept}

MaxCompute supports CTEs in standard SQL to improve the readability and execution efficiency of SQL statements.

Syntax structure of CTE.

```
WITH 
     cte_name AS
    
        cte_query
    
    [,cte_name2 AS 
     
     cte_query2
     
    ,……]
```

-   cte\_name refers to the CTE name, which must be unique in current WITH clause.  The cte\_name identifier in any position of the query indicates the CTE.
-   cte\_query is a SELECT statement, whose result set is used to populate the CTE.

Example:

```
INSERT OVERWRITE TABLE srcp PARTITION (p='abc')
SELECT * FROM (
    SELECT a.key, b.value
    FROM (
        SELECT * FROM src WHERE key IS NOT NULL ) a
    JOIN (
        SELECT * FROM src2 WHERE value > 0 ) b
    ON a.key = b.key
) c
UNION ALL
SELECT * FROM (
    SELECT a.key, b.value
    FROM (
        SELECT * FROM src WHERE key IS NOT NULL ) a
    LEFT OUTER JOIN (
        SELECT * FROM src3 WHERE value > 0 ) b
    ON a.key = b.key AND b.key IS NOT NULL
)d;
```

A JOIN clause is written on both sides of UNION at the top layer, and same queries are formed on the left table of JOIN.  You must repeat this code if writing subqueries.

The preceding statement can be rewritten as follows using the CTE:

```
with 
  a as (select * from src where key is not null),
  b as (select * from src2 where value>0),
  c as (select * from src3 where value>0),
  d as (select a.key,b.value from a join b on a.key=b.key ),
  e as (select a.key,c.value from a left outer join c on a.key=c.key and c.key is not null )
insert overwrite table srcp partition (p='abc')
select * from d union all select * from e;
```

After rewriting, the subquery corresponding to "a" only need to be rewritten once, and then can be reused subsequently.  The WITH clause in the CTE specifies multiple subqueries that can be repeatedly used like variables in the entire statement.  Besides being reused, subqueries do not have to be repeatedly nested.

