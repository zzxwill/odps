# Constraints and limitations {#concept_gjk_x35_z2b .concept}

This article introduces you to the constraints and limitations of using the MaxCompute Lightning service.

## DDL/DML constraints and limitations {#section_rzb_tdl_z2b .section}

MaxCompute Lightning only supports Select queries for MaxCompute tables and does not support UPDATE, CREATE, DELETE, and INSERT operations on MaxCompute tables.

## Query constraints and limitations {#section_e15_x35_z2b .section}

-   A maximum number of 1,024 scanned partitions can be queried when you query partitioned tables.
-   Currently, views cannot be created or used.
-   Currently, MAP、ARRAY、TINYINT、BINARY、TIMESTAMP and DECIMAL with accuracy data types are not supported.
-   A maximum of 1 TB data can be scanned for a table in each query.
-   The size of the submitted query statement cannot exceed 100 KB.
-   The query timeout period is one hour.

## UDF constraints and limitations {#section_f15_x35_z2b .section}

-   User-defined functions \(UDF\) created using MaxCompute cannot be used in MaxCompute Lightning.
-   [PostgreSQL](https://www.postgresql.org/docs/8.2/static/functions.html) user-defined functions cannot be created or used in MaxCompute Lightning.
-   MaxCompute built-in functions are not supported at this time.

## Query concurrency constraints {#section_nsd_c2l_z2b .section}

A maximum of 20 concurrent queries for a MaxCompute project is supported by MaxCompute Lightning.

