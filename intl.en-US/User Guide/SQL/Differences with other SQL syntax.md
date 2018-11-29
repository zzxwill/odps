# Differences with other SQL syntax {#concept_fsf_mvf_vdb .concept}

This article takes a SQL perspective. and introduces MaxCompute by comparing MaxCompute SQL with Hive, MySQL, Oracle, SQL Server Unsupported pant, and DML syntax.

## Pant syntax not supported by MaxCompute {#section_ed4_pvf_vdb .section}

|Grammar|MaxCompute|Hive|MySql|Oracle|SQL Server|
|:------|:---------|:---|:----|:-----|:---------|
|CREATE TABLE—PRIMARY KEY|N|N|Y|Y|Y|
|CREATE TABLE—NOT NULL|N|N|Y|Y|Y|
|Create Table-cluster|N|Y|N|Y|Y|
|Create Table-External table|Y \(supports OSS and OTS External tables\)|Y|N|N|N|
|Create Table-maid table|N|Y|Y|Y|Y \(with \# prefix\)|
|Create Index|N|Y|Y|Y|Y|
|Virtual Column|N|N \(only 2 predefined\)|N|Y|Y|

## DML syntax not supported by MaxCompute {#section_ws2_vvf_vdb .section}

|Grammar|MaxCompute|Hive|MySQL|Oracle|SQL Server|
|:------|:---------|:---|:----|:-----|:---------|
|Select-recurrent CTE|N|N|N|Y|Y|
|Select-group by roll up|N|Y|Y|Y|Y|
|Select-group by cube|N|Y|N|Y|Y|
|Select-grouping set|N|Y|N|Y|Y|
|Maid join|Y|Y|N|Y|Y|
|Select-Fig|N|N|N|Y|Y|
|Select-correlated subquery|N|Y|Y|Y|Y|
|Set operator-Union \(distinct\)|Y|Y|Y|Y|Y|
|Set operator-intersect|N|N|N|Y|Y|
|Set operator-minus|N|N|N|Y|Y \(keyword\)|
|Update... Where|N|Y|Y|Y|Y|
|Update... Order by limit|N|N|Y|N|Y|
|Delete... Where|N|Y|Y|Y|Y|
|Delete... Order by limit|N|N|Y|N|N|
|Analytic-reusable windowing clause|N|Y|N|N|N|
|Analytic-range|N|Y|N|Y|Y|

