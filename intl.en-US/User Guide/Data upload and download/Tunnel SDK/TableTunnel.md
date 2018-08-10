# TableTunnel {#concept_exm_f3g_vdb .concept}

TableTunnel is an ingress class that accesses the MaxCompute Tunnel service.  The TableTunnel.UploadSession interface is a session that uploads data to the MaxCompute table. The TableTunnel.DownloadSession interface is a session that downloads data to the MaxCompute table.

**This interface is defined as follows:**

```
public class TableTunnel {
 public DownloadSession createDownloadSession(String projectName, String tableName);
 public DownloadSession createDownloadSession(String projectName, String tableName, PartitionSpec partitionSpec);
 public UploadSession createUploadSession(String projectName, String tableName);
 public UploadSession createUploadSession(String projectName, String tableName, PartitionSpec partitionSpec);
 public DownloadSession getDownloadSession(String projectName, String tableName, PartitionSpec partitionSpec, String id);
 public DownloadSession getDownloadSession(String projectName, String tableName, String id);
 public UploadSession getUploadSession(String projectName, String tableName, PartitionSpec partitionSpec, String id);
 public UploadSession getUploadSession(String projectName, String tableName, String id);
 }
```

**Description:**

-   Lifecycle: It is the TableTunnel life cycle, begins with a TableTunnel instance creation and ends with the completion of the process.
-   PublicClassTableTunnel: A method of creating uploading and downloading objects.
-   Session: It is a process for uploading and downloading table or a partition. A session consists of one or more HTTP  Requests to the Tunnel RESTful API.
-   Uploading session: The uploading session of TableTunnel is INSERT  INTO semantics, which means that sessions that upload the same table or partition do not affect each other. The upload of each session is located in different directories.
-   Block ID: It is the corresponding file name. In an uploading session, each RecordWriter corresponds to an HTTP Request, identified by a block ID and corresponds to a file on the service side.
-   RecordWriter: In a session, opening RecordWriter multiple times with the same block ID results in overwriting. The data uploaded by the last RecordWriter calling close\(\) is retained.  This feature can be used for retransmissions when block uploading fails.

**TableTunnel Interface implementation process:**

1.  RecordWriter.write\(\) uploads data to a file in a temporary directory.
2.  RecordWriter.close\(\) moves the preceding file from the temporary directory to the data directory.
3.  Session.commit\(\) moves all files in the corresponding data directory to directory where the corresponding table is located, and updates the table meta. This means that data moving into the table is visible to other MaxCompute tasks \(including SQL, MR\).

**Limits:**

-   The range of block id is 0 to 20000. The data size uploaded by a single block is limited to 100 GB.
-   The session timeout is 24 hours.  Split the massive data into multiple sessions, if the transmission time is supposed to exceed the threshold that is 24 hours.
-   The HTTP Request timeout for RecordWriter  is 120 seconds.  If no data flow through the HTTP connection is observed within 120 seconds, the service automatically closes the connection.

    **Note:** By default, HTTP has a buffer of 8 KB.  Therefore, it is difficult to determine the data flow through an HTTP connection when you call RecordWriter.write\(\) each time.   Moreover, TunnelRecordWriter.flush\(\) can forcibly clear the data from the buffer.

-   When logs are being written into MaxCompute, the RecordWriter can be easily timed out as the flow of the data is unpredictable.  Note:
    -   We do not recommend using a RecordWriter for all types of data. Because each RecordWriter corresponds to a file resulting into numerous small files, critically impacting MaxCompute performance.
    -   We recommend calling a RecordWriter to write data in a batch when your code cache data size exceeds 64 MB.
-   The threshold for RecordReader timeout is 300 seconds.

