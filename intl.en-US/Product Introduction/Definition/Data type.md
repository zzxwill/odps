# Data type {#concept_1jhp_4bb_5db .concept}

## Basic data types {#section_b53_rbb_5db .section}

supported by MaxCompute2.0 are listed in the following table. Columns in a MaxCompute table must be any of the listed types. New types include TINYINT, SMALLINT, INT,  FLOAT, VARCHAR, TIMESTAMP, and BINARY data type.

**Note:** 

If data type such as TINYINT, SMALLINT,  INT, FLOAT, VARCHAR, TIMESTAMP, or BINARY are involved when running an SQL command, the set command `set  odps.sql.type.system.odps2=true;` must be added before the SQL command.

The set command and SQL command are then submitted simultaneously. If INT type is involved, 

and the set command is not added, the INT type is converted to BIGINT, which is 64 bits.

|Type|New|Constant|Description|
|:---|:--|:-------|:----------|
|TINYINT|Yes|1Y，-127Y|8-bit signed integer, range -128 to 127|
|SMALLINT|Yes|32767S, -100S| 16-bit signed integer, range -32768 to 32767|
|INT|Yes|1000,-15645787 \(Note1\)|32-bit signed integer-231 to 231 - 1|
|BIGINT|No|100000000000L, -1L|64-bit signed integer, range -263 + 1 to 263 - 1|
|FLOAT|Yes|None|32-bit binary floating point|
|DOUBLE|No|3.1415926 1E+7|64-bit binary floating point|
|DECIMAL|No|3.5BD， 99999999999.9999999BD|10-in-order exact numeric type, Plastic Part range-1036 + 1 to 1036-1, fractional portion accurate to 10-18|
|VARCHAR|Yes|None \(Note2\)|Variable-length character type, n is the length, and the range is 1 to 65535.|
|STRING|No|“abc”,’bcd’,”alibaba” ‘inc’ \(Note3\)|A single string length can be up to 8M|
|BINARY|Yes|None|Binary data type, a single string length can be up to 8M|
|DATETIME|No|DATETIME ‘2017-11-11 00:00:00’|0001-01-01 00:00:00 ~ 9999-12-31 23:59:59, Date type, use UTC+8 as the standard time system|
|TIMESTAMP|Yes|TIMESTAMP ‘2017-11-11 00:00:00.123456789’|It is independent of the time zone and ranges from January 1st 0000 to December 31, 9999 23.59:59.999999999,  and is accurate to nanosecond-level.|
|BOOLEAN|No|TRUE，FALSE|True/False, Boolean type|

All data types in the preceding table can be NULL.

**Note:** 

-   NOTE 1: For INT constant, if the range of INT is exceeded, INT is converted into BIGINT;  if the range of BIGINT is exceeded, it is converted into DOUBLE. 

    In MaxCompute versions earlier than 2.0, all INT types in SQL script are converted to BIGINT , for example:

    ```
    create table a_bigint_table(a int); 
    select cast(id as int) from mytable;
    ```

    To be compatible with earlier MaxCompute versions, MaxCompute 2.0 retains this conversion without setting odps.sql.type.system.odps2 as true, however, a warning is triggered when INT is treated as BIGINT. In this case, we recommend that you change an Int to a Bigint to avoid confusion.

-   NOTE 2: VARCHAR constants can be expressed by STRING constants of implicit transformation.
-   NOTE 3: STRING constants support connections, for example, `abc` `xyz` is parsed as `abcxyz`, and different parts can be written on different lines.

## Complex Data Types {#section_r53_rbb_5db .section}

Complex data types that MaxCompute supports are listed in the following table.

**Note:** If a complex data type  is involved when you run a SQL command, the set command `set  odps.sql.type.system.odps2=true;` must be added before the SQL command. The set command and SQL command are then submitted simultaneously.

|Type|Definition method|Construction method|
|:---|:----------------|:------------------|
|ARRAY|array< int \>; array< struct< a:int, b:string \>\>|array\(1, 2, 3\); array\(array\(1, 2\); array\(3, 4\)\)|
|MAP|map< string, string \>; map< smallint, array<  string\>\>|map\(“k1”, “v1”, “k2”, “v2”\); map\(1S, array\(‘a’, ‘b’\), 2S,  array\(‘x’, ‘y\)\)|
|STRUCT|struct< x:int, y:int\>; struct< field1:bigint,  field2:array< int\>, field3:map< int, int\>\>|named\_struct\(‘x’, 1, ‘y’, 2\); named\_struct\(‘field1’, 100L,  ‘field2’, array\(1, 2\), ‘field3’, map\(1, 100, 2, 200\)|

