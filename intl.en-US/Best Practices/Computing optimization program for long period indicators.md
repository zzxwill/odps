# Computing optimization program for long period indicators {#concept_ngc_wtd_5db .concept}

## Experiment background {#section_obq_15d_5db .section}

E-commerce companies such as TaoBao have all kinds of perspectives and procedures regarding user data analysis. E-commerce data warehouse and business analysis often require the calculation of various indicators  such as the number of visitors in last few days, buyers, and regular customers.

The calculation of these indicators is based on the data that has accumulated on the e-commerce platform or the online store over a period. This article assumes that the data for calculation is stored in MaxCompute.

In general, these indicators are calculated using the log detail table. The following code calculates the number of visitors of the product in the past 30 days:

```

select item_id --Item ID 
  ,count(distinct visitor_id) as ipv_uv_1d_001 
from Log detail table that stores the data about the visits to the item 
where ds <= ${bdp.system.bizdate} 
and ds >=to_char(dateadd(to_date(${bdp.system.bizdate},'yyyymmdd'),-29,'dd'),'yyyymmdd') 
 group by item_id;
```

**Note:** All the variables in the code are the scheduling variables of DataWorks. They are only applicable to the scheduling tasks of DataWorks.  This condition applies in the rest of this article.

The preceding code has a serious problem when many logs are generated every day. Massive **Map Instance** are required and may exceed the **99999** quantity limit, affecting the execution of the map task and subsequent operations.

The need for massive map instances is owing to the large volume of log data per day, not to mention the log data generated over a period of 30 days. The SELECT operation requires more map instances than permitted by the upper limit, causing abnormal code execution.

## Tutorial goal {#section_sbq_15d_5db .section}

The impact on performance resulting from the calculation of long-period indicators can be minimized in either of the following ways:

-   Reduce the data volume to avoid a summary of data from multiple days.

-   Create a temporary table and make a summary of 1D data to remove duplicate data and thus reduce the data volume on a daily basis.


## Tutorial scheme {#section_fvx_m5d_5db .section}

**Procedure**

1.  Create a medium table and make a daily summary.

    In the preceding example, you can create a medium table with a granularity of item\_id+visitor\_id for daily summary.  Record this table as A. For example:

    ```
    
    insert overwrite table mds_itm_vsr_xx(ds='${bdp.system.bizdate} ')
    select item_id,visitor_id,count(1) as pv
      from
      （
      select item_id,visitor_id
      from Log detail table that stores the data about the visits to the item 
      where ds =${bdp.system.bizdate} 
      group by item_id,visitor_id
      ）a;
    ```

2.  Calculate the data from multiple days, and use the medium table to make a summary.

    Make a 30-day summary on Table A, as shown in the following code:

    ```
    
    select item_id
            ,count(distinct visitor_id) as uv
            ,sum(pv) as pv
      from mds_itm_vsr_xx
      where ds <= '${bdp.system.bizdate} '
      and ds >= to_char(dateadd(to_date('${bdp.system.bizdate} ','yyyymmdd'),-29,'dd'),'yyyymmdd')
      group by item_id;
    ```


## Impact and consideration {#section_bcq_15d_5db .section}

In the preceding method, the detailed data of daily access logs is deduplicated to reduce the data volume while improving the performance. A weakness of this method is the need to read data from N partitions for every calculation of multi-day data.

To avoid repeated data reads, you can compress the data in the N partitions into one partition which contains all historical data.

To do this, you can **calculate long-period indicators in an incremental and accumulative way**.

## Use cases {#section_ccq_15d_5db .section}

Calculate the number of regular customers of the store for the past one day.  A regular customer is defined as a buyer who has purchased items at the store over a period, such as the past 30 days.

The number of regular buyers is calculated as follows:

```

select item_id ----Item ID     
        ,buyer_id as old_buyer_id
from Detail table that stores the data about buyers and purchased items 
where ds < ${bdp.system.bizdate} 
and ds >=to_char(dateadd(to_date(${bdp.system.bizdate},'yyyymmdd'),-29,'dd'),'yyyymmdd') 
group by item_id
        ,buyer_id;
```

**Improvement**:

-   Maintain a table to record the relationship between buyers and purchased items. Specifically, the first purchase period, the last purchase period, the total number of purchased items, and the total amount of the purchases. Record this table as A.

-   Update the data of Table A every day based on the payment detail log for the past one day.

-   To determine whether a person is a regular buyer, check whether the last purchase time is within the past 30 days. This deduplicates data relationship pairs and reduces the volume of input data.


