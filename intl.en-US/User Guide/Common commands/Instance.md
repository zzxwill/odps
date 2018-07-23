# Instance {#concept_bvs_hm2_vdb .concept}

## Show instances/Show P {#section_owc_pv2_vdb .section}

**The command format is as follows:**

```
SHOW INSTANCES [FROM startdate TO enddate] [number];
SHOW P [FROM startdate TO enddate] [number];
SHOW INSTANCES [-all];
SHOW P [-all];
```

**Action:**

The information of instances created by current users is displayed.

**Parameters information is as follows:**

-   startdate、enddate：returns the information about the instances during specified period \(from startdate to enddate\). The following format must be met: yyyy-mm-dd, precision to the day.  The parameters are optional. If the parameters are left unspecified, instances submitted within three days are returned.
-    number： Specify the number of instance to be showed.In accordance with the time scheduling, return N \(number\) instances nearest to the current time.  If it is not specified, all instances that meet the requirements are shown. In chronological order, the specified number of instances most recently preceding the current time are returned. 
-   -all：The information of all instances meeting requirements is returned. Note: The user executing the command needs to have list permission for the project.
-   The output items: Include StartTime \(the time accurate to seconds\), RunTime \(s\), Status \(including Waiting, Success, Failed, Running, Cancelled, and Suspended\).

**InstanceID and corresponding SQL are as following:**

```
StartTime RunTime Status InstanceID Query
2015-04-28 13:57:55 1s Success 20150428xxxxxxxxxxxxxxxxxx ALIYUN$xxxxx@aliyun-inner.com select * from tab_pack_priv limit 20;
...    ...    ...    ...    ...    ...
...    ...    ...    ...    ...    ......
```

**Six kinds of instance status are possible:**

-   Running
-   Success
-   Waiting
-   Failed \(Job failed but data in the target table is modified\)
-   Suspended
-   Cancelled

**Note:** The commands from the preceding example run in MaxCompute client.

##  Status Instance {#section_abq_rx2_vdb .section}

**The command format is as follows:**

```
 
status <instance_id>; -- instance_id: the unique identifier of an instance, to specify which instance to be queried.
```

**Action:**

-   Query the status of specified instance, such as Success, Failed, Running, and Cancelled.
-   If this instance is not created by current user, exception is returned.

**Examples:**

```
odps@ $project_name>status 20131225123xxxxxxxxxxxxxxx;
Success
```

Query the status of an instance which ID is 20131225123xxxxxxxxxxxxxxx, and the result is Success.

**Note:** The commands from the preceding example run in MaxCompute client.

## Top Instance {#section_ifc_2y2_vdb .section}

**The command format is as follows:**

```
 top instance;top instance -all;
```

**Action:**

Permission requirements: the user is project owner or administrator role.

top instance: the job information of the current account that is running in the project is displayed, including ISNTANCEID, Owner, Type, StartTime, Progress, Status, Priority, RuntimeUsage \(CPU/MEM\), TotalUsage \(CPU/MEM\), QueueingInfo \(POS/LEN\) and so on.

top instance-all :returns all currently executing jobs in the current project.

**Examples:**

```
odps@ $project_name>top instance;
```

**Note:** The commands from the preceding example run in MaxCompute client \(version 0.29.0 or later\).

##  Kill Instance {#section_apd_ty2_vdb .section}

**The command format is as follows:**

```
kill <instance_id>; -- instance_id: The unique identifier of an instance,  which must be ID of an instance whose status is 'Running', otherwise, an error is returned.
```

**Action:**

Stop specified instance. Instance status must be Running.

**Examples:**

```
odps@ $project_name>kill 20131225123xxxxxxxxxxxxxxx;
```

Stop the instance which ID is 20131225123xxxxxxxxxxxxxxx.

**Note:** 

-   The commands from the preceding example run in MaxCompute client.
-   This is an asynchronous process. It does not mean that the distributed task has stopped after the system accepts the request and returns result. To check whether the instance is deleted, use the status command.

## Desc Instance {#section_a1l_fz2_vdb .section}

**The command format is as folllows:**

```
desc instance <instance_id>; -- instance_id: The unique identifier of an instance.
```

**Action:**

Get the job information according to instance ID, including SQL, owner, startime, endtime, status.

**Examples:**

```
odps@ $project_name> desc instance 20150715xxxxxxxxxxxxxxx;
ID 20150715xxxxxxxxxxxxxxx
Owner ALIYUN$XXXXXX@alibaba-inc.com
StartTime 2015-07-15 18:34:41
EndTime 2015-07-15 18:34:42
Status Terminated
console_select_query_task_1436956481295 Success
Query select * from mj_test;
```

Query all the job information related to the instance which ID is 20150715xxxxxxxxxxxxxxx.

**Note:** The commands from the preceding example run in MaxCompute client.

## Wait instance {#section_llj_zz2_vdb .section}

**The command format is as follows:**

```
wait <instance_id>;  -- instance_id: The unique identifier of an instance.
```

**Action:**

Get running task information, including logs according to instance ID, and logview link. You can view task details by accessing logview link.

**Examples:**

```
wait 201709251611xxxxxxxxxxxxxx;
ID = 201709251611xxxxxxxxxxxxxx
Log view:
http://logview.odps.aliyun.com/logview/?h=http://service.odps.aliyun.com/xxxxxxxxxx
Job Queueing...
Summary:
resource cost: cpu 0.05 Core * Min, memory 0.05 GB * Min
inputs:
        alian.bank_data: 41187 (588232 bytes)
outputs:
        alian.result_table: 8 (640 bytes)
Job run time: 2.000
Job run mode: service job
Job run engine: execution engine
M1:
        instance count: 1
        run time: 1.000
        instance time:
                min: 1.000, max: 1.000, avg: 1.000
        input records:
                TableScan_REL5213301: 41187 (min: 41187, max: 41187, avg: 41187
)
        output records:
                StreamLineWrite_REL5213305: 8 (min: 8, max: 8, avg: 8)
R2_1:
        instance count: 1
        run time: 2.000
        instance time:
                min: 2.000, max: 2.000, avg: 2.000
        input records:
                StreamLineRead_REL5213306: 8 (min: 8, max: 8, avg: 8)
        output records:
                TableSink_REL5213309: 8 (min: 8, max: 8, avg: 8)
```

