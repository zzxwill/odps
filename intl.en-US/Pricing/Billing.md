# Billing {#concept_f32_fpm_xdb .concept}

**The billing of MaxCompute is calculated according to **

-   the projects you create and the items you use within each project. 

-   Billable items can include storage, computing resources, 

-   and data downloads of a project. MaxCompute fees are calculated daily.


## Storage pricing {#section_efw_kpm_xdb .section}

The data that is stored in MaxCompute, including tables and resources, is billed according to the capacity size.

MaxCompute collects the current storage usage in every project space at hourly level, and calculates the daily average storage value and multiplies the unit price based on the project space.

|Less than 1 GB USD/GB/Day|1 GB-100 GB \(contained 100 GB\) USD/GB/Day|100 GB-9 TB \(contained 9 TB\) USD/GB/Day|9 TB-90 TB \(contained 90 TB\) USD/GB/Day|90 TB-900 TB \(contained 900 TB\) USD/GB/Day|900 TB-1024 TB \(contained 1024 TB\) USD/GB/Day|Over 1024 TB USD/GB/Day|
|:------------------------|:------------------------------------------|:----------------------------------------|:----------------------------------------|:-------------------------------------------|:----------------------------------------------|-----------------------|
|Free|0.0028|0.0014|0.0013|0.0011|0.0009|Please contact us|

For example, if you store 50 TB data in MaxCompute, the bill is calculated as follows.

```
（100GB-1）* 0.0028 USD/GB/day
+（1024-100）GB* 0.0014 USD/GB/day
+（10240-1024）GB* 0.0013 USD/GB/day
+（50*1024-10240）GB * 0.0011 USD/GB/day
=58.61 USD/day
```

**Note:** 

-   Because MaxCompute compresses and stores user data, the bill is based on the capacity size of the data after compressed. This means the capacity size is different from the data file size of which is measured by a user. The compression ratio is generally about 5 times.

-   Generally, MaxCompute fees are deducted no more than 6 hours after the daily fee calculation is completed,  and are automatically deducted from the corresponding account balance. 

-   On the MaxCompute console, you can view your consumption details under Bill Management.


## Computation pricing {#section_lfw_kpm_xdb .section}

MaxCompute supports two kinds of billing methods.

-   Pay-As-You-Go:  Each task is measured according to the input size by job cost.

-   Subscription \(CU cost\):  Users can subscribe the usage of a part of resource in advance. This method is only supported on **Alibaba Cloud DTPlus Platform**.


Currently,  MaxCompute supports the following computing task types: SQL, UDF, MapReduce, Graph, and machine learning. Where SQL \(excluding UDF\) computing tasks have been charged and MapReduce tasks are being started in December 19, 2017 for charges, other types are not on schedule.

**Pay-As-You-Go**

Pay-As-You-Go is for SQL tasks and MapReduce tasks.

Pay-As-You-Go for SQL tasks

Every SQL task is billed according to **DataInputsize** and **SQLComplexity**. Once the SQL task is completed, MaxCompute sends its metering information to the billing system, which calculates the fee and adds it to the next payment. The MaxCompute SQL task is charged according to I/O for each job. All daily measurement information is paid in the next day.

Every SQL task is billed according to DataInputsize and SQLComplexity.  The MaxCompute SQL task is charged according to I/O for each job. All daily measurement information is paid in the next day.

The billing of a SQL task is calculated as follows.

```
 Computing Cost of One SQL = DataInputSize * SQLComplexity * SQL Price
```

The current 

|price for | a |
|:---------|:--|
|SQL task |is 0.0438 USD/GB.|

-   **DataInputSize**: The actual data size that a SQL statement scans. Most of SQL statements have partition filtering and column pruning, so this value is generally less than the source table data size.

    -   Column pruning: For example, the submitted SQL is `select f1,f2,f3 from t1;`.  Only the data size of three columns \(f1, f2, and f3\) in t1 are charged.

    -   Partition filtering: For example, a SQL statement includes  “where ds\>”20130101“. The “ds” is a partition column. The data size is calculated only according to the read partition, rather than the data of other partitions, and then billed.

-   **SQL Complexity**: First MaxCompute counts keywords in SQL statement, and then converts to SQL complexity. The details are shown as follows.

    -   SQL keyword number = join Number + group by number + order by number + distinct number + window function number +  max \(insert into Number -1, 1\)

    -   SQL complexity calculation:

        -   If SQL keyword number is less than or equal to 3, the complexity is 1.

        -   If SQL keyword number is less than or equal to 6, the complexity is 1.5.

        -   If SQL keyword number is less than or equal to 19, the complexity is 2.

        -   If SQL keyword number is greater than or equal to 20, the complexity is 4.


For example, the input SQL statement is shown as follows.

```
cost sql <SQL Sentence>;
```

The example is as follows:

```

odps@ $odps_project >cost sql SELECT DISTINCT total1 FROM
(SELECT id1, COUNT(f1) AS total1 FROM in1 GROUP BY id1) tmp1
ORDER BY total1 DESC LIMIT 100;

Complexity:1.5
```

The preceding SQL includes 4 keywords \(one DISTINCT, one COUNT, one GROUP BY, and one ORDER\),  so the SQL complexity is 1.5.  If the data volume of table “in1” is 1.7 GB, then the actual consumption is shown as follows.

```
1.7 * 1.5 * 0.0438 = 0.11 USD
```

**Note:** 

-   The bill invoicing time is usually before 06:00 in the next day.  After the computing task successfully ends, the system counts the data size and SQL complexity. After the bill is generated, the fee is automatically deducted from your account.  If the SQL task is unsuccessful, the deduction terminates.

-   Similarly to storage, SQL computing also calculates and bills the data size after compression.


Pay-As-You-Go for MapReduce

In December 19, 2017, MaxCompute began charging for MapReduce tasks. The billing of a MapReduce computing task is calculated as follows.

```
Computing Cost of One MR task = TotalTime * MR Price(USD)
```

The current 

|price |for a MapReduce|
|:-----|:--------------|
| task is| 0.0690 USD/Hour/Task.|

The Calculating Time for one successful MR task is: Execution time \(Hour\) × the number of cores that task calls

If one MR task calls 100 cores, and the tasks takes 30 minutes to complete, the Calculating Time for the MR task is: 0.5 hour × 100 cores = 50

After the MR task is finished, MaxCompute sends its metering information to the billing system, which calculates the fee and adds it to the next payment.

**Note:** 

-   You are not charged if a task failed to run.

-   The Calculating time does not contain the waiting time for execution.

-   If you purchased the MaxCompute Subscription service, you can use MR computing tasks for free within the range of the services you purchased, no additional fees is charged.


**Subscription \(CU cost\)**

Subscription by CU cost is only supported on the **Alibaba Cloud DTPlus Platform**.  Subscription allows you to pay an initial fee \(monthly or annually\) for your entire reserved resources,  that is, compute unit  \(CU\) term.  One CU means 1 core CPU and 4 GB memory.

|Resource definitions|Memory|CPU|USD/month|
|:-------------------|:-----|:--|:--------|
|1 CU|4GB|1 CPU|22.0|

We recommend that new users use  Pay-As-You-Go for I/O . For first time users,  Pay-As-You-Go is recommended as the initial billing method to gauge resource usage \(Subscription may l ead to excessive billing for unused resources.

## Pay-As-You-Go data download {#section_fgw_kpm_xdb .section}

You can download data from the extranet through the MaxCompute Tunnel.  The billing of a data download is calculated as follows.

```
Download Cost from Extranet/time = Downloaded Data Volume * Download Price
```

The specific price is as follows:

|The current |USD/GB|
|:-----------|:-----|
|price for data download is |0.1166 USD/GB.|

**Note:** 

-   The MaxCompute sends messages to you according to your download measurement information, and provides your download costs in the next day.

-   Download data Volume refers to the size of an HTTP body for one download request.  The HTTP body that  carries data uses protobuffer encoding, so it is generally smaller than the original data capacity, but larger than the data stored in MaxCompute after compression.

-   The different billing methods are applicable to different network environments, such as public network, classic network of Alibaba Cloud, or VPC network.  For more information, see [Access domains and data centers](https://www.alibabacloud.com/help/zh/doc-detail/34951.htm).


