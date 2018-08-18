# Window Functions {#concept_nyb_ftl_vdb .concept}

In MaxCompute SQL, window functions help in analyzing and processing the workflow flexibly. Window function can only appear in the ‘select’ clause. However using both the nested window function and aggregate function in window function is not allowed. Also, it cannot be used at the same level as that of the aggregation function together.

**Currently, in a MaxCompute SQL statement, you can use five window functions.**

Window function syntax:

```
window_func() over (partition by [col1,col2…]
[order by [col1[asc|desc], col2[asc|desc]…]] windowing_clause)
```

-   partition  by specifies open window columns. The rows of which partitioned columns have the same values are considered in the same window. Currently, a window can contain at most 100,000,000 rows data. We recommend that the rows must not exceed 5,000,000, otherwise, an error is reported at runtime.
-   The clause order by specifies how the data is ordered in a window.
-   In windowing\_clause part, use rows to specify window open way. The two methods are as follows:
    -   Rows between x preceding|following and y  preceding|following, which indicates the window range is from rows x preceding /following to rows y preceding/following.
    -   Rows x preceding|following: the window range is from rows x preceding /following to the present row.
    -   ‘x’, ‘y’ must be an integer constant that is greater than or equal to 0 and corresponding value range is 0~10000. If the value is 0, it indicates the present row. Use the rows method to specify window range on condition that you have specified ‘order by’ clause for.

**Note:** Not all window functions can be specified window open way using rows. The window functions support this usage include AVG, count, Max, min, StdDev, sum.

## COUNT {#section_q11_32n_vdb .section}

**Function definition**:

```
Bigint count([distinct] expr) over(partition by [col1, col2…]
 [order by [col1[asc|desc], col2[asc|desc]…]] [windowing_clause])
```

**Usage**:

Calculates the total number of retrieved rows.

**Parameter description**:

-   expr: Any data type. When it is NULL, this row is not counted. If the ‘distinct’ keyword is specified, it indicates using the unique count value.
-   partition by \[col1, col2…\]: Specifies the columns to use window function.
-   order by col1 \[asc|desc\], col2\[asc|desc\]: If ‘order by’ clause is not specified,  return the count vale of ‘expr’ in the current window. If ‘order by’ clause is specified, the return result is ordered according to the specified sequence and the value is a cumulative count value from start row to the current row in the current window.

**Return value**:

Bigint type.

**Note:** If the keyword ‘distinct’ is specified, the ‘order by’ clause cannot be used.

**Example**:

Thethe table ‘test\_src’ already exists and the column ‘user\_id’ of bigint type exists in this table.

```
select user_id,
        count(user_id) over (partition by user_id) as count
    from test_src;
    
    | user_id | count |
    
    | 1 | 3 |
    | 1 | 3 |
    | 1 | 3 |
    | 2 | 1 |
    | 3 | 1 |
    
    -- the ‘order by’ clause is not specified, return the count value of user_id in the current window.
    select user_id,
        count(user_id) over (partition by user_id order by user_id) as count
    from test_src;
    
    | user_id | count |
    
    | 1 | 1 | -- start row of the window
    | 1 | 2 | --two records exist from start row to current row. Return 2.
    | 1 | 3 |
    | 2 | 1 |
    | 3 | 1 |
    
    -- The ‘order by’ clause is specified and return a cumulative count value from start row to current row in the current window.
```

## AVG {#section_p53_fwz_vdb .section}

**Function definition**:

```
avg([distinct] expr) over(partition by [col1, col2…]
 [order by [col1[asc|desc], col2[asc|desc]…]] [windowing_clause])
```

**Usage**:

Calculates the average.

**Parameter description**:

-   distinct: if the keyword ‘distinct’ is specified, it indicates taking average of the unique value.
-   expr: Double type. 
    -   If the input is ‘string’ type or ‘bigint’ type, it is converted to ‘double’ type by implicit conversion and involved in the operation. If it is another data type, an exception is thrown.
    -   If this value is NULL, then this row is not counted in the calculation.
    -   If the data type is Boolean, then this row is excluded from the calculation.
-   partition by \[col1, col2...\]: Specified the olumns to use window function.
-   order by col1\[asc|desc\], col2\[asc|desc\]: If ‘order by’ clause is not specified, return the average of all values in the current window. If ‘order by’ clause is specified, the return result is ordered according to the specified sequence and returns the cumulative average from start row to current row in the current window.

**Return value**:

Double type.

**Note:** If the keyword ‘distinct’ isn specified, the ‘order by’ clause cannot be used.

## MAX {#section_qkf_ywz_vdb .section}

**Function definition**:

```
max([distinct] expr) over(partition by [col1, col2…]
[order by [col1[asc|desc], col2[asc|desc]…]] [windowing_clause])
```

**Usage**:

Calculates the maximum value.

**Parameter description**:

-   expr: Any types expect ‘Boolean’. If the value is NULL, this row is not involved in the calculation.  If the keyword ‘distinct’ is specified, it indicates taking the max value of the unique value.
-   partition by \[col1, col2…\]: Specifies columns to use window function.
-   order by \[col1\[asc|desc\], col2\[asc|desc: If ‘order by’ clause is not specified, return the maximum value in the current window.  If ‘order by’ clause is specified, the return result is ordered according to the specified sequence and return the maximum value from start row to current row in the current window.

**Return value**:

Same as the ‘expr’ type..

**Note:** If the keyword ‘distinct’ is specified, the ‘order by’ clause cannot be used.

## MIN {#section_mvt_3xz_vdb .section}

**Function definition**:

```
min([distinct] expr) over(partition by [col1, col2…]
[order by [col1[asc|desc], col2[asc|desc]…]] [windowing_clause])
```

**Usage**:

Calculates the minimum value of the column.

**Parameter description**:

-   exprAny types except ‘Boolean’. If the value is NULL, this row is not counted in the calculation. If the keyword ‘distinct’ is specified, it indicates using the minimum value of a unique value.
-   partition by \[col1, col2…\]: Specifies columns to use window function.
-   order by \[col1\[asc|desc\], col2\[asc|desc: If ‘order by’ clause is not specified, return the minimum value in the current window.  If ‘order by’ clause is specified, the return result is ordered according to the specified sequence and return the minimum value from start row to current row in the current window.

**Return value**:

the same type with ‘expr’.

**Note:** If the keyword ‘distinct’ is specified, the ‘order by’ clause cannot be used.

## MEDIAN {#section_ebx_txz_vdb .section}

**Function definition**:

```
Double median(Double number1,number2...) over(partition by [col1, col2…])
Decimal median(Decimal number1,number2...) over(partition by [col1,col2…])
```

**Usage**:

Calculates the median.

**Parameter description**:

-   number1,number1…: 1 to 255 digits of a Double or Decimal type.
    -   When the input value is a String type or a Bigint type, the operation is performed after the implicit conversion to a Double type, and other types throw exceptions.
    -   Return NULL when the input value is null.
    -   When the input value is a Double type, it converts to the Array of Double by default .
-   partition by \[col1, col2…\]: Specifies columns to use window function.

**Return value**:

Double type.

## STDDEV {#section_e5h_3yz_vdb .section}

**Function definition**:

```
Double stddev([distinct] expr) over(partition by [col1, col2…]
[order by [col1[asc|desc], col2[asc|desc]…]] [windowing_clause])
Decimal stddev([distinct] expr) over(partition by [col1, col2…] 
[order by [col1[asc|desc], col2[asc|desc]…]] [windowing_clause])
```

**Usage**:

Calculates population standard deviation.

**Parameter description**:

-   expr: Double type.
    -    If the input is ‘string’ or ‘bigint’ type, it is converted to ‘double’ type and is counted in the operation. If it is another data type, an exception is thrown. 
    -   If the input value is ‘NULL’, this row is excluded. 
    -   If the keyword ‘distinct’ is specified, it indicates calculating the population standard deviation of the unique value.
-   partition by \[col1, col2..\]: Specifies columns to use window function.
-   order by col1\[asc|desc\], col2\[asc|desc\]: If ‘order by’ clause is not specified, return the population standard deviation in the current window.  If ‘order by’ clause is specified, the return result is ordered according to the specified sequence and return the population standard deviation from start row to current row in the current window.

**Return value**:

When the input is ‘decimal’ type, return ‘decimal’; otherwise, return ‘double’.

**Example**:

```
select window, seq, stddev_pop('1\01') over (partition by window order by seq) from dual;
```

**Note:** 

-   If the keyword ‘distinct’ is specified, the ‘order by’ clause cannot be used.
-   Stddev\_pop is an alias function of stddev function and its usage is the same as that of stddev


## STDDEV\_SAMP {#section_skf_2zz_vdb .section}

**Function definition**:

```
Double stddev_samp([distinct] expr) over(partition by [col1, col2…]
[order by [col1[asc|desc], col2[asc|desc]…]] [windowing_clause])
Decimal stddev_samp([distinct] expr) over((partition by [col1,col2…] 
[order by [col1[asc|desc], col2[asc|desc]…]] [windowing_clause])
```

**Usage**:

Calculate sample standard deviation.

**Parameter description**:

-   Expr: Double type. 
    -   If the input is ‘string’ or ‘bigint’ type, it is converted to ‘double’ type and counted in the operation. If it is another data type, an exception is indicated. 
    -   If the input value is NULL, this row is excluded. 
    -   If the keyword ‘distinct’ is specified, it indicates calculating the sample standard deviation of the unique value.
-   partition by \[col1, col2..\]: Specifies columns to use window function.
-   Order by col1\[asc|desc\], col2\[asc|desc\]: If ‘order by’ clause is not specified, return the sample standard deviation in the current window.  If ‘order by’ clause is specified, the return result is ordered according to the specified sequence and return the sample standard deviation from start row to current row in the current window.

**Return value**:

When the input is ‘decimal’ type, return ‘decimal’; otherwise, return ‘double’.

**Note:** If the keyword ‘distinct’ is specified, the ‘order by’ clause cannot be used.

## SUM {#section_ggy_vzz_vdb .section}

**Function definition**:

```
sum([distinct] expr) over(partition by [col1, col2…]
[order by [col1[asc|desc], col2[asc|desc]…]] [windowing_clause])
```

**Usage**:

Calculates the sum of elements.

**Parameter description**:

-   Expr: Double type. 
    -   If the input is ‘string’ or ‘bigint’ type, it is converted to ‘double’ type and counted in the operation. If it is another data type, an exception is indicated. 
    -   If the input value is NULL, this row is excluded. 
    -   If the keyword ‘distinct’ is specified, it indicates calculating the sum of the unique value.
-   Partition by \[col1, col2..\]: Specifies columns to use window function.
-   Order by col1\[asc|desc\], col2\[asc|desc\]: If ‘order by’ clause is not specified, return the sum in the current window.  If ‘order by’ clause is specified, the return result is ordered according to the specified sequence and return the sum from start row to current row in the current window.

**Return value**:

-   If the input parameter is ‘bigint’ type, return ‘bigint’ type. 
-   If the input parameter is ‘Decimal’ type, return ‘Decimal’ type. 
-   If the input parameter is ‘double’ type or ‘string’ type, return ‘double’ type.

**Note:** If the keyword ‘distinct’ is specified, the ‘order by’ clause cannot be used.

## DENSE\_RANK {#section_mj4_k11_wdb .section}

**Function definition**:

```
Bigint dense_rank() over(partition by [col1, col2…]
order by [col1[asc|desc], col2[asc|desc]…])
```

**Usage**:

Calculates the dense rank. The data in the same row of col2 has the same rank.

**Parameter description**:

-   partition by \[col1, col2..\]: Specifies columns to use window function.
-   order by col1\[asc|desc\], col2\[asc|desc\]: Specifies the value which the rank is based on.

**Return value**:

Bigint type.

**Example**:

The data in table ‘emp’ is as follows:

```
| empno | ename | job | mgr | hiredate| sal| comm | deptno |
7369,SMITH,CLERK,7902,1980-12-17 00:00:00,800,,20
7499,ALLEN,SALESMAN,7698,1981-02-20 00:00:00,1600,300,30
7521,WARD,SALESMAN,7698,1981-02-22 00:00:00,1250,500,30
7566,JONES,MANAGER,7839,1981-04-02 00:00:00,2975,,20
7654,MARTIN,SALESMAN,7698,1981-09-28 00:00:00,1250,1400,30
7698,BLAKE,MANAGER,7839,1981-05-01 00:00:00,2850,,30
7782,CLARK,MANAGER,7839,1981-06-09 00:00:00,2450,,10
7788,SCOTT,ANALYST,7566,1987-04-19 00:00:00,3000,,20
7839,KING,PRESIDENT,,1981-11-17 00:00:00,5000,,10
7844,TURNER,SALESMAN,7698,1981-09-08 00:00:00,1500,0,30
7876,ADAMS,CLERK,7788,1987-05-23 00:00:00,1100,,20
7900,JAMES,CLERK,7698,1981-12-03 00:00:00,950,,30
7902,FORD,ANALYST,7566,1981-12-03 00:00:00,3000,,20
7934,MILLER,CLERK,7782,1982-01-23 00:00:00,1300,,10
7948,JACCKA,CLERK,7782,1981-04-12 00:00:00,5000,,10
7956,WELAN,CLERK,7649,1982-07-20 00:00:00,2450,,10
7956,TEBAGE,CLERK,7748,1982-12-30 00:00:00,1300,,10
```

Now, all employees need to be grouped by department, and each group must be sorted in descending order according to SAL to obtain the serial number in own group.

```
SELECT deptno
        , ename
        , sal
        , DENSE_RANK() OVER (PARTITION BY deptno ORDER BY sal DESC) AS nums--Deptno as a window column, and sort in descending order according to sal.
    FROM emp;
--The result is as follows:

| deptno | ename | sal | nums |

| 10 | JACCKA | 5000.0 | 1 |
| 10 | KING | 5000.0 | 1 |
| 10 | CLARK | 2450.0 | 2 |
| 10 | WELAN | 2450.0 | 2 |
| 10 | TEBAGE | 1300.0 | 3 |
| 10 | MILLER | 1300.0 | 3 |
| 20 | SCOTT | 3000.0 | 1 |
| 20 | FORD | 3000.0 | 1 |
| 20 | JONES | 2975.0 | 2 |
| 20 | ADAMS | 1100.0 | 3 |
| 20 | SMITH | 800.0 | 4 |
| 30 | BLAKE | 2850.0 | 1 |
| 30 | ALLEN | 1600.0 | 2 |
| 30 | TURNER | 1500.0 | 3 |
| 30 | MARTIN | 1250.0 | 4 |
| 30 | WARD | 1250.0 | 4 |
| 30 | JAMES | 950.0 | 5 |

```

## RANK {#section_yvx_jb1_wdb .section}

**Function definition**:

```
Bigint rank() over(partition by [col1, col2…]
order by [col1[asc|desc], col2[asc|desc]…])
```

**Usage**:

Calculates the rank.  The ranking of the same row data with col2 drops.

**Parameter description**:

-   Partition by \[col1, col2..\]: Specifies columns to use window function.
-   Order by col1\[asc|desc\], col2\[asc|desc\]: Specifies the value which the rank is based on.

**Return value**:

Bigint type.

**Example**:

The data in table ‘emp’ is as follows:

```
| empno | ename | job | mgr | hiredate| sal| comm | deptno |
7369,SMITH,CLERK,7902,1980-12-17 00:00:00,800,,20
7499,ALLEN,SALESMAN,7698,1981-02-20 00:00:00,1600,300,30
7521,WARD,SALESMAN,7698,1981-02-22 00:00:00,1250,500,30
7566,JONES,MANAGER,7839,1981-04-02 00:00:00,2975,,20
7654,MARTIN,SALESMAN,7698,1981-09-28 00:00:00,1250,1400,30
7698,BLAKE,MANAGER,7839,1981-05-01 00:00:00,2850,,30
7782,CLARK,MANAGER,7839,1981-06-09 00:00:00,2450,,10
7788,SCOTT,ANALYST,7566,1987-04-19 00:00:00,3000,,20
7839,KING,PRESIDENT,,1981-11-17 00:00:00,5000,,10
7844,TURNER,SALESMAN,7698,1981-09-08 00:00:00,1500,0,30
7876,ADAMS,CLERK,7788,1987-05-23 00:00:00,1100,,20
7900,JAMES,CLERK,7698,1981-12-03 00:00:00,950,,30
7902,FORD,ANALYST,7566,1981-12-03 00:00:00,3000,,20
7934,MILLER,CLERK,7782,1982-01-23 00:00:00,1300,,10
7948,JACCKA,CLERK,7782,1981-04-12 00:00:00,5000,,10
7956,WELAN,CLERK,7649,1982-07-20 00:00:00,2450,,10
7956,TEBAGE,CLERK,7748,1982-12-30 00:00:00,1300,,10
```

Now, all employees need to be grouped by department, and each group must be sorted in descending order according to SAL to obtain the serial number in own group.

```
SELECT deptno
        , ename
        , sal
        , RANK() OVER (PARTITION BY deptno ORDER BY sal DESC) AS nums--Deptno as a window column, and sort in descending order according to sal.
    FROM emp;
--The result is as follows:

| deptno | ename | sal | nums |

| 10 | JACCKA | 5000.0 | 1 |
| 10 | KING | 5000.0 | 1 |
| 10 | CLARK | 2450.0 | 3 |
| 10 | WELAN | 2450.0 | 3 |
| 10 | TEBAGE | 1300.0 | 5 |
| 10 | MILLER | 1300.0 | 5 |
| 20 | SCOTT | 3000.0 | 1 |
| 20 | FORD | 3000.0 | 1 |
| 20 | JONES | 2975.0 | 3 |
| 20 | ADAMS | 1100.0 | 4 |
| 20 | SMITH | 800.0 | 5 |
| 30 | BLAKE | 2850.0 | 1 |
| 30 | ALLEN | 1600.0 | 2 |
| 30 | TURNER | 1500.0 | 3 |
| 30 | MARTIN | 1250.0 | 4 |
| 30 | WARD | 1250.0 | 4 |
| 30 | JAMES | 950.0 | 6 |

```

## LAG {#section_dbf_xb1_wdb .section}

**Function definition**:

```
lag(expr，Bigint offset, default) over(partition by [col1, col2…]
[order by [col1[asc|desc], col2[asc|desc]…]])
```

**Command description**:

Take the value of nth row in front of current row in accordance with offset. If the current row number is rn, take the value of the row which row number is rn-offset.

**Parameter description**:

-   expr: Any type.
-   offset: A Bigint type constant.  If the input is String type or Double type, convert it to Bigint type by implicit conversion. Offset \> 0;
-   default: Define the default value while the specified range of ‘offset’ crosses the limit. It is constant and default is null.
-   partition by \[col1, col2..\]: Specifies columns to use window function.
-   order by col1\[asc|desc\], col2\[asc|desc\]: Specifies the order method for return result.

**Return Value**:

Returns the same with ‘expr’.

## LEAD {#section_s5f_jc1_wdb .section}

**Command format**:

```
lead(expr，Bigint offset, default) over(partition by [col1, col2…]
[order by [col1[asc|desc], col2[asc|desc]…]])
```

**Command description**:

Take the value of nth row following current row in accordance with offset. If the current row number is rn, take the value of the row which row number is rn+offset.

**Parameter description**:

-   expr: Any type.
-   offset: A Bigint type constant.  If the input is String, Decimal or Double type, convert it to Bigint type by implicit conversion. Offset \> 0.
-   default: Define the default value while the specified range of offset crosses the limit. It is constant.
-   partition by \[col1, col2..\]: Specifies columns to use window function.
-   order by col1\[asc|desc\], col2\[asc|desc\]: Specifies the order method for return result.

**Return Value**:

Same as the ‘expr’ type.

**Example**:

```
select c_Double_a,c_String_b,c_int_a,lead(c_int_a,1) over(partition by c_Double_a order by c_String_b) from dual;
select c_String_a,c_time_b,c_Double_a,lead(c_Double_a,1) over(partition by c_String_a order by c_time_b) from dual;
select c_String_in_fact_num,c_String_a,c_int_a,lead(c_int_a) over(partition by c_String_in_fact_num order by c_String_a) from dual;
```

## PERCENT\_RANK {#section_lmk_tc1_wdb .section}

Command format:

```
Percent_rank () over (partition by [col1, col2...]
order by [col1[asc|desc], col2[asc|desc]…])
```

**Command description**:

Calculate relative ranking of a certain row in a group of data.

**Parameter description**:

-   partition by \[col1, col2..\]: Specifies columns to use window function.
-   order by col1\[asc|desc\], col2\[asc|desc\]: Specifies the value based on the ranking.

**Return Value**:

Returns the Double type, value scope is \[0, 1\]. The calculation method of relative ranking is `(rank-1)/(number of rows -1)`.

**Note:** The current limit of rows in a single window cannot exceed 10,000,000.

## ROW\_NUMBER {#section_cm1_cd1_wdb .section}

**Command format**:

```
row_number() over(partition by [col1, col2…]
order by [col1[asc|desc], col2[asc|desc]…])
```

**Command description**:

Calculates the row number, beginning from 1.

**Parameter description**:

-   partition by \[col1, col2..\]: Specifies columns to use window function.
-   order by col1\[asc|desc\], col2\[asc|desc\]: Specifies the order method for return result.

**Return Value**:

Returns the Bigint type.

**Example**:

The data in table emp is as follows:

```
| empno | ename | job | mgr | hiredate| sal| comm | deptno |
7369,SMITH,CLERK,7902,1980-12-17 00:00:00,800,,20
7499,ALLEN,SALESMAN,7698,1981-02-20 00:00:00,1600,300,30
7521,WARD,SALESMAN,7698,1981-02-22 00:00:00,1250,500,30
7566, Jones, Manager, fig-04-02 00:00:00, 2975, 20
7654,MARTIN,SALESMAN,7698,1981-09-28 00:00:00,1250,1400,30
7698,BLAKE,MANAGER,7839,1981-05-01 00:00:00,2850,,30
7782,CLARK,MANAGER,7839,1981-06-09 00:00:00,2450,,10
7788, Scott, analyst, fig-04-19 00:00:00, 3000, 20
7839,KING,PRESIDENT,,1981-11-17 00:00:00,5000,,10
7844,TURNER,SALESMAN,7698,1981-09-08 00:00:00,1500,0,30
7876,ADAMS,CLERK,7788,1987-05-23 00:00:00,1100,,20
7900,JAMES,CLERK,7698,1981-12-03 00:00:00,950,,30
7902,FORD,ANALYST,7566,1981-12-03 00:00:00,3000,,20
7934,MILLER,CLERK,7782,1982-01-23 00:00:00,1300,,10
7948,JACCKA,CLERK,7782,1981-04-12 00:00:00,5000,,10
7956,WELAN,CLERK,7649,1982-07-20 00:00:00,2450,,10
7956, tebage, clerk, maid-12-30 00:00:00, 1300, 10
```

Now, all employees need to be grouped by department, and each group must be sorted in descending order according to SAL to obtain the serial number in own group.

```
SELECT deptno
        , ename
        , Sal
        , Row_number () over (partition by deptno order by Sal DESC) as Nums  --Deptno as a window column, and sort in descending order according to sal.
    FROM emp;
--The result is as follows:

| deptno | ename | sal | nums |

| 10 | JACCKA | 5000.0 | 1 |
| 10 | KING | 5000.0 | 2 |
| 10 | CLARK | 2450.0 | 3 |
| 10 | WELAN | 2450.0 | 4 |
| 10 | TEBAGE | 1300.0 | 5 |
| 10 | MILLER | 1300.0 | 6 |
| 20 | SCOTT | 3000.0 | 1 |
| 20 | FORD | 3000.0 | 2 |
| 20 | JONES | 2975.0 | 3 |
| 20 | ADAMS | 1100.0 | 4 |
| 20 | SMITH | 800.0 | 5 |
| 30 | BLAKE | 2850.0 | 1 |
| 30 | ALLEN | 1600.0 | 2 |
| 30 | TURNER | 1500.0 | 3 |
| 30 | MARTIN | 1250.0 | 4 |
| 30 | WARD | 1250.0 | 5 |
| 30 | JAMES | 950.0 | 6 |

```

## CLUSTER\_SAMPLE {#section_mst_md1_wdb .section}

**Command format**:

```
boolean cluster_sample([Bigint x, Bigint y])
over(partition by [col1, col2..])
```

**Command description**:

Used for Group sampling.

**Parameter description**:

-   x: A Bigint type constant, x\>=1.  If you specify the parameter y, x indicates dividing a window into x parts.  Otherwise x indicates selecting x rows records in a window \(if x rows are in this window, return true\).  If x is NULL, return NULL.
-   y: A Bigint type constant, y\>=1, y<=x.  It indicates selecting y parts records from x parts in a window \(in other words, if y parts records exist, return value is true\).  If y is NULL, return NULL.
-   partition by \[col1, col2\]: Specifies columns to use window function.

**Return Value**:

Returns the Boolean type.

**Example**:

If two columns key and value are in the table test\_tbl, key is grouping field. The corresponding values of key have groupa and groupb, the field value indicates value of key shown as follows:

```

    | key | value |
    
    | groupa | -1.34764165478145 |
    | groupa | 0.740212609046718 |
    | groupa | 0.167537127858695 |
    | groupa | 0.630314566185241 |
    | GroupA | 0.0112401388646925 |
    | groupa | 0.199165745875297 |
    | groupa | -0.320543343353587 |
    | groupa | -0.273930924365012 |
    | groupa | 0.386177958942063 |
    | groupa | -1.09209976687047 |
    | groupb | -1.10847690938643 |
    | groupb | -0.725703978381499 |
    | groupb | 1.05064697475759 |
    | groupb | 0.135751224393789 |
    | groupb | 2.13313102040396 |
    | groupb | -1.11828960785008 |
    | groupb | -0.849235511508911 |
    | groupb | 1.27913806620453 |
    | groupb | -0.330817716670401 |
    | groupb | -0.300156896191195 |
    | groupb | 2.4704244205196 |
    | groupb | -1.28051882084434 |
    
```

To select 10% values from each group, the following MaxCompute  SQL is recommended:

```
Select key, Value
    from (
        Select key, value, cluster_sample (10, 1) over (partition by key) as flag
        from tbl
        ) sub
    where flag = true;

| Key | value |

| groupa | 0.167537127858695 |
| groupb | 0.135751224393789 |

```

## NTILE {#section_gjj_c21_wdb .section}

**Command format**:

```
BIGINT ntile(BIGINT n) over(partition by [col1, col2…]  
[order by [col1[asc|desc], col2[asc|desc]…]] [windowing_clause]))
```

**Command description**:

Used to cut grouped data into N slices in order and return the current slice value, if the slice is uneven, the distribution of the first slice is increased by default.

Parameter description:

N: bigint data type.

Return Value:

Returns the bigint type.

Example:

The data in the table EMP is as follows:

```
| Empno | ename | job | Mgr | hiredate | Sal | REM | deptno |
7369, Smith, clerk, maid-12-17 00:00:00, 800, 20
7499, Allen, salesman, maid-02-20 00:00:00, 1600,300, 30
7521, Ward, salesman, maid-02-22 00:00:00, 1250,500, 30
7566, Jones, Manager, fig-04-02 00:00:00, 2975, 20
7654 Martin, salesman, fig-09-28 00:00:00, fig, 30
7698, Blake, Manager, fig-05-01 00:00:00, 2850, 30
7782, Clark, Manager, fig-06-09 00:00:00, 2450, 10
7788, Scott, analyst, fig-04-19 00:00:00, 3000, 20
00:00:00, King, President, 1991-11-17 5000, 7839, 10
7844, Turner, salesman, fig-09-08 00:00:00, 1500,0, 30
7876, Adams, clerk, maid-05-23 00:00:00, 1100, 20
7900 James, clerk, maid-12-03 00:00:00, 950, 30
7902 Ford, analyst, fig-12-03 00:00:00, 3000, 20
7934 Miller, clerk, fig-01-23 00:00:00, 1300, 10
7948, jaccka, clerk, fig-04-12 00:00:00, 5000, 10
7956, welan, clerk, fig-07-20 00:00:00, 2450, 10
7956, tebage, clerk, maid-12-30 00:00:00, 1300, 10
```

All employees now need to be divided into three groups according to Sal high to low cut, and get the serial number of the employee's own group.

```
Select deptno, ename, Sal, ntile (3) over (partition by depno order by Sal DESC) as nt3 from EMP;
-- Execution results as follows

| Deptno | ename | Sal | nt3 |

| 10 | jaccka | 5000.0 | 1 |
| 10 | King | 5000.0 | 1 |
| 10 | welan | 2450.0 | 2 |
| 10 | Clark | 2450.0 | 2 |
| 10 | tebage | 1300.0 | 3 |
10 | Miller | 1300.0 | 3 |
| 20 | Scott | 3000.0 | 1 |
| 20 | Ford | 3000.0 | 1 |
| 20 | Jones | 2975.0 | 2 |
| 20 | Adams | 1100.0 | 2 |
| 20 | Smith | 800.0 | 3 |
| 30 | Blake | 2850.0 | 1 |
| 30 | Allen | 1600.0 | 1 |
| 30 | Turner | 1500.0 | 2 |
| 30 | Martin | 1250.0 | 2 |
| 30 | ward | 1250.0 | 3 |
| 30 | James | 950.0 | 3 |

```

