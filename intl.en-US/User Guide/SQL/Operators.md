# Operators {#concept_bll_pfl_vdb .concept}

## Relational Operators {#section_dlk_lgl_vdb .section}

|Operator|Description|
|:-------|:----------|
|A = B|If A or B is NULL, NULL is returned. If A is equal to B, TRUE is returned. Otherwise, FALSE is returned.|
|A<\>B| If expression A or expression B is NULL, return NULL. TRUE if expression A is not equal to expression B otherwise FALSE.|
|A<B|If A or B is NULL, NULL is returned. If A is less than B, TRUE is returned. Otherwise, FALSE is returned.|
|A<=B|If A or B is NULL, NULL is returned. If A is not greater than B, TRUE is returned. Otherwise, FALSE is returned.|
|A \> B|If A or B is NULL, NULL is returned. If A is greater than B, TRUE is returned. Otherwise, FALSE is returned.|
|A \>= B|If A or B is NULL, NULL is returned; if A is not less than B, TRUE is returned; otherwise, FALSE is returned.|
|A IS NULL|If A is NULL, TRUE is returned. Otherwise, FALSE is returned.|
|A IS NOT NULL|If expression A is NULL, return TRUE. Otherwise, return FALSE.|
|A LIKE B|If expression A or expression B is NULL, return NULL. TRUE if String A matches the SQL simple regular expression B, otherwise FALSE.  The \(\_\) character in B matches any character in A, and the \(%\) character in B matches an arbitrary number of characters in A \(similar to .\* in posix regular expressions\).  To match \(%\) or \(\_\), use by the escape characters \(\\%\) and \(\\\_\).```
‘aaa’ like‘a__’= TRUE 
‘aaa’ like‘a%’ = TRUE
‘aaa’ like‘aab’= FALSE 
 ‘a%b’ like ‘a\%b’ = TRUE 
 ‘axb’ like ‘a\%b’ = FALSE               
```

|
|A RLIKE B|A is a string, and B is a string constant regular expression. TRUE if any substring of A matches the Java regular expression B, otherwise FALSE. If expression B is empty, report error and exit. NULL if expression A or B is NULL.|
|A IN B|B is a set. If expression A is NULL, return NULL. If expression A is in expression B, return TRUE, otherwise return FALSE. If expression B has only one element NULL, that is, A IN \(NULL\), return NULL. If expression B contains NULL element, take NULL as the type of other elements in B set. B must be a constant and at least has one element; all types must be consistent.|
|BETWEEN AND|The expression is `A [NOT] BETWEEN B AND C`. Empty if A, B, or C is empty. True if A is larger than or equal to B and less than or equal to C; otherwise false.|

The common usage:

```
select * from user where user_id = '0001'; 
select * from user where user_name <> 'maggie'; 
select * from user where age > ‘50’; 
select * from user where birth_day >= '1980-01-01 00:00:00'; 
select * from user where is_female is null; 
select * from user where is_female is not null; 
select * from user where user_id in (0001,0010); 
select * from user where user_name like 'M%';
```

The Double values in MaxCompute are different in precision. For this reason, it is not recommended to use the equal sign \(=\) for comparison between two Double data.  You can subtract two Double types, and then take the absolute value for determination.  When the absolute value is small enough, the two double values are considered equal. For example:

```
abs(0.9999999999 - 1.0000000000) < 0.000000001
 -- 0.9999999999 and 1.0000000000 have the precision of 10 decimal digits, while 0.000000001 has the precision of 9 decimal digits.
 -- It is considered that 0.9999999999 is equal to 1.0000000000.
```

**Note:** 

-   ABS is a built-in function provided by MaxCompute to take absolute value. See [ABS](intl.en-US/User Guide/SQL/Builtin Function/Mathematical Functions.md) for more information.
-   In general, the Double type in MaxCompute can retain 14 valid digits.

## Arithmetic Operators {#section_ycc_chl_vdb .section}

|Operator|Description|
|:-------|:----------|
|A + B|If expression A or B is NULL, return NULL; otherwise return the result of adding A and B.|
|A – B|If expression A or B is NULL, return NULL; otherwise return the result of subtracting B from A.|
|A \* B|If expression A or B is NULL, return NULL; otherwise return the result of multiplying A and B.|
|A / B| If expression A or B is NULL, return NULL; otherwise return the result of dividing B from A.  If Expression A and B are bigint types, the result is double type.|
|A % B|If expression A or B is NULL, return NULL; otherwise return the reminder resulting from dividing A by B.|
|+A| Still return A.|
|–A|If expression A is NULL, return NULL; otherwise return –A.|

The common usage:

```
select age+10, age-10, age%10, -age, age*age, age/10 from user;
```

**Note:** 

-   Only String, Bigint, and Double could be involved in arithmetic operation, Datetime type and Boolean type are not allowed participating in arithmetic operation.
-   Before involved in operation, the type String will be converted to Double by implicit type conversion.
-   If Bigint and Double are both involved in arithmetic operation, the type Bigint will be converted to Double by implicit type conversion.
-   When A and B are Bigint types, the return result of A/B will be a Double type. For other arithmetic operations, the return value is also a Bigint type.

## Bitwise Operators { .section}

|Operator|Description|
|:-------|:----------|
|A & B|Return the result of bitwise AND of A and B. For example: 1&2, return 0; 1&3, return 1; Bitwise AND of NULL and other values, all return NULL. Expression A and B must be Bigint.|
|A | B|Return the result of bitwise OR of A and B. For example: 1 | 2, return3. 1 | 3, return 3. Bitwise OR of NULL and other values, all return NULL.  A and B Expression A and B must be Bigint.|

**Note:** Bitwise operator does not support implicit conversion, only supports the type Bigint.

## Logical Operators { .section}

```
    Operator  Description
    A and B TRUE and TRUE=TRUE
                    TRUE and FALSE=FALSE
                    FALSE and TRUE=FALSE
                    FALSE and NULL=FALSE
                    NULL and FALSE=FALSE
                    TRUE and NULL=NULL
                    NULL and TRUE=NULL
                    NULL and NULL=NULL
    A or B TRUE or TRUE=TRUE
                    TRUE or FALSE=TRUE
                    FALSE or TRUE=TRUE
                    FALSE or NULL=NULL
                    NULL or FALSE=NULL
                    TRUE or NULL=TRUE
                    NULL or TRUE=TRUE
                    NULL or NULL = NULL
    NOT A If A is NULL, NULL is returned.
                    If A is TRUE, FALSE is returned.
                    If A is FALSE, TRUE is returned.
```

**Note:** Only the type Boolean can be involved in logic operation and the implicit type conversion is not supported.

