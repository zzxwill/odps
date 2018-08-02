# TableTunnel {#concept_exm_f3g_vdb .concept}

TableTunnel is an ingress class that accesses the MaxCompute Tunnel service.  The TableTunnel.UploadSession interface is a session that uploads data to the MaxCompute table. The TableTunnel.DownloadSession interface is a session that downloads data to the MaxCompute table.

**This interface is defined as follows:**

```
public class tabletunnel {
 public DownloadSession createDownloadSession(String projectName, String tableName);
 public DownloadSession createDownloadSession(String projectName, String tableName, PartitionSpec partitionSpec);
 public UploadSession createUploadSession(String projectName, String tableName);
 public UploadSession createUploadSession(String projectName, String tableName, PartitionSpec partitionSpec);
 public DownloadSession getDownloadSession(String projectName, String tableName, PartitionSpec partitionSpec, String id);
 public DownloadSession getDownloadSession(String projectName, String tableName, String id);
 public UploadSession getUploadSession(String projectName, String tableName, PartitionSpec partitionSpec, String id);
 public UploadSession getUploadSession(String projectName, String tableName, String id);
 
```

**Description:**

-   Life cycle: Starts from TableTunnel instance creation and ends with the completion of the program.
-   Provides the method for creating uploading and downloading objects.
-   The process of uploading and downloading a table or partition is called as a session.  A session consists of one or more HTTP  requests to the Tunnel RESTful API.
-   The uploading session of TableTunnel is INSERT  INTO semantics, which means that sessions that upload the same table or partition do not affect each other. The upload of each session is located in different directories.
-   In an uploading session, each RecordWriter corresponds to an HTTP Request, identified by a block id, corresponding to a file on the service side \(The block id is the corresponding file name\).
-   In a session, opening RecordWriter multiple times with the same block id  results in overwriting. The data uploaded by the last RecordWriter calling close\(\) is retained.  This feature can be used for retransmissions when block uploading fails.

**The TableTunnel Interface implementation process is as follows:**

1.  RecordWriter.write\(\) uploads data to a file in a temporary directory.
2.  RecordWriter.close\(\) moves the preceding file from the temporary directory to the data directory.
3.  Session.commit\(\) moves all files in the corresponding data directory to directory where the corresponding table resides, and updates the table meta. This means that data moving into the table is visible to other MaxCompute tasks \(such as SQL, MR\).

**The TableTunnel Interface restrictions are as follows:**

-   The range of block id is 0 to 20000. The data size uploaded by a single block is limited to 100 GB.
-   The session timeout is 24 hours.  If massive data results in the transmission time exceeding 24 hours, you must split them into multiple sessions.
-   The HTTP Request timeout for RecordWriter  is 120 seconds.  If there is no data flow through the HTTP connection within 120 seconds, the service automatically closes the connection. 

    **Note:**  It should be noted that there is an 8 KB buffer for HTTP itself,  so we cannot make sure that there is data flow through an HTTP connection when you call RecordWriter.write\(\) each time.  TunnelRecordWriter.flush\(\) can forcibly flush data from the buffer.

-   For the scenario that logs are written into MaxCompute, the RecordWriter can easily time out because the data arrives unpredictably.  At this time:
    -   We do not recommend that you open a RecordWriter for each piece of data\(Because each RecordWriter corresponds to a file, too many small files can seriously affect the MaxCompute performance\).
    -   We recommend that you can call a RecordWriter to write data in a batch when your code cache data size exceeds 64 MB.
-   RecordReader timeout is 300 seconds.

