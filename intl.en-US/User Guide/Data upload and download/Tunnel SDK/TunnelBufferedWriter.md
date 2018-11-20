# TunnelBufferedWriter {#concept_ns5_qlg_vdb .concept}

To complete the uploading process, follow these steps:

1.  Divide the data.
2.  Specify a block ID for each data block by calling the openRecordWriter \(id\).
3.  Use one or more threads to upload the blocks. Even if a single block upload fails, you must re-upload all the blocks.
4.  After uploading all blocks, provide the uploaded blockID list to the server for verification.  Call session.commit\(\[1,2,3,…\]\) to complete this action.

    The connection time-out and other limits on the server block manager complicate the upload process logic. So, to simplify the process, SDK provides an enhanced RecordWriter—TunnelBufferWriter interface.


**This interface is defined as follows:**

```
public class TunnelBufferedWriter implements RecordWriter {
        public TunnelBufferedWriter(TableTunnel.UploadSession session, CompressOption option) throws IOException;
        public long getTotalBytes();
        public void setBufferSize(long bufferSize);
        public void setRetryStrategy(RetryStrategy strategy);
        public void write(Record r) throws IOException;
        public void close() throws IOException;
    }
```

**Parameters:**

-   Life cycle: Begins with a RecordWriter creation and ends with the completion of data upload.
-   Create TunnelBufferedWriter instance: Call  openBufferedWriter interface ofUploadSession to create an instance.
-   Data upload: Call the Write interface. Data is first written to the local cache. Once the cache is full, the data is submitted to the server in batches to avoid connection time-out. Automatic retries are supported if the upload fails.
-   End upload: Call the close interface, and then call the Commit interface of UploadSession to complete the upload process.
-   Buffer control: Use the setBufferSize interface to modify the size of memory \(bytes\), occupied by the buffer preferably greater than 64 MB\(default\) to prevent the server from generating numerous small files that may critically impact the performance. The default value is generally used for this parameter without additional settings.
-   Retry policy setting: You have three retry avoidance policies to choose from: EXPONENTIAL\_BACKOFF, LINEAR\_BACKOFF, and CONSTANT\_BACKOFF.  For example: The following code segment sets the number of Write retries to 6. To avoid unnecessary retries, each retry is performed only after exponentially ascending intervals of 4s, 8s, 16s, 32s, 64s, and 128s. This is the default configuration and generally cannot be changed.

    ```
    RetryStrategy retry 
      = new RetryStrategy(6, 4, RetryStrategy.BackoffStrategy.EXPONENTIAL_BACKOFF)
    writer = (TunnelBufferedWriter) uploadSession.openBufferedWriter();
    writer.setRetryStrategy(retry);
    ```


