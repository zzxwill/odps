# MULTI INSERT {#concept_jkh_pdb_wdb .concept}

MaxCompute SQL supports inserting different result tables or partitions in a single SQL statement.

**Statement format**:

```
FROM from_statement
     INSERT OVERWRITE | INTO TABLE tablename1 [PARTITION (partcol1=val1, partcol2=val2 ...)]
     select_statement1 [FROM from_statement]
      [INSERT OVERWRITE | INTO TABLE tablename2 [PARTITION (partcol1=val3, partcol2=val4 ...)]
        select_statement2 [FROM from_statement]]
```

**Note:** 

-   Generally, up to 256 ways of output can be written in a single SQL statement. Once exceeding 256 ways of output, syntax error occurs.
-   In a multi insert statement:
    -   For a partitioned table, a target partition cannot appear for multiple times.
    -   For an unpartitioned table, this table cannot appear for multiple times.
-   Different partitions within a partitioned table cannot have an Insert overwrite operation and an Insert into operation at the same time; otherwise, an error is returned.

For an unpartitioned table, this table cannot appear for multiple times.

```
create table sale_detail_multi like sale_detail;
    from sale_detail
        insert overwrite table sale_detail_multi partition (sale_date='2010', region='china' ) 
            select shop_name, customer_id, total_price where .....
        insert overwrite table sale_detail_multi partition (sale_date='2011', region='china' )
            select shop_name, customer_id, total_price where .....
    -- Return result successfully. Insert the data of sale_detail into the 2010 sales records and 2011 sales records in China region.
    from sale_detail
        insert overwrite table sale_detail_multi partition (sale_date='2010', region='china' )
            select shop_name, customer_id, total_price
        insert overwrite table sale_detail_multi partition (sale_date='2010', region='china' )
            select shop_name, customer_id, total_price;
    -- An error is thrown. The same partition appears for multiple times.
    from sale_detail
        insert overwrite table sale_detail_multi partition (sale_date='2010', region='china' )
            select shop_name, customer_id, total_price
        insert into table sale_detail_multi partition (sale_date='2011', region='china' )
            select shop_name, customer_id, total_price;
    -- An error is thrown. Different partitions within a partition table cannot have both an ‘insert overwrite’ operation and an ‘insert into’ operation.
```

