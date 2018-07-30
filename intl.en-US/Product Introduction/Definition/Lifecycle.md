# Lifecycle {#concept_zbq_yfb_5db .concept}

The lifecycle of a MaxCompute table or partition is measured from the last update time. If the table or partition remains unchanged after a specified time, MaxCompute automatically recycles it.  The **specified time** indicates the lifecycle.

-   Lifecycle units: days, positive integers only.
-   When a lifecycle is specified for a non-partition table, the lifecycle is counted from the last time the table data was modified \(LastDataModifiedTime\). If table data has not been changed, MaxCompute recycles the table automatically without manual operation \(similar to the drop table operation\).
-   When a lifecycle is specified for a non-partition table, the lifecycle is counted from the last time the table data was modified \(LastDataModifiedTime\).  If table data has not been changed, MaxCompute recycles the table automatically without manual operation \(similar to the drop table operation\).

    **Note:** 

    Lifecycle scanning is started at a scheduled time every day, and entire partitions are scanned.If the partition remains unchanged after its lifecycle, MaxCompute automatically recycles it. 

    When a lifecycle is specified for a partition table, MaxCompute determines whether to recycle the partition based on its LastDataModifiedTime.  Unlike non-partition tables,  a partition table cannot be deleted even when all its partitions have been recycled.

-   You can set the lifecycle of tables, but not of partitions.  The lifecycle of a table can be specified during table creation.
-   If no lifecycle is specified, the table, or partition cannot be automatically recycled by MaxCompute.

For more information on specifying or modifying lifecycles during table creation, and modifying a table’s LastDataModifiedTime, see [Table Operations](../../../../intl.en-US/User Guide/SQL/DDL SQL/Table Operations.md#).

