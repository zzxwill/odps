# Explain {#concept_s2f_wkb_wdb .concept}

The Explain operation of MaxCompute SQL helps to display the description of the final execution plan structure corresponding to a DML statement. The execution plan is the program used at the final stage to run SQL semantics.

**Function definition:**

```
EXPLAIN <DML query>;
```

The execution result of ‘explain’ includes the following:

-   The dependency structure of all the tasks corresponding to this DML statement.
-   All task dependency structures in a task.
-   All operator dependency structures in a task.

**For examples**: 

```
EXPLAIN
SELECT abs(a.key), b.value FROM src a JOIN src1 b ON a.value = b.value;
```

The output result of Explain consists of the following parts:

-   The dependency between jobs: `job0 is root job`, As the query requires one job \(job0\), only one row of information is required.
-   The dependency between tasks:

    ```
    In Job job0:
    root Tasks: M1_Stg1, M2_Stg1
    J3_1_2_Stg1 depends on: M1_Stg1, M2_Stg1
    ```

    Job0 contains three tasks, among which M1\_Stg1 and M2\_Stg1 are run first, followed by J3\_1\_2\_Stg1.

    The naming rules of tasks are as follows:

    -   MaxCompute contains four types of tasks: MapTask, ReduceTask, JoinTask, and LocalWork.
    -   The first letter of a task name represents the current task type. For example, **M2Stg1** is a MapTask.
    -   The number following the first letter represents the current task ID, which must be unique in all tasks corresponding to the current query.
    -   The numbers separated by underscores \(\_\) represent the direct dependencies of the current task. For example, J3\_1\_2\_Stg1 indicates that the current task \(whose ID is 3\) depends on two tasks whose IDs are 1 and 2.

-   The third part is the operator structure in the task. The operator string describes the execution semantics of a task:

    ```
    In Task M1_Stg1:
      Data source: yudi_2.src # Data source describes the input content of the current task
      TS: alias: a # TableScanOperator
          RS: order: + # ReduceSinkOperator
              keys:
                   a.value
              values:
                   a.key
              partitions:
                   a.value
    In Task J3_1_2_Stg1:
      JOIN: a INNER JOIN b # JoinOperator
          SEL: Abs(UDFToDouble(a._col0)), b._col5 # SelectOperator
              FS: output: None # FileSinkOperator
    In Task M2_Stg1:
      Data source: yudi_2.src1
      TS: alias: b
          RS: order: +
              keys:
                   b.value
              values:
                   b.value
              partitions:
                   b.value
    ```

    -   Description of operators:
        -   TableScanOperator: Describes the logic of FROM statement blocks in a Query statement. The input table name \(alias\) is displayed in the EXPLAIN results.
        -   SelectOperator: Describes the logic of SELECT statement blocks in a QUERY statement. The columns to be passed to the next operator are displayed in the Explain results, separated by commas \(,\). 
            -   If column references are to be passed, `< alias >.< column_name >` is displayed
            -   If expression results are to be transmitted, they are displayed as functions, for example, `func1(arg1_1, arg1_2, func2(arg2_1, arg2_2))`.
            -   If constants are to be passed, the values are directly displayed.
        -   FilterOperator: Describes the logic of WHERE statement blocks in a QUERY statement. A WHERE condition expression is displayed in the Explain results, with the display rules similar to those of SelectOperator.
        -   JoinOperator: Describes the logic of JOIN statement blocks in a QUERY statement. Both the tables to be joined and the JOIN method are displayed in the Explain results.
        -   GroupByOperator: Describes the logic of aggregate operations. This structure is displayed if an aggregate function is used in a QUERY statement. The aggregate function content is displayed in the Explain results.
        -   ReduceSinkOperator: Describes the logic of data distribution operations between tasks. If the result of the current task is to be passed to another task, ReduceSinkOperator must be used at the end of the current task to perform the data distribution operation. The sorting method of output results, distributed keys, values, and columns used to calculate the hash value are displayed in the Explain results.
        -   FileSinkOperator: Describes the storage operation of final data. If Insert statement blocks exist in the QUERY statement, the target table name is displayed in the Explain results.
        -   LimitOperator: Describes the logic of Limit statement blocks in a QUERY statement. The number of LIMIT is displayed in the Explain results.
        -   MapjoinOperator: Similar to JoinOperator, it describes JOIN operations in large tables.

**Note:** If a QUERY statement is so complicated that Explain has too many results, API restrictions are triggered, which leads to incomplete display of Explain results.  In this case, you can split the QUERY and perform the Explain operation on each part to understand the job structure.

