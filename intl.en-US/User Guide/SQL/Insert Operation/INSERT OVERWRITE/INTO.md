# INSERT OVERWRITE/INTO {#concept_yzd_ndb_wdb .concept}

**Function definition:**

```
INSERT OVERWRITE|INTO TABLE tablename [PARTITION (partcol1=val1, partcol2=val2 ...)] [(col1,col2 ...)]
select_statement
FROM from_statement;
```

**Note:** 

-   Insert syntax of MaxCompute is different from MySQL or Oracle Insert syntax. The keyword table must be added following insert overwrite|into, instead of using tablename directly.
-   When the target table for Insert is a partitioned table, expressions such as functions are not allowed in `[PARTITION (partcol1=val1, partcol2=val2 …)]` .
-   Currently, INSERT OVERWRITE does not support inserting columns. You can use INSERT INTO instead.

Insert overwrite/into saves calculation results into a destination table.

The difference between insert into and insert overwrite is that insert into inserts added data into the table or partition, while insert overwrite clears source data from the table or partition before inserting the data in it.

While processing data through MaxCompute SQL, insert overwrite/into is the most common statement. It can save the calculation result into a table, needed for the subsequent calculation.  For example, use the following statements to calculate the sale detail of different regions from the table sale\_detail:

```
create table sale_detail_insert like sale_detail;
alter table sale_detail_insert add partition(sale_date='2013', region='china');
insert overwrite table sale_detail_insert partition (sale_date='2013', region='china')
select shop_name, customer_id, total_price from sale_detail;
```

**Note:** The correspondence between source table and destination table depends on the column sequence in select clause, not the column name correspondence between the two tables. The following statement is still valid:

```
insert overwrite table sale_detail_insert partition (sale_date='2013', region='china')
select customer_id, shop_name, total_price from sale_detail;
    -- When the sale_detail_insert table is created, the column sequence is as below:
    -- shop_name string, customer_id string, total_price bigint
    -- When data is inserted from sale_detail to sale_detail_insert, the insertion sequence of sale_detail is as below:
    -- customer_id, shop_name, total_price
    -- Inserts data in sale_detail.customer_id into sale_detail_insert.shop_name.
    -- Inserts data in sale_detail.shop_name into sale_detail_insert.customer_id.
```

To insert data into a partition, the partition column cannot appear in the Select list.

```
insert overwrite table sale_detail_insert partition (sale_date='2013', region='china')
        select shop_name, customer_id, total_price, sale_date, region from sale_detail;
    -- Returns an error. The items sale_date and region are partition columns, which cannot appear in the INSERT statement of static partitions.
```

Simultaneously, the value of the partition can only be a constant and expressions cannot appear. The following statements are invalid:

```
insert overwrite table sale_detail_insert partition (sale_date=datepart('2016-09-18 01:10:00', 'yyyy') , region='china')
        select shop_name, customer_id, total_price from sale_detail;
```

