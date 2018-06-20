# UDF Summary {#concept_dkq_tn2_vdb .concept}

MaxCompute provides many built-in functions to meet the computing requests of a user and a user can also create user-defined functions to meet different computing needs. A User Defined Function \(UDF\) is similar to an ordinary Built-in Function. MaxCompute  provides many built-in functions to meet the computing requests of you and you can also create user-defined functions to meet different computing needs.  UDF is similar to an ordinary  [Built-in Function](intl.en-US/User Guide/SQL/Builtin Function/Mathematical Functions.md) .

If you use [Maven](http://search.maven.org/) to search “odps-sdk-udf” from [Maven](http://search.maven.org/) to get different versions of Java  SDK. The configuration information is shown as follows:

```
<dependency>
    <groupId>com.aliyun.odps</groupId>
    <artifactId>odps-sdk-udf</artifactId>
   <version>0.20.7-public</version>
</dependency>
```

In MaxCompute, you can expand two kinds of UDF:

|UDF Class|Description|
|:--------|:----------|
| UDF\(User Defined Scalar Function\)|User Defined Scalar Function. The relationship between input and output is a one-to-one relationship. Read a row data and write an output value.|
|UDTF \(User-defined table valued function\)|User-defined table valued functions are used in scenarios where the calling of one function leads to multiple rows of data being output. It is a unique user-defined function which can return multiple fields, while UDF  can only output a return value.|
|UDAF（User Defined Aggregation Function）|User Defined Aggregation Function \(UDAF\), the relationship between its input and output is one-to-many relationships. That is to aggregate multiple input records to an output value.  It can be used with Group By  clause together.  For more information, see [Aggregation Functions](intl.en-US/User Guide/SQL/Builtin Function/Aggregate function.md).|

**Note:** 

-   UDF stands for the set of use-defined functions, including User Defined Scalar Function, User Defined Aggregation Function and User Defined Table Valued Function.  In a narrower sense, it represents user User Defined Scalar Function.  The document uses this term frequently and the readers can judge the specific meaning according to the context .
-   If the system prompts that memory is not enough with an UDF involved in the SQL statement,  configure `set  odps.sql.udf.joiner.jvm.memory=xxxx;` to solve it. The reason is that the amount of data is too large and there is a data skew, so that the memory size occupied by task exceeds the default memory size.

MaxCompute UDF supports cross-project sharing. A UDF in project\_b can be used in project\_a.  For more information about authorization, see Authorization in Security Guide documentation. other\_project:udf\_in\_other\_project\(arg0, arg1\) as res from table\_t;。

## UDF Examples {#section_qt4_kdf_vdb .section}

Please see [UDF Example](../../../../intl.en-US/Quick Start/Java UDF Development.md) in Quick Start Volume.

