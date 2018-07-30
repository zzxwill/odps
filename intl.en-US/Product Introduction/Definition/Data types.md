# Data types {#concept_jhp_4bb_5db .concept}

## Basic data types {#section_b53_rbb_5db .section}

supported by MaxCompute2.0 are listed in the following table. Columns in a MaxCompute table must be any of the listed types. New types include TINYINT, SMALLINT, INT,  FLOAT, VARCHAR, TIMESTAMP, and BINARY data type.The details are as follows:

**Note:** If data type such as TINYINT, SMALLINT,  INT, FLOAT, VARCHAR, TIMESTAMP, or BINARY are involved when running an SQL command, the set command `set odps.sql.type.system.odps2=true;` must be added before the SQL command. 

The set command and SQL command are then submitted simultaneously. If INT type is involved, 

and the set command is not added, the INT type is converted to BIGINT, which is 64 bits.

|Type|New|Constant|Description|
|:---|:--|:-------|:----------|
|TINYINT|Yes |1Y，-127Y|8-bit signed integer, range -128 to 127|
|SMALLINT|Yes |32767S, -100S| 16-bit signed integer, range -32768 to 32767|
|INT|Yes |1000,-15645787 \(Note1\)|32 bit signed shaping, the range is \(-2\) of the 31 power to 2 of the 31 power minus 1.|
|BIGINT|No|100000000000L, -1L|64 bit signed shaping, the range is \(-2\) of the 63 power to 2 of the 63 power minus 1.|
|FLOAT|Yes |None|32-bit binary floating point|
|DOUBLE|No|3.1415926 1E+7|64-bit binary floating point|
|DECIMAL|No|3.5BD， 99999999999.9999999BD|Decimal precision number type, shaping part range \(-10\)∧36 plus 1 to 10∧36 reduce 1, decimal part accurate to 10∧\(-18\)|
|VARCHAR|Yes |None \(Note2\)|Variable-length character type, n is the length, and the range is 1 to 65535.|
|STRING|No|“abc”,’bcd’,”alibaba” ‘inc’ \(Note3\)|A single string length can be up to 8M|
|BINARY|Yes |None|Binary data type, a single string length can be up to 8M|
|DATETIME|No|DATETIME ‘2017-11-11 00:00:00’|Date-time type, range from December 31, 999 to January 1-9, 0000, exact to milliseconds \(note 4\)|
|TIMESTAMP|Yes |TIMESTAMP ‘2017-11-11 00:00:00.123456789’|It is independent of the time zone and ranges from January 1st 0000 to December 31, 9999 23.59:59.999999999,  and is accurate to nanosecond-level.|
|BOOLEAN|No|TRUE，FALSE|True/False, Boolean type|

All data types in the preceding table can be NULL.

**Note:** 

-   NOTE 1: For INT constant, if the range of INT is exceeded, INT is converted into BIGINT;  if the range of BIGINT is exceeded, it is converted into DOUBLE. 

    In MaxCompute versions earlier than 2.0, all INT types in SQL script are converted to BIGINT , for example:

    ```
    create table a_bigint_table(a int); -- the int here is actually treated as a bigint
    select cast(id as int) from mytable; -- the int here is actually treated as a bigint
    ```

    To be compatible with earlier MaxCompute versions, MaxCompute 2.0 retains this conversion without setting odps.sql.type.system.odps2 as True. However, a warning is triggered when INT is treated as BIGINT. In this case, we recommend that you change an Int to a Bigint to avoid confusion.

-   NOTE 2: VARCHAR constants can be expressed by STRING constants of implicit transformation.
-   NOTE 3: STRING constants support connections, for example, `abc``xyz` is parsed as `abcxyz`, and different parts can be written on different lines.
-   NOTE 4: The time value displayed by the current query does not contain milliseconds. The tunnel command specifies the time format through`-dfp`, and can be specified in milliseconds, such as`tunnel upload -dfp 'yyyy-MM-dd HH:mm:ss.SSS'`, for more information about tunnel commands, refer to[Tunnel commands](../../../../intl.en-US/User Guide/Data upload and download/Tunnel commands.md#).

MaxCompute2.0 supports the complex data types listed in the following table.

**Note:** If a complex data type is involved when you run an SQL command, the set command `set  odps.sql.type.system.odps2=true;` must be added before the SQL command. The set command and SQL command are then submitted simultaneously.

|Type|Definition method|Construction method|
|:---|:----------------|:------------------|
|ARRAY|array< int \>;array< struct< a:int, b:string \>\>|array\(1, 2, 3\); array\(array\(1, 2\); array\(3, 4\)\)|
|MAP| map< string, string \>;map< smallint, array< string\>\>|map\(“k1”, “v1”, “k2”, “v2”\); map\(1S, array\(‘a’, ‘b’\), 2S,  array\(‘x’, ‘y\)\)|
|STRUCT| struct< x:int, y:int\>;struct< field1:bigint, field2:array< int\>, field3:map< int, int\>\>|named\_struct\(‘x’, 1, ‘y’, 2\); named\_struct\(‘field1’, 100L,  ‘field2’, array\(1, 2\), ‘field3’, map\(1, 100, 2, 200\)|

