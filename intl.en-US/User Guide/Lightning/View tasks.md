# View tasks {#concept_ett_h35_z2b .concept}

## View running queries {#section_k3d_bdl_z2b .section}

MaxCompute Lightning provides a system view stv\_recents. By querying the view, you can view all query tasks that the current user is running, and the related information, including query ID, user name, query SQL statement, start time, duration, and waiting resources. Note: The "t" indicates that a query task has not been executed yet and is waiting for resources. The "f" indicates that the resources are being acquired and that the query task is being executed.

Run the following query command.

```
select * from stv_recents;
```

The following figure shows a command output example.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/20128/153631022511169_en-US.jpg)

## Cancel running queries {#section_zc4_kdl_z2b .section}

You can obtain information on running queries by querying the stv\_recents table. To cancel a running query, execute the following query command.

```
select cancel('query_id');
```

In parentheses is the query\_id of a running query.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/20128/153631022511170_en-US.jpg)

