# SCALAR SUBQUERY {#concept_hjc_xz4_hfb .concept}

## SCALAR SUBQUERY {#section_bb1_b1p_hfb .section}

当SUBQUERY的输出结果为单行单列的时候，可以当做标量来使用。如：

```
select * from t1 where (select count(*)  from t2 where t1.a = t2.a) > 1; 
-- 等效于
select t1.* from t1 left semi join (select a, count(*) from t2 group by a having count(*) > 1) t2 on t1 .a = t2.a;
```

语句`select count(*) from t2 where t1.a = t2.a;` 的输出结果是一个row set，但可以判断这条语句的输出有且仅有一行一列，因此可以将其当做标量使用，即可以参与标量运算（ ‘\>’ \) 。但在实现过程中，实际会尽可能地转成join来处理，如等效的第二条语句。

注意：能当成标量来使用的SUBQUERY必须是在编译阶段就能够确认返回结果只有一行一列的查询，如果一条语句，即使能够确定在实际运行过程中只会产生一行数据，但是编译过程中确定不了，编译器也是会报错。

目前编译器能够接受的语句需满足两个特征：

-   子查询的select 列表里面用了聚合函数，且不是在表值函数的参数列表中。
-   子查询中包含聚合函数的这一层查询没有group by语句。

同时还有两个限制：

-   scalar subquery支持引用外层查询的列，当嵌套多层scalar subquery时，只支持引用直接外层的列。如：

    ```
    select * from t1 where (select count(*) from t2 where t1.a = t2.a) = 3;--允许
     select * from t1 where (select count(*) from t2 where (select count(*) from t3 where t3.a = t1.a) = 2) = 3;-- 不允许，不能在子查询的子查询中引用外部查询的列
    ```

-   scalar subquery只能where中使用。比如下面是不允许的

    ```
    select * from t1 where (select t1.b + count(*) from t2) = 3;  -- 不能在子查询的select 中引用
      select (select count(*) from t2 where t2.a = t1.a)  from t1;  -- 不能在外层查询的select 中引用
    ```


实际上所有的满足一行一列输出值的子查询都可以类似上述示例进行重写（如果查询的结果只有一行，在外面包一层MAX或MIN操作，其结果不变）。

