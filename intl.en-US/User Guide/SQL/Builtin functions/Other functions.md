# Other functions {#concept_wzd_xhm_vdb .concept}

This article shows you how to use functions such as cast, decode, least, array, split, map, and so on.

## CAST {#section_bpc_dy1_wdb .section}

Function definition:

```
cast(expr as <type>)
```

Convert the result of expression to object type. For example, cast \(‘1’ as bigint\) is to convert string ‘1’ to bingint ‘1’. If the conversion is unsuccessful or the conversion is not supported, an exception occurs.

**Note:** 

-   cast \(double as bigint\) converts double type value to bigint type value.
-   cast\(string as bigint\) converts a value of the string type into a value of the bigint type. If the string is composed of numerals expressed in integer form, it is directly converted into a value of the bigint type.
-   If the string is composed of numerals expressed in the float or exponent form, it will be converted into a value of the double type first and then into a value of the bigint type.
-   cast\(string as datetime\) or cast\(datetime as string\) adopts the default format `yyyy-mm-dd hh:mi:ss`.

## COALESCE {#section_dts_3y1_wdb .section}

Function definition:

```
coalesce(expr1, expr2, ...)
```

Usage:

Return the first value which is not NULL from the list. If all values in the list are NULL, return NULL.

Parameter description:

expr: value to be tested. All these values have the same data type or be NULL, otherwise an expection occurs.

Return value:

Return value type is the same as parameter type.

**Note:** There must be one parameter at least, otherwise an exception occurs.

## DECODE {#section_ygq_4y1_wdb .section}

Function definition:

```
decode(expression, search, result[, search, result]...[, default])
```

Usage:

Implement the selection function of if-then-else branch.

Parameter description:

-   expression: expression to be compared.
-   search: A search string to be compared with the expression.
-   result: the returned value when the values of search and expression match.
-   default: it is optional. If all search items do not match the expression, return this default value. If it is not specified, return NULL.

Return Value:

-   return matched search.
-   If no matching record exists, return default.
-   If default is not specified, return NULL.

    **Note:** 

    -   You must specify at least three parameters.
    -   All of the result types must be the same or NULL. Inconsistent data type causes an exception. All of the search and expression types must be consistent, otherwise an exception is reported.
    -   If the option search in decode has repeated record and has been matched, return the first value.

Example:

```
Select
decode(customer_id,
1, 'Taobao',
2, 'Alipay',
3, 'Aliyun',
Null, 'N/A',
'Others') as result
from sale_detail;
```

The decode function mentioned previously implements the function in following if-then-else sentence:

```
if customer_id = 1 then
result := 'Taobao';
elsif customer_id = 2 then
result := 'Alipay';
elsif customer_id = 3 then
result := 'Aliyun';
...
else
result := 'Others';
end if;
```

**Note:** 

-   Calculating NULL= NULL by MaxCompute SQL, return NULL, while the values of NULL and NULL are equal in decode function.
-   In the preceding example, if the value of customer\_id is NULL, decode function returns N/A as a result.

## GET\_IDCARD\_AGE {#section_j2q_1z1_wdb .section}

Function definition:

```
get_idcard_age(idcardno)
```

Usage:

Returns the current age based on the ID number which is the difference of the current year and the year of birth identified in the ID.

Parameter description:

idcardno: String type, ID number of 15-digit or 18-digit. In the calculation, the validity of the ID is checked according to the province code and the last digit, and Null is returned if the check fails.

Return Value:

Returns the Bigint type. Input is Null, returns Null. Returns Null if the difference of the current year and the year of birth is larger than 100.

## GET\_IDCARD\_BIRTHDAY {#section_tfq_dz1_wdb .section}

Function definition:

```
get_idcard_birthday(idcardno)
```

Usage:

Returns date of birth based on the ID number.

Parameter description:

idcardno: String type, ID number of 15-digit or 18-digit. In the calculation, the validity of the ID is checked according to the province code and the last digit, and Null is returned if the check fails.

Return Value:

Returns the Datetime type. Input is Null, returns Null.

## GET\_IDCARD\_SEX {#section_akt_gz1_wdb .section}

Function definition:

```
get_idcard_sex(idcardno)
```

Usage:

Returns the gender based on the ID number and the value is either M \(male\) or F \(female\).

Parameter description:

idcardno: String type, ID number of 15-digit or 18-digit. In the calculation, the validity of the ID is checked according to the province code and the last digit, and Null is returned if the check fails.

Return Value:

Returns the String type. Input is Null, returns Null.

## GREATEST {#section_n1g_kz1_wdb .section}

Function definition:

```
greatest(var1, var2, ...)
```

Usage:

Return the greatest input parameter.

Parameter description:

var1/var2: Its type can be Bigint, Double, Decimal，Datetime or String type. If all values are NULL, return NULL.

Return Value:

-   The greatest value in input parameter. If the implicit conversion is not needed, return type is the same as input parameter type.
-   NULL is the least value.

    If the input parameter types are different,

    -   For Double, Bigint, Decimal and String type, convert them to be Double type.
    -   For String and Datetime, convert them to be Datetime type.
    -   Other implicit conversion is not allowed.

## ORDINAL {#section_pcj_pz1_wdb .section}

Function definition:

```
ordinal(bigint nth, var1, var2, ...)
```

Usage:

Return the location value specified by ‘nth’ after the input variables are sorted by small to large.

Parameter description:

-   nth: Bigint type, specify the location to return its value. If it is NULL, return NULL.
-   var1/var2: Its type can be Bigint, Double, Datetime or String type.

Return Value:

-   The value in nth bit. If the implicit conversion is not needed, return type is the same as input parameter type.
-   If implicit conversion is in input parameters,
    -   For Double, Bigint and String type, convert them to be Double type.
    -   For String and Datetime type, convert them to be Datetime type.
    -   Other implicit conversion is not allowed.
-   NULL is the least value.

Example:

```
ordinal(3, 1, 3, 2, 5, 2, 4, 6) = 2
```

## LEAST {#section_m1b_xz1_wdb .section}

Function definition:

```
least(var1, var2, ...)
```

Usage:

return the least value in input parameter.

Parameter description:

var1/var2: Its type can be Bigint, Double, Decimal，Datetime or String type. If all values are NULL, return NULL.

Return Value:

-   The least value in input parameter; If the implicit conversion is not needed, return type is the same as input parameter type.
-   If implicit conversion is in input parameters,
    -   For Double, Bigint and String type, convert them to be Double type.
    -   For ‘string’ type and ‘datetime’ type, convert them to be ‘datetime’ type.
    -   Converts to Decimal type when Decimal type compares to Double, Bigint or String type.
    -   Other implicit conversion is not allowed.
-   NULL is the least value.


## MAX\_PT {#section_yyf_d1b_wdb .section}

Function definition:

```
max_pt(table_full_name)
```

Usage:

For a partitioned table, this function returns the maximum value of the level-one partition of the partitioned table, which is sorted alphabetically, and there is a corresponding data file for the partition.

Parameter description:

table\_full\_name: String type, specifys the name of table, which must be with the name of project, for example: prj.src\). You must own read permission on this table.

Return Value:

Return value: Returns the value of the largest level-one partition.

Example:

Example: Suppose that ‘tbl’ is a partitioned table, all partitions of the table are as follows, and there are data files:

```
pt =‘20120901’
pt =‘20120902’
```

In the following statement, the return value of max\_pt is ‘20120902’, and the MaxCompute SQL statement reads the data in the ‘20120902’ partition.

```
select * from tbl where pt=max_pt('myproject.tbl');
```

**Note:** 

If a new partition is added by using alter table, but there is no data file in this partition, then this partition is not returned.

## UUID {#section_gtg_j1b_wdb .section}

Function definition:

```
string uuid()
```

Usage:

Return a random ID. Example: `29347a88-1e57-41ae-bb68-a9edbdd94212`.

**Note:** UUID returns a random global ID with a low probability of duplication.

## SAMPLE {#section_vvl_l1b_wdb .section}

Function definition:

```
boolean sample(x, y, column_name)
```

Usage:

sample all values of column\_name according to the setting of x and y and filter out the rows which do not meet the sampling condition.

Parameter description:

-   x, y: Bigint type, indicates hash to x portions, take yth portions. y can be ignored.
    -   If y is ignored, take the first portion. If y in parameter is ignored, then column\_name is ignored at the same time.
    -   x and y are Bigint constants and greater than 0. If it is other data type or less than or equal to 0, an exception is thrown. If y&gt, x exception is also thrown. If any input of x and y is NULL, return NULL.
-   column\_name: the destination column to be sampled.
    -   column\_name can be omitted, in which case, a random sample is taken according to the values of x and y.
    -   It can be any data type and the column value can be NULL. Do not need implicit type conversion.
    -   If column\_name is the constant NULL, an exception is reported.

Return Value:

Boolean type.

**Note:** 

To avoid data skew brought by NULL value, NULL values in column\_name will be carried out a uniform hash in x portions. If column\_name is not added, the output is not necessarily uniform since the data size is smaller. So column\_name is suggested to be added to get better output.

Example:

Suppose that the table tbla is existent and a column cola is in this table:

```
select * from tbla where sample (4, 1 , cola) = true;
-- The values are carried out Hash into 4 portions and take the first portion.
select * from tbla where sample (4, 2) = true;
-- The values do random Hash into 4 portions for each row of data and take the second portion.
```

## CASE WHEN EXPRESSION {#section_axm_v1b_wdb .section}

MaxCompute provides two kinds of case when syntax formats, as follows:

```
case
when (_condition1) then result1
when (_condition2) then result2
...
else resultn
end
case
when (_condition1) then result1
when (_condition2) then result2
when (_condition3) then result3
...
else resultn
end
```

Case when expression can return different values according to the computing result of expression values flexibly.

The following sentences is used to get the region according to different shop\_name:

```
select
case
when shop_name is null then 'default_region'
when shop_name like 'hang%' then 'zj_region'
end as region
From sale_detail;
```

**Note:** 

-   If the types of result include Bigint and Double, convert them to Double type and then return the result.
-   If the types of result include string type, convert them to be string type and then return the result. If the conversion is unsuccessfully, the error is reported. \(such as Boolean type\).
-   Expect these, the conversion between other types is not allowed.

## If expression {#section_mg5_1bb_wdb .section}

Function definition:

```
if(testCondition, valueTrue, valueFalseOrNull)
```

Usage:

Judge if testCondition is true. If it is true, return valueTrue, otherwise return valueFalse or Null.

Parameter description:

-   testCondition: The expression to be judged. Boolean type.
-   valueTrue: It returns when the expression testCondition is true.
-   valueFalseOrNull: It returns when the expression testCondition is not true and also can be null.

Return Value:

The return type is the same as the valueTrue or valueFalseOrNul type.

Example:

```
select if(1=2,100,200) from dual; 
--Returned results:
+ ------------ +
| _c0 |
+------------+
| 200 |
+------------+
```

## SPLIT {#section_omq_nbb_wdb .section}

Function definition:

```
split(str, pat)
```

Purpose: After the STR is split by Pat, the array is returned.

Parameter description:

-   str: String type, specifies the string to be separated.
-   pat: String type, specifies the delimiter, supports regular expressions.

Return Value:

`array <string>`

The result is the elements in str separated by pat.

Example:

```
select split("a,b,c",",") from dual;
Results:
+------+
| _c0 |
+------+
| [a, b, c] |
+------+
```

**Note:** 

Set commands supported by MaxCompute SQL and MapReduce for MaxCompute 2.0

-   Once data type such as Tinyint、Smallint、 Int、 Float、Varchar or TIMESTAMP BINARY is involved when running an SQL statement, `set odps.sql.type.system.odps2=true;` must be added before the SQL statement. The set statement and SQL statement are submitted simultaneously.
-   Project level: that is, the project level is supported for new type opening. The project owner can be set to project as needed, with the following commands:

    ```
    set odps.sql.type.system.odps2=true;
    
    ```


## STR\_TO\_MAP {#section_p1z_xrj_dfb .section}

Function declaration:

```
str_to_map(text [, delimiter1 [, delimiter2]])
```

Purpose: use ‘delimiter1’ to separate ‘text’ into K-V pairs, then use ‘delimiter2’ to separate each K-V pair.

Parameter description

-   text: String type, specifies the string to be separated.
-   delimiter1: string type, separator that does not specify the default ','.
-   delimiter1: string type, separator, default to '=' when not specified '.

Return value: map <string, string \>. The elements are the K-V results of the separation of 'text' by the strings 'delimiter1' and 'delimiter2'.

Example:

```
Select fig ('test1 &amp; 1-test2 & 2 ','-','&amp;');
```

Return result:

```
+------------+
| A |
+------------+
| {Test1: 1, Test2: 2} |
```

## EXPLODE {#section_wgw_xbb_wdb .section}

Function definition:

```
explode(var)
```

Usage:

Converts one row of data into a multi-row UDTF.

-   If var is Array type, the array stored in the column is converted to multiple rows.
-   If var is Map type, each key-value pair of the map stored in the column is converted to a row with two columns, one column for the key and one for the value.

Parameter description:

`var: array<T> type or map<K, V> type.`

Return Value:

Rows after conversion are returned.

**Note:** 

The following restrictions apply when using UDTF:

-   One select can only have one UDTF and no other columns can appear.
-   It cannot be used with group by, cluster by, distribute by, or sort by.

Example:

```
explode(array(null, 'a', 'b', 'c')) col
```

## MAP {#section_bzn_hcb_wdb .section}

Function definition:

```
MAP map(K key1, V value1, K key2, V value2, ...)
```

Usage:

Uses the given key/value pairs to create a map.

Parameter descriptio:

key/value

-   All key types are consistent, including those after implicit conversion, and must be basic.
-   All value types are consistent, including those after implicit conversion, and can be of any type.

Return Value:

Returns the map type.

Example:

For example, the fields in t\_table are\(c1 bigint,c2 string,c3 string, c4 bigint ,c5 bigint\), with the following data

```
+------------+----+----+------------+------------+
| c1         | c2 | c3 | c4         | c5         |
+------------+----+----+------------+------------+
| 1000       | k11 | k21 | 86         | 15         |
| 1001       | k12 | k22 | 97         | 2          |
| 1002       | k13 | k23 | 99         | 1          |
+------------+----+----+------------+------------+
```

Execute SQL:

```
select  map(c2,c4,c3,c5) from t_table;
```

The result is as follows:

```
+ ---- +
| _c0 |
+------+
| {k11:86, k21:15} |
| {k12:97, k22:2} |
| {k13:99, k23:1} |
+------+
```

## MAP\_KEYS {#section_mym_clb_wdb .section}

Function definition:

```
ARRAY map_keys(map<K, V>)
```

Usage:

Returns an array of all the keys in the map parameter.

Parameter description:

map：map type data.

Return value:

Returns the array type, enter null, and null.

Example:

For example, the field of t\_table\_map is \(`c1 bigint,t_map map<string,bigint>`\), data as follows

```
+------------+-------+
| C1 | t_map |
+ ------------ + ------- +
| 1000       | {k11:86, k21:15} |
| 1001       | {k12:97, k22:2} |
| 1002       | {k13:99, k23:1} |
+------------+-------+
```

Execute SQL:

```
select  c1,map_keys(t_map) from t_table_map;
```

The result is as follows:

```
+------------+------+
| c1         | _c1  |
+------------+------+
| 1000       | [k11, k21] |
| 1001       | [k12, k22] |
| 1002       | [k13, k23] |
+------------+------+
```

## MAP\_VALUES {#section_pd3_jlb_wdb .section}

Function definition:

```
ARRAY map_values(map<K, V>)
```

Usage:

Returns an array of all the values in the map parameter.

Parameter description:

map: map-type data.

Return Value:

Returns the array type, enter null, and null.

Example:

```
select map_values(map('a',123,'b',456));
Results:
[123, 456]
```

## ARRAY {#section_zcz_4lb_wdb .section}

Function definition:

```
ARRAY array(value1,value2, ...)
```

Usage:

Creates an array using the given values.

Parameter description:

value: This parameter can be of any type, but all the values must be of the same type.

Return Value:

Returns the array type.

Example:

For example, the fields int\_table are \(c1 bigint,c2 string,c3 string, c4 bigint ,c5 bigint\), with the following data

```
+------------+----+----+------------+------------+
| c1         | c2 | c3 | c4         | c5         |
+------------+----+----+------------+------------+
| 1000       | k11 | k21 | 86         | 15         |
| 1001       | k12 | k22 | 97         | 2          |
| 1002       | k13 | k23 | 99         | 1          |
+------------+----+----+------------+------------+
```

Execute SQL:

```
select array(c2,c4,c3,c5) from t_table;
```

Results:

```
+------+
| _c0 |
+------+
| [k11, 86, k21, 15] |
| [k12, 97, k22, 2] |
| [k13, 99, k23, 1] |
+ ---- +
```

## SIZE {#section_nr5_vlb_wdb .section}

Function definition:

```
INT size(map)
INT size(array)
```

Usage:

-   `size(map<K，V>)` returns the number of K/V pairs in the given map.
-   `size(array<T>)` returns the number of elements in the given array.

Parameter description:

-   `map<K, V>`: Map-type data.
-   `array<T>`: Array-type data.

Return Value:

Returns the Int type.

Example:

```
select size(map('a',123,'b',456)) from dual;--Returns 2
select size(map('a',123,'b',456,'c',789)) from dual;--Returns 3
select size(array('a','b')) from dual;--Returns 2
select size(array(123,456,789)) from dual;--Returns 3
```

## ARRAY\_CONTAINS {#section_rk2_2mb_wdb .section}

**Function definition**:

```
boolean array_contains(ARRAY<T> a,value v)
```

Usage:

Checks if the given array a contains v.

Parameter description:

-   a: Array-type data.
-   v: The given v must be of the same type as the data in the array.

Return Value:

Returns the Boolean type.

Example:

If the field of t\_table\_array is \(`c1 bigint, t_array array<string>`\), the data is as follows:

```
+ ------------ + --------- +
| c1         | t_array |
+------------+---------+
| 1000       | [k11, 86, k21, 15] |
| 1001       | [k12, 97, k22, 2] |
| 1002       | [k13, 99, k23, 1] |
+------------+---------+
```

Execute SQL:

```
select c1, array_contains(t_array,'1') from t_table_array;
```

Results:

```
+------------+------+
| c1         | _c1  |
+------------+------+
| 1000       | false |
| 1001       | false |
| 1002       | true |
+------------+------+
```

## SORT\_ARRAY {#section_jmq_rmb_wdb .section}

Function definition:

```
ARRAY sort_array(ARRAY<T>)
```

Usage:

This function used to sorts the given array.

Parameter description:

`ARRAY<T>`: Array-type data, the data in the array can be of any type.

Return Value:

Returns the array type.

Example:

```
select sort_array(array('a','c','f','b')),sort_array(array(4,5,7,2,5,8)),sort_array(array('You','Me','He')) from dual;
Results:
[a, b, c, f] [2, 4, 5, 5, 7, 8] [He, You, Me]
```

Execute SQL

```
Select sort_array (C1), sort_array (C2), sort_array (C3) from t_array;
```

Return result:

```
[a, b, c, f] [2, 4, 5, 5, 7, 8] [He, You, Me]
```

## POSEXPLODE {#section_ilc_xmb_wdb .section}

Function definition:

```
posexplode(ARRAY<T>)
```

Usage:

Explodes the given array. Each value is given a row and each row has two columns corresponding to the subscript \(starting from 0\) and the array element.

Parameter description:

ARRAY: Array-type data, the data in the array can be of any type.

Return Value:

Returns the table generation function.

Example:

```
select posexplode(array('a','c','f','b')) from dual;
Results:
+------------+-----+
| pos | val |
+------------+-----+
| 0 | a |
| 1 | c |
| 2 | f |
| 3 | b |
+------------+-----+
```

## STRUCT {#section_ifb_2nb_wdb .section}

Function definition:

```
STRUCT struct(value1,value2, ...)
```

Usage:

Creates a struct using the given value list.

Parameter description:

value: Each value can be of any type.

Return Value:

Returns the `STRUCT<col1:T1, col2:T2, ... >`Type. field names are sequential: col1, col2, …

Example:

```
select struct('a',123,'ture',56.90) from dual;
Results:
{col1:a, col2:123, col3:ture, col4:56.9}
```

## NAMED\_STRUCT {#section_ecs_3nb_wdb .section}

Function definition:

```
STRUCT named_struct(string name1, T1 value1, string name2, T2 value2, ...)
```

Usage:

Creates a struct using the given name/value list.

Parameter description:

-   value: Each value can be of any type.
-   name: Specifies the name of a String-type field.

Return Value:

Returns the `STRUCT<name1:T1, name2:T2, ... >`type. The field names of the generated struct are sequential: name1, name2, …

Example:

```
select named_struct('user_id',10001,'user_name','LiLei','married','F','weight',63.50) from dual;
Results:
{user_id:10001, user_name:LiLei, married:F, weight:63.5}
```

## INLINE {#section_w3l_pnb_wdb .section}

Command Format:

```
inline(array<struct<f1:T1, f2:T2, ... >>)
```

as shown in the following figure:

Explodes the given struct array. Each element is given one row and each struct element corresponds to one column in each row.

Parameter description:

`STRUCT<f1:T1, f2:T2, ... >`: The values in the array can be of any type.

Return Value:

Returns the table generation function.

Example:

If the field in Table t\_table is \(`t_struct struct<user_id:bigint,user_name:string,married:string,weight:double>` <user\_id: bigint,="" user\_name:="" string,="" married:="" weight:="" double=""\>\), the table data is as follows:</user\_id:\>

```
+----------+
| T_struct |
+----------+
{user_id:10001, user_name:LiLei, married:F, weight:63.5}
{user_id:10001, user_name:LiLei, married:F, weight:63.5}
+----------+
```

Execute SQL:

```
select inline(array(t_struct)) from t_table;
```

Return result:

```
+------------+----+----+------------+------------+
| user_id    | user_name | married | weight     |
+------------+----+----+------------+------------+
| 10001      | LiLei     | N       | 63.5       |
| 10002      | HanMeiMei | Y       | 43.5       |
+------------+-----------+---------+------------+
```

## TRANS\_ARRAY {#section_y21_vnb_wdb .section}

Function definition:

```
trans_array (num_keys, separator, key1,key2,…,col1, col2,col3) as (key1,key2,…,col1, col2)
```

Usage:

A UDTF that converts one row of data to multiple rows, and converts an array separated with fixed-separator format in column into multiple rows.

Parameter description:

-   num\_keys: Bigint type constant, must be larger than or equal to 0. It is used as the number of columns to transpose key when converting to multiple rows.
-   Key: Duplicate columns in multiple rows when converting one row to multiple rows.
-   separator: String type constant. It is a separator used to split a string into multiple elements. Exception is thrown when it is null.
-   keys: As column of key when you transpose. It is specified by num\_keys. If num\_keys specifies that all columns are keys \(that is, num\_keys equals the number of all columns\), only one row is returned.
-   cols: An array to convert to rows. All columns after keys are considered as an array to be transposed. String type. The stored contents are arrays of string format, such as “Hangzhou; Beijing; shanghai”, they are arrays separated by “;”.

Return Value:

Transposed rows, new column names are specified by as. The type of column that is as key remains unchanged, and all other columns are String type. The number of rows to be split depends on the array that has maximum number, no-value locales are complemented with NULL.

**Note:** 

The following restrictions apply when using UDTF:

-   All columns that are considered as keys must be placed front, and columns to be transposed must be placed behind.
-   One select can only have one UDTF and no other columns can appear.
-   One select can only have one UDTF and no other columns can appear.

Example:

The data in the t\_table table is as follows:

```
+----------+----------+------------+
| login_id | login_ip | login_time |
+----------+----------+------------+
wangwangA 192.168.0.1,192.168.0.2 20120101010000,20120102010000
| Wangwangb | 192.168.25.10, 192.168.67.22, 192,168.6. 3 | maid, 20120223080000 |
+----------+----------+------------+
```

Execute SQL:

```
trans_array(1, ",", login_id, login_ip, login_time) as (login_id,login_ip,login_time)
```

Results:

```
+----------+----------+------------+
| Login_id | login_ip | login_time |
+----------+----------+------------+
| wangwangB | 192.168.45.10 | 20120111010000 |
| wangwangB | 192.168.67.22 | 20120112010000 |
| wangwangB | 192.168.6.3 | 20120223080000 |
| wangwangA | 192.168.0.1 | 20120101010000 |
| wangwangA | 192.168.0.2 | 20120102010000 |
+----------+----------+------------+
```

If the table contains the following data:

```
Login_id LOGIN_IP LOGIN_TIME 
wangwangA 192.168.0.1,192.168.0.2 20120101010000
```

NULL is complemented to the no-value locales in the array:

```
Login_id Login_ip Login_time 
wangwangA 192.168.0.1 20120101010000
wangwangA 192.168.0.2 NULL
```

