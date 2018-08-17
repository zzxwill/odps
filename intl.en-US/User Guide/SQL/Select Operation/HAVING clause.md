# HAVING clause {#concept_zgy_5kb_wdb .concept}

HAVING clauses are used because the Where keyword of MaxCompute SQL cannot be used together with aggregate functions.

**Function definition:**

```
SELECT column_name, aggregate_function(column_name)
FROM table_name
WHERE column_name operator value
GROUP BY column_name
HAVING aggregate_function(column_name) operator value
```

**Example**:

A table named Orders contains four fields: Customer, OrderPrice, Order\_date, and Order\_id. To query customers whose OrderPrice is smaller than 2,000, The SQL statement is as follows:

```
SELECT Customer,SUM(OrderPrice) FROM Orders
GROUP BY Customer
HAVING SUM(OrderPrice)<2000
```

