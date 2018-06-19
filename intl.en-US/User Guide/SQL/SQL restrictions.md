# SQL restrictions {#concept_yjp_crl_vdb .concept}

Some users may fail to notice specific restrictions and find the service has stopped.  The restrictions for MaxCompute SQL include the following:

|Boundry name|Maximum value/Restriction|Class|Description|
|:-----------|:------------------------|:----|:----------|
|Length of table name|128 bytes|Length limit|Table names and column names cannot contain special characters. They can contain only English letters \(a-z, A-Z\), numbers, and underscores \(\_\), and must start with a letter.|
|Annotation length|1,024 bytes|Length limit|The annotation can contain valid strings of up to 1,024 bytes.|
|Column definitions|1,200| Quantity limit| One table can contain 1,200 column definitions at most.|
|Partitions |60,000| Quantity limit|One table can contain a maximum of 60,000 partitions.|
|Partition levels of a table|6 levels| Quantity limit| A table can contain a maximum of six levels of partition.|
|Statistical definitions|100| Quantity limit|One table can contain a maximum of 100 statistical definitions.|
|Statistical definitions| 64,000|Length limit|A statistical definition can contain a maximum of 64,000 bytes.|
|Screen display|10,000 rows| Quantity limit|The screen display of a SELECT statement outputs a maximum of 10,000 rows.|
|INSERT targets|256|Quantity limit|A multiins operation can insert a maximum of 256 targets at a time.|
|UNION ALL|256|Quantity limit|The UNION ALL operation can be performed on a maximum of 256 tables.|
|MAPJOIN 　|Eight small tables|Quantity limit|A MAPJOIN operation can be performed on a maximum of eight small tables.|
|MAPJOIN memory restriction|512 MB|Quantity limit|The memory size of all small tables on which MAPJOIN operation is performed cannot exceed 512 MB.|
|Window functions|Five|Quantity limit|A SELECT statement can contain a maximum of five window functions.|
|ptinsubq 　|1,000 rows|Quantity limit|The results returned by PT IN SUBQUERY cannot exceed 1,000 rows.|
|SQL statement |2 MB|Length limit|The maximum length of an SQL statement is 2 MB.|
|Number of conditions for a where clause|256|Quantity limit|A where clause can use a maximum of 256 conditions.|
|Length of column records|8 MB|Quantity limit|The maximum length of a cell in tables is 8 MB.|
|Number of parameters of an in statement|1,024|Quantity limit|Specifies the maximum number of parameters of an in statement, for example, in  \(1,2,3….,1024\).  An excess of parameters of in\(…\) results  in compilation pressure. 1,024 is a recommended value, not a limit value.|
|jobconf.json|1 MB|Length limit|The size of ‘jobconf.json’ is 1 MB. Including too many partitions in a table may cause ‘jobconf.json’ to exceed 1 MB.|
|View|Not writable|Operation restriction|A view cannot be written or operated using an insert statement.|
|Column data type|Not allowed|Operation limit|The data type and position of a column cannot be modified.|
|java udf function|Cannot be abstract or static|Operation limit|A Java UDF cannot be abstract or static.|
|A maximum of 10,000 partitions can be queried.|10,000|Quantity limit|A maximum of 10,000 partitions can be queried.|

**Note:** The restrictions of MaxCompute SQL cannot be manually modified or configured.

