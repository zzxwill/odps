# Subquery {#concept_fbc_4kb_wdb .concept}

## Basic definition of a subquery {#section_ddy_yqb_wdb .section}

A normal SELECT operation reads data from several tables, for example, select `column_1, column_2 … from table_name`.  However, the query object can be another SELECT operation, which is shown as follows:

```
select * from (select shop_name from sale_detail) a;
```

**Note:** The subquery must have an alias.

In a FROM clause, a subquery can be used as a table to perform JOIN operations with other tables or subqueries, which is shown as follows:

```
create table shop as select * from sale_detail;
select a.shop_name, a.customer_id, a.total_price from
(select * from shop) a join sale_detail on a.shop_name = sale_detail.shop_name;
```

## IN SUBQUERY / NOT IN SUBQUERY {#section_vgb_2rb_wdb .section}

IN SUBQUERY is similar to LEFT SEMI JOIN.

**For example**:

```
SELECT * from mytable1 where id in (select id from mytable2);
-- is equivalent to
SELECT * from mytable1 a LEFT SEMI JOIN mytable2 b on a.id=b.id;
```

Currently, MaxCompute supports both IN SUBQUERY and CORRELATED conditions. 

**For example**:

```
SELECT * from mytable1 where id in (select id from mytable2 where value = mytable1.value);
```

`where value = mytable1.value` in the subquery is a CORRELATED condition. MaxCompute of early versions reports errors for such expressions that reference source tables both in subqueries and in outer queries.  **MaxCompute supports such expressions now**. In fact, such filtering conditions are a part of the ON condition in SEMI JOIN.

NOT IN SUBQUERY is similar to LEFT ANTI JOIN. However, they have one significant difference. 

**For example**:

```
SELECT * from mytable1 where id not in (select id from mytable2);
-- If none of the IDs in mytable2 are NULL, this statement is equivalent to
SELECT * from mytable1 a LEFT ANTI JOIN mytable2 b on a.id=b.id;
```

If mytable2 contains any column whose ID is NULL, the NOT IN expression is NULL, so that the WHERE condition is invalid and no data is returned. This is different from LEFT ANTI JOIN.

MaxCompute 1.0 supports \[NOT\] IN  SUBQUERY not serving as a JOIN condition, for example, in a non-WHERE statement, or failure in conversion to a JOIN condition even in a WHERE statement.  MaxCompute 2.0 still  supports this feature. However, \[NOT\] IN SUBQUERY cannot be converted to SEMI JOIN, and a separate job must be started to run subqueries. Therefore, \[NOT\] IN SUBQUERY does not support CORRELATED conditions.

**For example**:

```
SELECT * from mytable1 where id in (select id from mytable2) OR value > 0;
```

As the WHERE clause includes OR, \[NOT\] IN SUBQUERY cannot be converted to SEMI JOIN. A separate job must be started to run subqueries.

In addition, partition tables are specially processed:

```
SELECT * from sales_detail where ds in (select dt from sales_date);
```

If ds is a partition column, `select dt from sales_date` separately  starts a job to run subqueries, instead of converting to SEMI JOIN. After running, the results are compared with ds one by one. If a ds value in sales\_detail is not in the returned results, the partition is not read to make sure that partition pruning is still valid.

## EXISTS SUBQUERY/NOT EXISTS SUBQUERY {#section_hq5_q5b_wdb .section}

In an EXISTS SUBQUERY, when at least one data row exists in the subquery, TRUE is returned; otherwise, FALSE is returned. NOT EXISTS subquery is completely opposite of this.

Currently, MaxCompute supports only subqueries including the correlated WHERE conditions.  EXISTS SUBQUERY/NOT EXISTS SUBQUERY is implemented by converting to LEFT SEMI  JOIN or LEFT ANTI JOIN.

**For example**:

```
SELECT * from mytable1 where exists (select * from mytable2 where id = mytable1.id);
-- is equivalent to
Select * From mytable1 a left semi join mytable2 B on A. ID = B. ID;
```

While

```
SELECT * from mytable1 where not exists (select * from mytable2 where id = mytable1.id);
-- is equivalent to
SELECT * from mytable1 a LEFT ANTI JOIN mytable2 b on a.id=b.id;
```

