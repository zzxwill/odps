# Input and Output {#concept_usp_myf_vdb .concept}

-   Built-in data types include: BIGINT, DOUBLE, STRING, DATETIME, and BOOLEAN. User-defined types \(UDFs\) are not supported.
-   Multiple-table input is allowed, and the schema of input tables can be different. In a Map function, users can obtain corresponding Table information of the current record.
-   The input can be null. View as an input is not supported.
-   Reduce accepts multiple outputs and can output data to different tables or different partitions in the same table. The schema of different outputs can be different. Different outputs are distinguished through the label however, the default output does not need any label. An output cannot be empty.

For more input and output examples, see [Program Examples](reseller.en-US//MapReduce/Program Example/Multi-input and Output.md).

