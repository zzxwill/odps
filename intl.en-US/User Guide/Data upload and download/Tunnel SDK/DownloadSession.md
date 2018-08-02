# DownloadSession {#concept_yvg_zkg_vdb .concept}

**This DownloadSession interface is defined as follows:**

```
public class DownloadSession {
        DownloadSession(Configuration conf, String projectName, String tableName,
            String partitionSpec) throws TunnelException
        DownloadSession(Configuration conf, String projectName, String tableName,
            String partitionSpec, String downloadId) throws TunnelException
        public String getId()
        public long getRecordCount()
        public TableSchema getSchema()
        public DownloadSession.Status getStatus()
        public RecordReader openRecordReader(long start, long count)
        public RecordReader openRecordReader(long start, long count, boolean compress)
    
```

**Download objects:**

-   **Life cycle**: Starts from the creation of the Download instance and ends with the completion of data download.
-   **Create Download instance**: You can create an instance by Calling the Constructor or by using TableTunnel.
    -   Request method: Synchronous.
    -   The server creates a session for this download instance and generates a unique DownloadId for the download. Obtain this ID using the getId on the client.
    -   This operation results in high costs. The server creates an index for the data files. In case of a large amount of files, this may take a long time.
    -   At the same time, the server returns the total number of Records and starts multiple concurrent downloads based on this value.
-   **Download data**:
    -   Request method: Asynchronous.
    -   Call the openRecordReader method to generate a RecordReader instance. “start” identifies the start position of downloading this record which cannot be less than  zero. “count” specifies the number of records for this download which must be greater than zero.
-   **View download**:
    -   Request method: Synchronous.
    -   Call getStatus to obtain the current download status.
-   The four types of status are as follows:
    -   UNKNOWN: The initial value when the server creates a session.
    -   NORMAL: Create Download object succeeds.
    -   CLOSED: After downloading.
    -   EXPIRED: The download has timed out.

