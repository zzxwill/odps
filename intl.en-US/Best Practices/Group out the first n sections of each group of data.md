# Group out the first n sections of each group of data {#concept_wnd_z4c_5db .concept}

This article will show you how to group data, take out the first n sections of data for each data group.

## Sample Data {#section_qkr_fpc_5db .section}

Current data, as shown in the following table:

|empno|ename|job|sal|
|:----|:----|:--|:--|
|7369|SMITH|CLERK|800.0|
|7876|SMITH|CLERK|1100.0|
|7900|JAMES|CLERK|950.0|
|7934|MILLER|CLERK|1300.0|
|7499|ALLEN|SALESMAN|1600.0|
|7654|MARTIN|SALESMAN|1250.0|
|7844|TURNER|SALESMAN|1500.0|
|7521|WARD|SALESMAN|1250.0|

## Implementation Method {#section_ykr_fpc_5db .section}

You can achieve this in two ways:

-   Take out the line number of each data and filter it with the where statement.

    ```
    
    SELECT * FROM (
      SELECT empno
      , ename
      ,sal
      , job
      , ROW_NUMBER() OVER (PARTITION BY job ORDER BY sal) AS rn
      FROM emp
    ) tmp
    WHERE rn < 10;
    ```

-   Implement the Split Function using UDTF.

    For more information, see the last example in  [this article](https://yq.aliyun.com/articles/67084). This example can more quickly judge the current sequence number. In this example, it will not work if you have exceeded the planned number of lines \(for example, 10 \),  and thus improve the efficiency of the calculation.


