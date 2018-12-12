# SQL reference {#concept_yxk_f35_z2b .concept}

Based on the official PostgreSQL function, MaxCompute Lightning adds the following built-in functions.

## Query syntax {#section_lb3_wjh_1fb .section}

The MaxCompute Lightning query engine is based on PostgreSQL 8.2 and currently only supports SELECT queries for existing MaxCompute tables. For more information about the query syntax, see [PostgreSQL documentation](https://www.postgresql.org/docs/8.2/static/queries.html).

## Function {#section_yn5_yjh_1fb .section}

The MaxCompute Lightning query engine is based on PostgreSQL 8.2 supports builtin funtion, for more information, see [PostgreSQL documentation](https://www.postgresql.org/docs/8.2/static/functions.html).

Based on the official PostgreSQL function, MaxCompute Lightning adds the following builtin functions.

-   **MAX\_PT**

    **Command format**

    `max_pt(table_full_name)`

    **Command description**

    For partitioned tables, this function returns the maximum value of the level-one partition of the partitioned table, sorted alphabetically, and there is a corresponding data file under the partition.

    **Parameter description​**

    table\_full\_name: String type, used to specify the table name \(must carry the project name, such as prj.src\), you must have read access to this table.

    **Return value**

    Returns the value of the largest level-one partition.

    **Example**

    Suppose tbl is a partition table, the corresponding partition is as follows, and both contain data files:

    ```
    pt =‘20120901’
    pt =‘20120902’
    ```

    Then the partition max\_pt returns the value of '20120902' in the following statement, and the MaxCompute SQL statement reads the data under the pt=‘20120902’ partition.

    `select * from tbl where pt=max_pt('myproject.tbl');`


