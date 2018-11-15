# Data types {#concept_jhp_4bb_5db .concept}

## Basic data types {#section_b53_rbb_5db .section}

supported by MaxCompute2.0 are listed in the following table. Columns in a MaxCompute table must be any of the listed types. New types include TINYINT, SMALLINT, INT,  FLOAT, VARCHAR, TIMESTAMP, and BINARY data type.The details are as follows:

**Note:** At present, the Set commands supported by MaxCompute SQL and new version Mapreduce are divided into two ways:

-   Session Level: To use the new data types \(Tinyint, Smallint, Int, Float, Varchar, TIMESTAMP BINARY\), add a set statement before the table statement

    ```
    set odps.sql.type.system.odps2=true;
    ```

    And submit the execution together with the table statement.

-   Project level: that is to support new types of project level open. The Owner of project can set up project as required.

    ```
    setproject odps.sql.type.system.odps2=true;
    ```

    For a detailed description of setproject, please see:[Other operations](../../../../reseller.en-US/User Guide/Common commands/Other operations.md#).

-   When it comes to INT types, 32 bits are added to the set statement and converted to BIGINT, 64 bits, if not added.
-   SDK 0.27.2-public version and above, Client 0.27.0 version and above support new data type.

Open new type`odps.sql.type.system.odps2`of influence.

-   Implicit type conversion rules change.

    Possible compatibility issues: Some implicit type conversions under the new type system will be disabled, including string - \> bigint, string - datetime, double - \> bigint, decimal - \> double, decimal - \> bigint are all at risk of precision loss or error. This situation can be solved by cast transformation.

-   Support operations, built-in functions, UDF is not the same.

    Some operations and built-in functions that take new types as parameters and return values are not available without opening new types, including UDF, which overloads new types as parameters and return values. Possible compatibility issues: For example, UDF contains two overloads: bigint and int. The old type must be the overload of bigint, while the new type may be resolved to the overload of int.

-   The type of constant will change.

    A single shaping constant, such as 123, is bigint type under the old type, and int type under the new type. Possible compatibility issues: Type int leads to inconsistencies in function prototypes invoked by subsequent operations, including new type tables generated after dropping \(i.e., writing to disk\) that lead to changes in the behavior of peripheral tools and subsequent jobs.

-   The resolution of the bigint keyword is different.

    In the old type system, the int keyword is treated as bigint, while the new type system is treated as real int. Possible compatibility issues: Type int leads to inconsistencies in function prototypes invoked by subsequent operations, including new type tables generated after dropping \(i.e., writing to disk\) that lead to changes in the behavior of peripheral tools and subsequent jobs.


For a detailed description of setproject, please see:[Other operations](../../../../reseller.en-US/User Guide/Common commands/Other operations.md#)

.

MR type tasks do not support new data types for the time being.

|Type|New|Constant|Description|
|:---|:--|:-------|:----------|
|TINYINT|Yes |1Y，-127Y|8-bit signed integer, range -128 to 127|
|SMALLINT|Yes |32767S, -100S| 16-bit signed integer, range -32768 to 32767|
|INT|Yes |1000,-15645787 \(Note2\)|32 bit signed shaping, the range is -231 to 231 - 1.|
|BIGINT|No|100000000000L, -1L|64 bit signed shaping, the range is -263 + 1 to 263 - 1.|
|FLOAT|Yes |None|32-bit binary floating point|
|DOUBLE|No|3.1415926 1E+7|64-bit binary floating point|
|DECIMAL|No|3.5BD， 99999999999.9999999BD\(Note1\)|Decimal precision number type, shaping part range -1036 + 1 to 1036 - 1, decimal part accurate to 10-18|
|VARCHAR|Yes |None \(Note3\)|Variable-length character type, n is the length, and the range is 1 to 65535.|
|STRING|No|“abc”,’bcd’,”alibaba” ‘inc’ \(Note4\)|A single string length can be up to 8M|
|BINARY|Yes |None|Binary data type, a single string length can be up to 8M|
|DATETIME|No|DATETIME ‘2017-11-11 00:00:00’|Date-time type, range from December 31, 999 to January 1-9, 0000, exact to milliseconds \(note 5\)|
|TIMESTAMP|Yes |TIMESTAMP ‘2017-11-11 00:00:00.123456789’|It is independent of the time zone and ranges from January 1st 0000 to December 31, 9999 23.59:59.999999999,  and is accurate to nanosecond-level.|
|BOOLEAN|No|TRUE，FALSE|True/False, Boolean type|

All data types in the preceding table can be NULL.

**Note:** 

-   NOTE 1:When insert is constant to decimal field, pay attention to the writing of constants. For example,3.5BD in the definition of constants.

    ```
    insert into test_tb(a) values (3.5BD);
    ```

    The a field is decimal type.

-   NOTE 2: For INT constant, if the range of INT is exceeded, INT is converted into BIGINT;  if the range of BIGINT is exceeded, it is converted into DOUBLE. 

    In MaxCompute versions earlier than 2.0, all INT types in SQL script are converted to BIGINT , for example:

    ```
    create table a_bigint_table(a int); -- the int here is actually treated as a bigint
    select cast(id as int) from mytable; -- the int here is actually treated as a bigint
    ```

    To be compatible with earlier MaxCompute versions, MaxCompute 2.0 retains this conversion without setting odps.sql.type.system.odps2 as True. However, a warning is triggered when INT is treated as BIGINT. In this case, we recommend that you change an Int to a Bigint to avoid confusion.

-   NOTE 3: VARCHAR constants can be expressed by STRING constants of implicit transformation.
-   NOTE 4: STRING constants support connections, for example, `abc``xyz` is parsed as `abcxyz`, and different parts can be written on different lines.
-   NOTE 5: The time value displayed by the current query does not contain milliseconds. The tunnel command specifies the time format through`-dfp`, and can be specified in milliseconds, such as`tunnel upload -dfp 'yyyy-MM-dd HH:mm:ss.SSS'`, for more information about tunnel commands, refer to[Tunnel commands](../../../../reseller.en-US/User Guide/Data upload and download/Tunnel commands.md#).

MaxCompute2.0 supports the complex data types listed in the following table.

|Type|Definition method|Construction method|
|:---|:----------------|:------------------|
|ARRAY|array< int \>;array< struct< a:int, b:string \>\>|array\(1, 2, 3\); array\(array\(1, 2\); array\(3, 4\)\)|
|MAP| map< string, string \>;map< smallint, array< string\>\>|map\(“k1”, “v1”, “k2”, “v2”\); map\(1S, array\(‘a’, ‘b’\), 2S,  array\(‘x’, ‘y\)\)|
|STRUCT| struct< x:int, y:int\>;struct< field1:bigint, field2:array< int\>, field3:map< int, int\>\>|named\_struct\(‘x’, 1, ‘y’, 2\); named\_struct\(‘field1’, 100L,  ‘field2’, array\(1, 2\), ‘field3’, map\(1, 100, 2, 200\)|

**Note:** At present, the Set commands supported by MaxCompute SQL and new version Mapreduce are divided into two ways:

-   Session Level: To use the new data types \(Tinyint, Smallint, Int, Float, Varchar, TIMESTAMP BINARY\), add a set statement before the table statement

    ```
    set odps.sql.type.system.odps2=true;
    ```

    And submit the execution together with the table statement.

-   Project level: that is to support new types of project level open. The Owner of project can set up project as required.

    ```
    setproject odps.sql.type.system.odps2=true;
    ```

    For a detailed description of setproject, please see:[Other operations](../../../../reseller.en-US/User Guide/Common commands/Other operations.md#).


