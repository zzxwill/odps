# Example for BufferedWriter uploading {#concept_npv_cpg_vdb .concept}

```
// Initializes MaxCompute and Tunnel code
RecordWriter writer = null;
TableTunnel.UploadSession uploadSession = tunnel.createUploadSession(projectName, tableName);
try {
  int i = 0;
  // Generates TunnelBufferedWriter instance
  writer = uploadSession.openBufferedWriter();
  Record product = uploadSession.newRecord();
  for (String item : items) {
    product.setString("name", item);
    product.setBigint("id", i);
    // Calls the Write interface to write data
    writer.write(product);
    i += 1;
  
} finally {
  if (writer ! = null) {
    // Closes TunnelBufferedWriter
    writer.close();
  

// Submits data via uploadSession to end the upload process
uploadSession.commit();
```

