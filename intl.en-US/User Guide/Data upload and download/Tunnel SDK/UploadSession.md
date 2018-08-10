# UploadSession {#concept_ecn_pjg_vdb .concept}

**The UploadSession interface is defined as follows:**

```
public class UploadSession {
        UploadSession(Configuration conf, String projectName, String tableName,
            String partitionSpec) throws TunnelException;
        UploadSession(Configuration conf, String projectName, String tableName, 
            String partitionSpec, String uploadId) throws TunnelException;
        public void commit(Long[] blocks);
        public Long[] getBlockList();
        public String getId();
        public TableSchema getSchema();
        public UploadSession.Status getStatus();
        public Record newRecord();
        public RecordWriter openRecordWriter(long blockId);
        public RecordWriter openRecordWriter(long blockId, boolean compress);
        public RecordWriter openBufferedWriter();
        public RecordWriter openBufferedWriter(boolean compress);
    }
```

**Upload Objects description:**

-   **Life cycle**: Begins with the creation of the Upload instance and ends with the completion of an upload process.
-   **Create Upload instance**: An instance can be created either by Calling the Constructor or using the TableTunnel.
    -   Request mode: Synchronous.
    -   The server creates a session for this upload instance and  a unique UploadId is generated. Obtain this ID using the getId on the client.
-   **Upload data**:
    -   Request mode: Synchronous.
    -   Call the openRecordWriter method to generate a RecordWriter instance. The blockId identifies the data to be uploaded and indicates its location in the table within the value range \[0, 20000\]. If the data upload fails, use BlockId to re-upload it.
-   **View upload**:
    -   Request mode: Synchronous.
    -   Call getStatus to obtain the current upload status.
    -   Call getBlockList to obtain the successfully uploaded blockId  list. Compare the result with the upload blockId list to find and re-upload failed blockIds.
-   **End upload**:
    -   Request mode: Synchronous.
    -   Call the commit \(Long\[\]  blocks\) method. The blocks list shows successfully uploaded blocks. The server verifies this list.
    -   This function enhances data verification. If the provided block list does not match the block list on the server, an error occurs.
    -   If Commit fails, you can try again.
-   Seven kinds of status are described as follows:

-   UNKNOWN: The initial value when the server creates a session.
-   NORMAL: The upload object is created successfully.
-   CLOSING: The server changes the status to CLOSING when complete is called.
-   CLOSED: The upload is now complete. In other words, moving the data to the directory where the result table is located.
-   EXPIRED: The upload session is timed out.
-   CRITICAL: A service error has occurred.

**Note:** 

-   The blockIds in the same UploadSession must be unique.   In a single UploadSession, when you use a blockId to open RecordWriter, write a batch of data, call close, and then call commit.Do not use the same blockID to open another RecordWriter to write data.
-   The maximum size of a block is 100 GB, preferably more than 64 MB.
-   The threshold of each session on the server is 24 hours.
-   When data is being uploaded, each 8 KB of data written by the Writer triggers a network action. If no network actions are triggered within 120 seconds, the server closes the connection. In this case, open a new connection as the Writer becomes unavailable. 
-   We recommend that you use the openBufferedWriter interface to upload data. This interface does not show blockId details and contains an internal data cache for automatic retry upon failures. For more information, see the introductions and examples of TunnelBufferedWriter.

