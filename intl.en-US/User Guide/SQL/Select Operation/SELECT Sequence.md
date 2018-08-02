# SELECT Sequence {#concept_jsv_mkb_wdb .concept}

The actual logic execution sequence of SELECT statements written in compliance with the preceding SELECT syntax is different from the standard writing sequence. See the following:

```
SELECT key, max(value) FROM src t WHERE value > 0 GROUP BY key HAVING sum(value) > 100 ORDER BY key LIMIT 100;
```

The actual logic execution sequence is `FROM->WHERE->GROUP BY->HAVING->SELECT->ORDER BY->LIMIT`.  ORDER  BY can only reference columns generated in the SELECT list rather than accessing columns in the FROM source table.  The HAVING operation can access GROUP BY keys and aggregate functions.  When the SELECT operation is performed, SELECT can  only access group keys and aggregate functions rather than columns in the FROM source table if GROUP BY exists.The columns generated in the select list can only be referenced in by, rather than accessing the columns in the source table of from.

To avoid confusion, MaxCompute allows users to write a query statement by the execution sequence. For example, the preceding statement can be written as follows:

```
FROM src t WHERE value > 0 GROUP BY key HAVING sum(value) > 100 SELECT key, max(value) ORDER BY key LIMIT 100;
```

