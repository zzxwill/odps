# UDT {#concept_drb_xk4_hfb .concept}

MaxCompute has introduced the User-defined type \(UDT\) based on the new generation SQL engine. UDT allow you to reference classes or objects of third-party languages in SQL statements to obtain data or call methods.

## Scenarios {#section_wbd_xp4_hfb .section}

UDT are typically applied in the following scenarios:

-   Scenario 1: MaxCompute does not have built-in functions to complete tasks that can be easily performed using other languages. For example, some tasks can be performed by calling built-in Java classes only once. The procedure of using user defined functions \(UDFs\) to complete these tasks is complex.
-   Scenario 2: You need to call a third-party library in SQL statements to implement the corresponding function. You want to use a function provided by a third-party library directly in a SQL statement, instead of wrapping the function inside a UDF.
-   Scenario 3: Select Transform allows you to include objects and classes in SQL statements to make these SQL statements easier to read and maintain. For some languages, such as Java, the source code can be executed only after it is compiled. You want to reference objects and classes of these languages in SQL statements.

## Overview {#section_drd_zp4_hfb .section}

UDT allow you to reference classes or objects of third-party languages in SQL statements to obtain data or call methods.

There are major differences between UDT supported by MaxCompute and other SQL engines. UDT supported by other SQL engines are similar to the struct composite type in MaxCompute. Some proprietary SQL languages provide features that allow you to call third-party libraries, such as the CREATE TYPE statement in Oracle databases. UDT supported by MaxCompute is similar to the CREATE TYPE statement. A UDT contains both fields and methods. In addition, MaxCompute does not require that you use Data Definition Language \(DDL\) statements to define type mappings. Instead, MaxCompute allows you to reference types directly in SQL statements.

Example:

```
set odps.sql.type.system.odps2=true;    
-- Enable new data type support in MaxCompute. The following example will use a new type of Integer (int).
SELECT java.lang.Integer.MAX_VALUE;
```

The output is as follows:

```
+-----------+
| max_value |
+-----------+
| 2147483647 |
+-----------+
```

You can shorten the statement by removing java.lang in the same way as in Java:

```
set odps.sql.type.system.odps2=true;
SELECT Integer.MAX_VALUE;
```

The expression in the preceding SELECT statement is similar to a Java expression and is executed in the same way as in Java. The expression specifies a UDT in MaxCompute.

You can use UDFs to implement all functions provided by UDT. If you use a UDF to implement the same function, you need to follow these steps:

1.  Define a UDF class.

    ```
    package com.aliyun.odps.test;
    public class IntegerMaxValue extends com.aliyun.odps.udf.UDF {
    public Integer evaluate() {
     return Integer.MAX_VALUE;
    } 
    }
    ```

2.  Compile the UDF and pack it into a JAR package. Upload the JAR package and create a function.

    ```
    add jar odps-test.jar;
    create function integer_max_value as 'com.aliyun.odps.test.IntegerMaxValue' using 'odps-test.jar';
    ```

3.  Call the function in a SQL statement.

    ```
    select integer_max_value();
    ```

    Using a UDT simplifies this procedure. By using UDT, you can use external functions provided by other languages in SQL statements more easily.


## How UDT work {#section_hgw_cn4_hfb .section}

The example shows how to use UDT to access Java static fields. You can use UDT to implement more functions. The following example shows the UDT execution procedure and its functions.

```
--  Sample data
@table1 := select * from values ('100000000000000000000') as t(x);
@table2 := select * from values (100L) as t(y);
--  Logic of the code
@a := select new java.math.BigInteger(x) x from @table1;          -- Create a new object
@b := select java.math.BigInteger.valueOf(y) y from @table2;      -- Call a static method.
select /*+mapjoin(b)*/ x.add(y).toString() from @a a join @b b;   -- Call an instance method
```

The output is follows:

```
100000000000000000100
```

This example also shows how to retrieve UDT columns using subqueries, which is difficult to complete using UDFs. The x column retrieved by variable a is java.math.BigInteger type. It is not a built-in type. You can pass the UDT data to another operator and then call its method. You can also use the UDT data in data shuffling.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/22183/154297270313239_en-US.png)

This figure shows that a UDT has three stages: M1, R2, and J3. When a Join clause is used, data reshuffling is required, which is the same as MapReduce. Data is processed at multiple stages. Typically, data processing at different stages are performed in different processes or different physical machines.

Only the new java.math.BigInteger\(x\) method is called at the M1 stage.

The java.math.BigInteger.valueOf\(y\) and x.add\(y\).toString\(\) methods are called separately at the J3 stage. These methods are called at different stages in different processes or physical machines. The UDT encapsulates these stages and acts as a JVM.

Features

-   UDT currently only support Java. Other languages will be supported in later versions.
-   UDT also allow you to upload JAR packages and directly reference the packages. UDT have provided flags.
    -   You can use set odps.sql.session.resources to specify one or more resources that you need to reference and separate the resources with commas \(,\). Example: `set odps.sql.session.resources=foo.sh,bar.txt;`.

        **Note:** This flag is the same as the resource setting flag in Select Transform. Therefore, this flag controls two functions. For example, you can use a UDT to reference the UDF JAR package that we have mentioned in the Overview section.

        ```
        set odps.sql.type.system.odps2=true;
        set odps.sql.session.resources=odps-test.jar; 
        --To reference the JAR package, you must first upload the package to the corresponding project and make sure that it is a JAR type resource.
        select new com.aliyun.odps.test.IntegerMaxValue().evaluate();
        ```

    -   You can use odps.sql.session.java.imports to specify one or more default JAR packages separated with commas \(,\). It is similar to the Java import statement. You can specify a class path, such as `java.math.BigInteger`, or use `*`. Currently, `static import` is not supported.

        For example, you can use a UDT to reference the UDF JAR package that we have mentioned in Overview.

        ```
        set odps.sql.type.system.odps2=true;
        set odps.sql.session.resources=odps-test.jar;
        set odps.sql.session.java.imports=com.aliyun.odps.test. *;  
        -- Specify the default JAR package.
        select new IntegerMaxValue().evaluate();
        ```

-   UDT supports the following operations:

    -   Instantiate objects using the new operator.
    -   Instantiate arrays using the new operator, including ArrayList initialization. Example: new Integer\[\] \{ 1, 2, 3 \}.
    -   Call methods, including static methods. You can create objects in the factory pattern.
    -   Access fields, including static fields.
    **Note:** 

    -   Only public methods and public fields are supported.
    -   Identifiers in UDT include package names, class names, method names, and field names. All identifiers are case-sensitive.
    -   UDT support type conversion and SQL syntax, for example, cast \(1 as java.lang.Object\). UDT do not support type conversion using Java syntax, for example, \(Object\)1.
    -   Anonymous classes and lambda expressions are not supported. They may be supported in later versions.
    -   UDT are typically used in expressions. Functions that do not return values cannot be called in expressions. Therefore, UDT currently do not support calling functions that do not return values. This issue will be resolved in a later version.
-   By default, you can reference all classes provided by Java SDK.

    **Note:** The runtime currently uses JDK1.8. Later versions may be not supported.

-   All operators use the MaxCompute SQL semantics. The result of `String.valueOf(1) + String.valueOf(2)` is 3. The two strings are implicitly converted to double type values and summed. If you use Java string concatenation to merge the strings, the result will be 12.

    In addition to the string concatenation methods in MaxCompute and Java, you may also have confusion about the equal \(=\) operator. The equal \(=\) operator in SQL statements is used as a comparison operator. To compare two Java objects, you must call the equals method. You cannot use the equal \(=\) operator to verify whether two objects are equal. When UDT are used, you cannot guarantee that one object is equal to another object. For more information, see the following descriptions.

-   Java data types are mapped to built-in data types. For more information, see the data type mapping table in [Java UDFs](intl.en-US/User Guide/SQL/UDF/Java UDF.md#). The mapping table can be applied to UDT.

    -   Built-in type data can directly call the method of the Java type to which the built-in type is mapped. Example: `'123'.length() , 1L.hashCode()`.
    -   UDT can be used in built-in functions and UDFs. For example, in `chr(Long.valueOf('100'))`, `Long.valueOf` returns a `java.lang.Long` type value. Built-in function chr supports the built-in type of BIGINT.
    -   Java primitive type data is automatically converted to boxing type data and the preceding two rules can be applied in this situation.
    **Note:** For certain built-in [new data types](../../../../intl.en-US/User Guide/Definition/Data types.md#), you must add the `set odps.sql.type.system.odps2=true;` statement to declare these types. Otherwise, an error occurs.

-   UDT completely support Java generics. For example, based on the parameter type, the compiler knows the type of the value returned by the `java.util.Arrays.asList(new java.math.BigInteger('1'))` method is `java.util.List<java.math.BigInteger>`.

    **Note:** You must set the type parameter in a construct function or use java.lang.Object, which is the same as in Java. For example, the result of `new java.util.ArrayList(java.util.Arrays.asList('1', '2'))` is `java.util.ArrayList<Object>`. The result of `new java.util.ArrayList<String>(java.util.Arrays.asList('1', '2'))` is `java.util.ArrayList<String>`.

-   UDT do not have a clear definition of equal objects. This is caused by data reshuffling. The join example shows that objects may be transmitted between different processes or physical machines. During the transmission, an object may be referenced as two different objects. For example, an object may be shuffled to two machines and then reshuffled.

    Therefore, when you use UDT, you must use the equals method to compare two objects instead of using the equal \(=\) operator.

    The correlations between objects in a row or column can be guaranteed. However, the correlations between objects in different rows or columns are not guaranteed.

-   Currently, UDT cannot be used as shuffle keys in the join, group by, distribute by, sort by, order by, and cluster by clauses.

    UDT can be used at any stages in expressions, but cannot be output as final results. For example, you cannot call the `group by new java.math.BigInteger('123')` method. Instead, you can call the `group by new java.math.BigInteger('123').hashCode()` method. This is because the value returned by hashCode is an int.class type, which can be used as a built-in type of int. This applies the built-in type to Java type mapping rules.

-   UDT have made the data type conversion rules more flexible:
    -   UDT objects can be converted to objects of its base classes by implicit conversion.
    -   UDT objects can be forcibly converted to objects of its base classes or subclasses.
    -   Data type conversion for two objects without inheritance applies the native conversion rules. The conversion may change the data. For example, java.lang.Long type data can be forcibly converted to java.lang.Integer type data. It is a process of converting built-in BIGINT type data to INT type data. This process may cause data changes or even data precision changes.
-   Currently, you cannot save UDT objects, which means that you cannot add UDT objects to tables. DDL does not support UDT. You cannot create tables that contain UDT, unless you convert the data to built-in types using implicit conversion. In addition, the final output cannot be UDT type. To resolve this issue, you can call the toString\(\) method to convert the UDT data to java.lang.String type data because all Java classes support the toString\(\) method. You can use this method to check UDT data during debugging.

    You can also add the `set odps.sql.udt.display.tostring=true;` statement to enable MaxCompute to convert all UDT data to strings by calling the `java.util.Objects.toString(...)` method for debugging. This flag is typically used for debugging because it can only be applied to the print statement. It cannot be applied to the insert statement.

    Binary is a built-in type and supports auto serialization. You can then save the byte\[\] arrays. The saved byte\[\] arrays can be deserialized to binary type.

    Some classes may have their own serialization and deserialization methods, such as protobuffer. To save UDT, you must call serialization and deserialization methods to convert the data to binary data.

-   You can use UDT to achieve the function provided by the scalar function. With the built-in functions [collect list](intl.en-US/User Guide/SQL/Builtin functions/Aggregate functions.md#) and [explode](intl.en-US/User Guide/SQL/Builtin functions/Other functions.md#), you can use UDT to achieve the functions provided by the aggregator and table functions.

## UDT examples {#section_ors_lq4_hfb .section}

-   Example of using Java arrays

    ```
    set odps.sql.type.system.odps2=true;
    set odps.sql.udt.display.tostring=true;
    select
        new Integer[10],    -- Create an array that contains 10 elements.
        new Integer[] {c1, c2, c3},  -- Create an array that contains three elements by initializing an ArrayList.
        new Integer[][] { new Integer[] {c1, c2}, new Integer[] {c3, c4} },  -- Create a multidimensional array.
        new Integer[] {c1, c2, c3} [2], -- Access the elements in the array using indexes.
        java.util.Arrays.asList(c1, c2, c3);    -- This is another way to create a built-in array. It creates a List<Integer>, which can be used as an array<int>.
    from values (1,2,3,4) as t(c1, c2, c3, c4);
    ```

-   Example of using JSON

    The runtime of UDT carries a JSON dependency \(version 2.2.4\), which can be directly used in JSON.

    ```
    set odps.sql.type.system.odps2=true;
    set odps.sql.session.java.imports=java.util.*,java,com.google.gson. *; -- To import multiple packages, separate the packages with commas (,).
    @a := select new Gson() gson;   -- Create a gson object.
    select 
    gson.toJson(new ArrayList<Integer>(Arrays.asList(1, 2, 3))), -- Convert an object to a JSON string.
    cast(gson.fromJson('["a","b","c"]', List.class) as List<String>) --Deserialize the JSON string. The gson also forcibly converts the deserialized result from List<Object> type to List<String> type.
    from @a;
    ```

    Compared with built-in function [get\_json\_object](intl.en-US/User Guide/SQL/Builtin functions/String functions.md#), this method is simple and it improves efficiency by extracting content from the JSON string and then deserializing the string to a supported data type.

    In addition to JSON dependencies, MaxCompute runtime also carries other dependencies, including commons-logging \(1.1.1\), commons-lang \(2.5\), commons-io \(2.4\), and protobuf-java \(2.4.1\).

-   Example of using composite types

    Built-in types of array and map are mapped to java.util.List and java.util.Map, respectively. Results:

    -   Java objects in classes calling the java.util.List or java.util.Map interface can be used in MaxCompute SQL composite type data processing.
    -   Array and map type data in MaxCompute can directly call the List or Map interface.
    ```
    set odps.sql.type.system.odps2=true;
    set odps.sql.session.java.imports=java.util.*;
    select
        size(new ArrayList<Integer>()),        -- Call built-in function size to obtain the size of the ArrayList.
        array(1,2,3).size(),                   -- Call the List method for built-in type array.
        sort_array(new ArrayList<Integer>()),  -- Sort the data in the ArrayList.
        al[1],                                 -- The Java List method does not support indexing. However, the array type supports indexing.
        Objects.toString(a),        -- With this method, you can now convert array type to string type data.
        array(1,2,3).subList(1, 2)             -- Get a sublist.
    from (select new ArrayList<Integer>(array(1,2,3)) as al, array(1,2,3) as a) t;
    ```

-   Example of aggregation

    To use UDT to achieve aggregation, you must first use built-in function [collect\_set](intl.en-US/User Guide/SQL/Builtin functions/Aggregate functions.md#) or [collect\_list](intl.en-US/User Guide/SQL/Builtin functions/Aggregate functions.md#) to convert the data to the List type, and then call the UDT methods to aggregate the data.

    The following example shows how to obtain the median from BigInteger data. You cannot directly call the built-in [median](intl.en-US/User Guide/SQL/Builtin functions/Aggregate functions.md#) function because the data is java.math.BigInteger type.

    ```
    set odps.sql.session.java.imports=java.math.*;
    @test_data := select * from values (1),(2),(3),(5) as t(value);
    @a := select collect_list(new BigInteger(value)) values from @test_data;  -- Aggregate the data to a list.
    @b := select sort_array(values) as values, values.size() cnt from @a;  -- To obtain the median, first sort the data.
    @c := select if(cnt % 2 == 1, new BigDecimal(values[cnt div 2]), new BigDecimal(values[cnt div 2 - 1].add(values[cnt div 2])).divide(new BigDecimal(2))) med from @b;
    -- Final output
    select med.toString() from @c;
    ```

    You cannot use the collect\_list function to implement partial aggregation because it aggregates all data. Using the built-in aggregator or UDAF object is more efficient. We recommend that you use the built-in aggregator if possible. Aggregating all data in a group increases the risk of data skew.

    If the logic of the UDAF object is to aggregate all data, which is the same as the built-in function wm\_concat, using the collect\_list function is more efficient than using the UDAF object.

-   Example of using the table-valued function

    The table-valued function allows you to input and output multiple rows and columns. To input or output multiple rows and columns, follow these steps:

    1.  To input multiple rows or columns, reference the example of using the aggregate function.
    2.  To output multiple rows, you can use a UDT to define a Collection type \(List or Map\), and then call the explode function to split the collection into multiple rows.
    3.  A UDT can contain multiple fields. You can retrieve the data from the fields by calling different getter methods. The data is then output in multiple rows.
    The following example shows how to split a JSON string and output the splitting result in multiple columns:

    ```
    @a := select '[{"a":"1","b":"2"},{"a":"1","b":"2"}]' str; -- Sample data
    @b := select new com.google.gson.Gson().fromJson(str, java.util.List.class) l from @a; -- Deserialize the JSON string.
    @c := select cast(e as java.util.Map<Object,Object>) m from @b lateral view explode(l) t as e;  -- Call the explode function to split the string.
    @d := select m.get('a') as a, m.get('b') as b from @c; -- Output the splitting result in multiple columns.
    select a.toString() a, b.toString() b from @d; -- The final output. Columns a and b in variable d are Object type.
    ```


## Features, performance, and security {#section_mxl_mq4_hfb .section}

UDT have the following features:

-   Easy to use. You do not need to define any functions.
-   You can directly use all functions supported by the JDK. This improves the flexibility of SQL.
-   You can directly reference objects and classes of other languages in SQL statements, which is easy to manage.
-   You can directly reference libraries of other languages. This enables you to reuse your code.
-   You can achieve functions based on the object-oriented concept.

Improvements that will be made to UDT later:

-   UDT will support functions that do not return values, including functions that return values but the returned values are ignored and the passed in data is used, such as the add method provided by the List interface. This method returns the list that you have passed in.
-   Anonymous classes and lambda expressions will be supported.
-   You can use UDT as shuffle keys.
-   More languages will be supported, such as Python.

The execution procedure of UDT is similar to UDF. UDT and UDFs have almost the same performance. In addition, the compute engine has been greatly improved. Therefore, UDT have higher performance in certain scenarios.

-   Deserialization is not required for objects in only one process. Deserialization is required only when the objects are transmitted among processes. This means that UDT do not incur any serialization or deserialization overhead when no data reshuffling is performed, such as calling the join or aggregator function.
-   The runtime of UDT is based on Codegen. It is not based on reflection. Therefore, no performance reduction is experienced. Multiple UDT are wrapped in one FunctionCall and executed at the same time. For example, you may think that multiple UDT methods are called in `values[x].add(values[y]).divide(java.math.BigInteger.valueOf(2))`, but actually only one method is called. UDT focus on small-granularity data processing. However, this does not incur additional overhead for the interface where multiple functions are called.

UDT are restricted by the [Java sandbox](intl.en-US/User Guide/Java Sandbox.md#) model, as same as UDF. To perform restricted operations, you must enable sandbox isolation or apply to join a sandbox whitelist.

