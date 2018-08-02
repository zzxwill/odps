# SQL implements multiple rows of data into one {#concept_qxm_dcc_5db .concept}

This article will show you how to use SQL to compress multiple data into one.

## Scenario example {#section_yhj_3lc_5db .section}

The following table data is used as an example:

|class|gender|name|
|:----|:-----|:---|
|1|M|LiLei|
|1|F|HanMM|
|1|M|Jim|
|2|F|Kate|
|2|M|Peter|

**Scenario 1**

Depending on your needs, the common scenarios are as follows:

|class|names|
|:----|:----|
|1|LiLei,HanMM,Jim|
|2|Kate,Peter|

Using a separator for string mosaic is similar to this, you can use the following statement:

```
SELECT class, wm_concat(',', name) FROM students GROUP BY class;
```

**Scenario two**

Another common requirement is as follows:

|class|cnt\_m|cnt\_f|
|:----|:-----|:-----|
|1|2|1|
|2|1|1|

You can use the following statement for the need to change multiple columns like this:

```

SELECT 
class
,SUM(CASE WHEN gender = 'M' THEN 1 ELSE 0 END) AS cnt_m
,SUM(CASE WHEN gender = 'F' THEN 1 ELSE 0 END) AS cnt_f
FROM students
GROUP BY class;
```

