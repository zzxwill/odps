# Job instance {#concept_i1t_svg_vdb .concept}

## View a job instance {#section_jrj_zsn_vdb .section}

Studio supports the following two ways to view MaxCompute job instances:

1.  **Open the details of a job in read-only mode  using a Logview URL or local offline Logview file.**

    Users of MaxCompute will be familiar with using Logview to view the details of a job. Using Logview, you can also view the status of tasks submitted by other users in other projects. You can also view the details of any job  by entering a valid Logview URL in Studio.

    In the menu bar, select MaxCompute \> Open  Logview. Valid Logview URLs in the Clipboard are automatically copied to the dialog box displayed. Alternatively, you can select to export the local offline Logview file.

2.  In [Job Explorer](reseller.en-US/Tools and Downloads/MaxCompute Studio/Manage MaxCompute jobs/Job viewing.md), double-click a MaxCompute instance to view its details. You can also right-click the instance and select Open.

## Job details view {#section_mrj_zsn_vdb .section}

The job details view page comprises a toolbar at the top, a properties bar on the left, and a detailed view on the right. The detailed view consists of seven views:

-   **Execution view**: Displays the overall information of a job in the form of a DAG. You can view the dependencies and detailed execution plans for each subtask.
-   **Timeline view**: Displays the execution timeline of a job, allows you to view this timeline in different granularities, and provides a number of filters.
-   **Details**: Displays the details of a job in table  view, including the subtask list, the worker list for each subtask, the volume of data processed by workers, the execution time, and the status.
-   **Script**: Displays the corresponding SQL statement and parameter configuration for when a job is submitted.
-   **Summary \(JSON\)**: Displays the running details of a job in JSON format.
-   **Result**: Displays the running results of a job.
-   **Analysis**: Provides scatter plots, long tail distributions, and skewed data charts to show the results of a job execution.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/12138/15448013752388_en-US.png)

## Toolbar {#section_sff_zwn_vdb .section}

**Collapse pages on left/right**

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/12138/15448013752391_en-US.png)

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/12138/15448013752392_en-US.png)

Used to expand or collapse views on the left and right, allowing you to focus on a particular view.

**Stop a job**

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/12138/15448013762393_en-US.png)

Used to stop a job that is being executed. You need permission \(owner or administrator\) to do so.

**Refresh details**

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/12138/15448013762394_en-US.png)

The basic information of a running job, such as its status and quota, is refreshed automatically, but the detailed view on the right is not. If you want the most up-to-date details, you have to refresh them manually.

**Copy Logview**

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/12138/15448013762395_en-US.png)

Copies the corresponding Logview of a job to the Clipboard.

**Open job details in a browser**

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/12138/15448013762396_en-US.png)

Generates a Logview  URL and opens it in a browser.

## Store job details locally {#section_asj_zsn_vdb .section}

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/12138/15448013762397_en-US.png)

Stores the job details as a local file.

**Auto refresh**

![](images/2398_en-US.png)

With this function enabled, Studio automatically refreshes all details of the executed job on a timed basis.

## Basic information page {#section_esj_zsn_vdb .section}

Displays the basic information of a job, including its ID, Owner, status, start and end time, computing resource usage,  input, output, and so on.  The basic information of a running job is automatically refreshed on a timed basis. The input and output items list the input and output tables of the job. Double-click a table name to view its details.

## Execution view {#section_fsj_zsn_vdb .section}

**Execution view** is the most commonly used tool to display the dependency between Fuxi Job, Fuxi Task, and Operation.  It also provides a series of auxiliary tools such as job playback, progress view, heat map view, and so on. The execution view is useful for troubleshooting problems.

The execution view can display job dependencies in three dimensions: the Fuxi Job layer, Fuxi Task layer, and Operation layer. Click the breadcrumb or the up arrow to switch between dimensions. By default, the dependency in the Fuxi Task layer is displayed.  

**Note:** For non-SQL jobs, only those in the Fuxi Job and Fuxi Task layers can be displayed. Operation layer jobs cannot be displayed. 

 

-   Fuxi Job layer:

    Click the breadcrumb or the up arrow to switch between dimensions. By default, the dependency in the Fuxi Task layer is displayed. Fuxi To open the Fuxi Job layer, click the MaxCompute Job in the breadcrumb or the up arrow in the Fuxi Task layer. Fuxi Job workers include the names, start times, and end times of Fuxi Tasks. To go to the Fuxi Task layer, double-click any Fuxi Job worker.

     


-   Fuxi Task layer:

    In the event there is more than one Fuxi Job, the Fuxi Task layer of the last Fuxi Job is opened by default. This layer can display  the dependency, input/output tables, and partitions of Fuxi Tasks.  Once the job has ended, click the drop-down box in the toolbar to switch between views, including those for progress, input and output heat maps, and Task time and Instance heat maps.  The progress view displays the progress of completion for the worker. The heat map view uses colors to distinguish between different worker heats. Double-click any Fuxi  Task to open its Operation layer. Right-click to open the Operation layers of all Fuxi Tasks.

     

      Contents of a Fuxi Task worker:

    ```
    
    1.Instance Count: a/b/c indicates that at a certain point in time, the number of running subtask instances is a, the number of completed task instances is b, and the total number of task instances is c.
    
    2.I/O records: Similarly, this displays the number of input records and output records at a certain point in time.
    
    3.Percentage and orange progress bar: Indicates the running status of the task. It is obtained by analyzing the running subtask instances.
    
    4.The line connecting subtasks shows the number of output records.  The arrow indicates the data flow direction.
    ```

-   Operation layer

    The Operation layer reveals how Fuxi Tasks run internally. By clicking a worker, all Operation information is displayed.

     


## Job playback {#section_osj_zsn_vdb .section}

Studio supports the job playback function. The history of a job can be reviewed within 12s, just like playing a media file. This function helps you understand the running status of a MaxCompute instance in different time points, rapidly determine the sub-task-level running sequence and time consumed, master the key path for executing a job, and accordingly optimize sub-tasks that run slowly.

-   Click **\>** to start playing the job, and click \> again to pause.  You can also manually drag the progress bar.

-   The start time of the job is displayed on the left side of the progress bar, the playing time in the middle, and the end time on the right.


 

**Note:** The playback function only estimates the volume of I/O data at a certain point in time by measuring the time, thereby determining the progress of completion. This does not represent the actual volume of I/O data.  Jobs with a running status do not support playback.

## Timeline view {#section_rsj_zsn_vdb .section}

Displays in the form of a Gantt chart detailed data for the distributed execution of a job. You can adjust the display granularity to show all computing workers in a Gantt chart.

 

Gantt charts can be used to display the time bottlenecks and long-tail workers of running jobs.  Multiple filters are also provided that help select the key paths, the largest data worker, and the longest time worker for job execution.

## Job details page {#section_tsj_zsn_vdb .section}

Mainly applies to SQL DML jobs, displaying their Fuxi Task lists and computing worker lists on the compute cluster.

 

One job typically corresponds to one or more Fuxi Jobs. Each Fuxi Job is divided into multiple Fuxi Tasks \(stages\), and each Fuxi Task comprises multiple Fuxi instances \(workers\).  

Right-click a Fuxi instance, and the displayed menu allows you to view the standard output, standard errors, and Debug Info.

 

 

## Analysis page {#section_xsj_zsn_vdb .section}

Displays the long-tail workers and skewed data workers of jobs.  Displays worker scatter plots and column charts to help diagnose job execution bottlenecks.

 

 

Scatter plots and column charts allow you to call the details view page from their workers and check Fuxi instance details.

## Results page {#section_atj_zsn_vdb .section}

The results page displays different pages based on the job type and the parameters configured at the time the job was submitted.

-   Select statement and set odps.sql.select.output.format = HumanReadable. This displays the result in text format
-   Select statement but do not set the output format. This displays the result in table format
-   For scripts where data is exported to the table, the output table name and the link that redirects to the table details are displayed
-   For abnormal jobs, the results page limits the details on abnormalities

