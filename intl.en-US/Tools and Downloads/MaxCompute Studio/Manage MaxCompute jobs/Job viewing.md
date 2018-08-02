# Job viewing {#concept_orl_vtg_vdb .concept}

MaxCompute Studio supports viewing information of MaxCompute running instances submitted by the **current user**, including the running status, job type, and start and stop time.

## Open Job Explorer {#section_ixg_n5g_vdb .section}

If **Job Explorer View** is not displayed on **Dock** on the left, open Job Explorer by choosing **View \> ** \> **Tool \> Windows \> ** \> **MaxCompute Job Explorer.**  

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/12137/2070_en-US.png)

## View all job instances in a project {#section_kxg_n5g_vdb .section}

Job Explorer allows you to query submitted job lists by status.

 

Click the date drop-down box to select another date.

 

Click Refresh to obtain the job list.

**Note:** By default, only the first 1,000 jobs that meet the conditions are displayed. If more than 1,000 jobs meet the conditions, update the filtering conditions.

## Sort the job list {#section_nxg_n5g_vdb .section}

You can click the column name in the job list to sort the jobs.

 

## Job queue {#section_pxg_n5g_vdb .section}

If a job in running status is waiting for scheduling in a queue, the job’s location in the queue and global priority is displayed in the job list.

 

**Note:** The job status and queue location on the Running Instances tab are automatically updated. After a job finishes, it is removed from the list.

## Save job logs {#section_rxg_n5g_vdb .section}

Currently, Logview logs of a job are saved for seven days by default. If you want to save some important Logview logs for a longer period and view them in the future, you can save them locally.

Double-click a job in the list to display the job details on the right. Click **Save** on the toolbar to save the logs to your local host.

 

You can set the path for saving the log file on the Setting tab of MaxCompute Studio.

 

