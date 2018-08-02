# Aggregate function {#concept_q4j_zrd_5db .concept}

The relation between the input and the output of aggregate functions is a many-to-one relationship; that is, to aggregate multiple input records into an output record. It can be used with the group by clause in SQL.

## COUNT {#section_x1k_xq1_wdb .section}

**Function definition:**

```
bigint count([distict|all] value)
```

**Usage:**

This function is used to count the record numbers.

**Parameter description**:

-   distinct|all: specify whether to remove duplicate records in the count process. The default all is used for counting all records. If the field ‘distinct’ is specified, it indicates taking a unique count value.

-   value: any type. If the value is NULL, the corresponding row is not involved in the count. Count \(\*\), returns all rows.


**Return Value**:

Returns the Bigint type.

**For example**:

```
-- If the table tbla has the column col1 and the data type is Bigint.
+------+
| COL1 |
+------+
| 1 |
+------+
| 2 |
+------+
| NULL |
+------+
select count(*) from tbla;  -- value is 3.
select count(col1) from tbla;  -- value is 2
```

The aggregation function can be used with the group by clause. For example, suppose that the table test\_src has two columns, key is a String type, and value is a Double type.

```
-- The data of test_src is shown as follows:
+-----+-------+
| key | value |
+-----+-------+
| a | 2.0 |
+-----+-------+
| a | 4.0 |
+-----+-------+
| b | 1.0 |
+-----+-------+
| b | 3.0 |
+-----+-------+
-- Now run following sentence and get the result:
select key, count(value) as count from test_src group by key;
+-----+-------+
| key | count |
+-----+-------+
| a | 2 |
+-----+-------+
| b | 2 |
+-----+-------+
-- The aggregation function is to do aggregation calculation for the values which has the same key value. The use of other aggregate functions introduced below follows the same rule as above. See this example if necessary.
```

## AVG {#section_oxf_mr1_wdb .section}

**Function definition:**

```
double avg(double value)
decimal avg(decimal value)
```

**Usage:**

This function is used to calculate the average value.

**Parameter description**:

value: Double or Decimal type. If the input is String  or Bigint type, it is converted to Double type by implicit conversion. If it is another data type, an exception is indicated. If this value is NULL, a corresponding row is not involved in the calculation. The input cannot be Boolean type.

**Return value**:

If the input is Decimal type, then return Decimal type. If it is the other valid types, then return Double type.

**For example**:

```
-- If the table tbla has a column value and its data type is Bigint.
+-------+
| value | 
+-------+
| 1 |
| 2 |
| NULL |
+-------+
-- the avg of this column is: (1+2)/2=1.5
select avg(value) as avg from tbla;
+------+
| avg |
+------+
| 1.5 |
+------+
```

## MAX {#section_rys_tr1_wdb .section}

**Function definition:**

```
max(value)
```

**Usage:**

This function is used to calculate the maximum value.

**Parameter description**:

value: can be any data type. If the column value is NULL, the corresponding row is not involved in the operation. Values of the Boolean type are excluded from calculation.

**Return value**:

The return value is of the same type as the value type.

**For example**:

```
-- If the table tbla has a column clo1 and its data type is Bigint. 
+------+
| col1 |
+------+
| 1 |
+------+
| 2 |
+------+
| NULL |
+------+
Select max (value) from tbla; -- return value is 2
```

## MIN {#section_mll_yr1_wdb .section}

**Function definition:**

```
MIN(value)
```

**Usage**:

This function is used to calculate the minimum value of the column.

**Parameter description**:

can be any data type. If the column value is NULL, the corresponding row is not involved in the operation. A Boolean type is not allowed involving in the operation.

**For example**:

```
-- If the table tbla has a column value and its data type is Bigint. 
+------+
| value|
+------+
| 1 |
+------+
| 2 |
+------+
 
+------+
Select min (value) from tbla; -- return value is 1
```

## MEDIAN {#section_m5y_cs1_wdb .section}

**Function definition:**

```
double median(double number)
decimal median(decimal number)
```

**Usage**:

This function is used to calculate the median.

**Parameter description**:

number: Double or Decimal type. If the input is String or Bigint type, it is converted to Double type and involved in the operation. If it is another data type, an exception is indicated.

**Return value**:

Returns the Double or Decimal type.

**For example**:

```
-- If the table tbla has a column value and its data type is Bigint. 
+------+
| value|
+------+
| 1 |
+------+
| 2 |
+------+
| 3 |
+------+
| 4 |
+------+
| 5 |
+------+
select MEDIAN(value) from tbla;  -- return value is 3.0
```

## STDDEV {#section_gg5_dv1_wdb .section}

**Function definition:**

```
double stddev(double number)
decimal stddev(decimal number)
```

**Usage**:

This function is used to calculate a population standard deviation.

**Parameter description**:

number: Double type or Decimal type. If the input is String or Bigint type, it is converted to Double type and involved in operation. If it is another data type, an exception is indicated.

**Return value**:

Returns a Double or Decimal type.

**For example**:

```
-- If the table tbla has a column value and its data type is Bigint. 
+------+
| value|
+------+
| 1 |
+------+
| 2 |
+------+
| 3 |
+------+
| 4 |
+------+
| 5 |
+------+
select STDDEV(value) from tbla;  -- return value is 1.4142135623730951
```

## STDDEV\_SAMP {#section_sgk_jv1_wdb .section}

**Function definition**:

```
double stddev_samp(double number)
decimal stddev_samp(decimal number)
```

**Usage**:

This function is used to calculate a sample standard deviation.

**Parameter description**:

number: Double type or Decimal type. If the input is String or Bigint type, it is converted to Double type and involved in operation. If it is another data type, an exception is indicated.

**Return value**:

Returns a Double or Decimal type.

**For example**:

```
-- If the table tbla has a column value and its data type is Bigint. 
+------+
| value|
+------+
| 1 |
+------+
| 2 |
+------+
| 3 |
+------+
| 4 |
+------+
| 5 |
+------+
select STDDEV_SAMP(value) from tbla; -- return value is 1.5811388300841898
```

## SUM {#section_okf_4v1_wdb .section}

**Function definition:**

```
sum(value)
```

**Usage**:

This function is used to calculate the sum of elements.

**Parameter description**:

value: Double, Decimal, or Bigint type. If the input is String type, it is converted to Double type and involved in operation. If the value in the column is NULL, this row is not involved in the calculation. A Boolean type is not allowed to be involved in the calculation.

**Return value**:

If the input parameter is Bigint type, return Bigint type. If the input parameter is Double type or String type, return Double type.

**For example**:

```
-- If the table tbla has a column value and its data type is Bigint. 
+------+
| value|
+------+
| 1 |
+------+
| 2 |
+------+
| NULL |
+------+
select sum(value) from tbla;   -- return value is 3
```

## WM\_CONCAT {#section_ddm_tv1_wdb .section}

**Function definition:**

```
string wm_concat(string separator, string str)
```

**Usage**:

This function is used to use a specific separator to link the value in str.

**Parameter description**:

-   Separator: a String type constant. Constants of other types or non-constants can cause exceptions.

-   Str: String type. If the input is String type, it is converted to Double type and involved in operation. If it is another data type, an exception is indicated.


**Return value**:

Returns the String type.

**Note:** 

For the sentence `select wm_concat(',', name) from test_src;`, if test\_src is empty set, this MaxCompute SQL sentence returns NULL.

## COLLECT\_LIST {#section_fth_1w1_wdb .section}

**Function definition:**

```
ARRAY collect_list(col)
```

**Usage**:

Within a given group, the expression specified by col is used to aggregate the data into an array.

**Parameter description**:

col: A table column can be any data type.

**Return value**:

Returns the ARRAY type.

**Note:** 

Please add `set odps.sql.type.system.odps2=true;` in front of the SQL statement that uses this function,  and submit it with SQL to use the new data type normally.

## COLLECT\_SET {#section_skl_fw1_wdb .section}

**Function definition:**

```
ARRAY collect_set(col)
```

**Usage**:

Within a given group, the expression specified by col is used to aggregate the data into an array of non-repeating elements.

**Parameter description**:

col: A table column can be any data type.

**Return value**:

Return ARRAY type.

**Note:** 

Please add `set odps.sql.type.system.odps2=true;` in front of the SQL statement that uses this function and submit it with SQL to use the new data type normally.

