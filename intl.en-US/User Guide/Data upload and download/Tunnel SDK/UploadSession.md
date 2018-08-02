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
    
```

**Upload objects:**

-   **Life cycle**: Starts from the creation of the Upload instance and ends with the completion of the upload process.
-   **Create Upload instance**: You can create an instance by **Calling the Constructor** or by using **TableTunnel**.
    -   Request method: Synchronous.
    -   The server creates a session for this upload instance and  generates a unique UploadId for the upload. Obtain this ID using the getId on the client.
-   **Upload data**:
    -   Request method: Synchronous.
    -   Call the openRecordWriter method to generate a RecordWriter instance. The blockId identifies the data to be uploaded and describes its location in the table within the value range of \[0,20000\]. When data fails to be uploaded, you can reupload it based on the blockId.
-   **View upload**:
    -   Request method: Synchronous.
    -   Call getStatus to obtain the current upload status.
    -   Call getBlockList to obtain the successfully uploaded blockId list.  You can compare this with the upload blockId list to find and reupload failed blockIds.
-   **End upload**:
    -   Request method: Synchronous.
    -   Call the Commit \(Long\[\] blocks\) method.  The blocks list shows successfully uploaded blocks. The server verifies this list.
    -   This function enhances data verification. If the provided block list does not match the block list on the server, an error occurs.
    -   If Commit fails, you can try again.
-   Seven kinds of status are described as follows:

-   UNKNOWN: The initial value when the server creates a session.
-   NORMAL: The upload object is created successfully.
-   CLOSING: When the complete method \(end upload\) is called, the server changes the status to CLOSING.
-   CLOSED: After completing upload \(which means to move data to the directory where result table is\).
-   EXPIRED: The upload has timed out.
-   CRITICAL: Service error.

**Note:** 

-   The blockIds in the same UploadSession must be unique.  In a single UploadSession, when you use a blockId to open RecordWriter, write a batch of data, call close, and then call Commit, you cannot use the same blockId to open another RecordWriter to write data.
-   The maximum size of a block is 100 GB, preferably more than 64 MB.
-   The life cycle of each session on the server is 24 hours.
-   When data is being uploaded, each 8 KB of data written by the Writer triggers a network action. If no network actions are triggered within 120 seconds, the server closes the connection. In this case, the Writer becomes unavailable and you must open a new one.
-   We recommend that you use the openBufferedWriter interface to upload data. This interface does not show blockId details and contains an internal data cache for automatic retry upon failures. For more information, see the introductions and examples of TunnelBufferedWriter.

