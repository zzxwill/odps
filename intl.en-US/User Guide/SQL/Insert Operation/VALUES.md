# VALUES {#concept_uhn_rdb_wdb .concept}

In the test phase, prepare some basic data for a small data table. You can quickly write some test data to the test table by using the  INSERT … VALUES statement.

**Note:** 

Currently, INSERT OVERWRITE does not support insert columns, use INSERT INTO instead.

**Statement format**:

```
INSERT INTO TABLE tablename 
[PARTITION (partcol1=val1, partcol2=val2 ...)][co1name1,colname2...] 
[VALUES (col1_value,col2_value,...),(col1_value,col2_value,...),...]
```

**Example 1:**:

```
drop table if exists srcp;
create table if not exists srcp (key string ,value bigint) partitioned by (p string);
insert into table srcp partition (p='abc') values ('a',1),('b',2),('c',3);
```

After the preceding statements run successfully, the result of partition ‘abc’ is as follows:

```

| key | value | p |

| a | 1 | abc |
| b | 2 | abc |
| c | 3 | abc |

```

When many columns are in the table, and you want to insert data into some of the columns , use the insert list function as follows.

**Example 2:**

```
drop table if exists srcp;
create table if not exists srcp (key string ,value bigint) partitioned by (p string);
insert into table srcp partition (p)(key,p) values ('d','20170101'),('e','20170101'),('f','20170101');
```

After the preceding statements run successfully, the result of partition ‘20170101’ is as follows:

```

| key | value | p |

| d | NULL | 20170101 |
| e | NULL | 20170101 |
| f | NULL | 20170101 |

```

For columns not specified in values, the default value is NULL.  The insert list function is not necessarily used with values, and can also be used with ‘Insert into…select…’.

The Insert…values method has a limitation: values must be constants. You can use the values table function of MaxCompute to perform some simple operations on the inserted data. For more information, see Example 3.

**Example 3:**

```
drop table if exists srcp;
create table if not exists srcp (key string ,value bigint) partitioned by (p string);
insert into table srcp partition (p) select concat(a,b), length(a)+length(b),'20170102' from values ('d',4),('e',5),('f',6) t(a,b);
```

The values \(…\), \(…\) t \(a, b\) are to define a table named t whose columns are a and b, data type is \(a string, b  bigint\), the data type of which is derived from the values list.  In this way, with no physical table prepared, it is possible to simulate a multi-row table with arbitrary data and perform arbitrary calculations.

After the preceding statements run successfully, the result of partition ‘20170102’ is as follows:

```

| key | value | p |

| d4 | 2 | 20170102 |
| e5 | 2 | 20170102 |
| f6 | 2 | 20170102 |

```

**Note:** 

-   Values only support constants and do not support functions including ARRAY complex types. Currently, MaxCompute cannot construct corresponding constants. Modify the statement as follows:

    ```
    insert into table srcp (p ='abc') select 'a',array('1', '2', 
            '3');.
    ```

    which can provide the same effect.

-   To write datetime or timestamp type through values, specify the type name in values statement, for example:

    ```
    insert into table srcp (p ='abc') values (datetime'2017-11-11 
                00:00:00',timestamp'2017-11-11 00:00:00.123456789');
    ```


In fact, the values is not only used in the Insert statement, any DML statement can also be used.

A special usage of values is as follows.

```
select abs(-1), length('abc'), getdate();
```

As the preceding statement shows, select can be run without the from statement, if the expression list of select does not use any upstream table data. The underlying implementation is selecting from an anonymous values table in one row and zero columns. In this way, to test some functions, such as your UDF, etc., you do not need to manually create DUAL tables.

