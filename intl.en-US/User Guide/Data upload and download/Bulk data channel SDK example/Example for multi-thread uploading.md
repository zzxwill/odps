# Example for multi-thread uploading {#concept_xn5_24g_vdb .concept}

```
import java.io.IOException;
 import java.util.ArrayList;
 import java.util.Date;
 import java.util.concurrent.Callable;
 import java.util.concurrent.ExecutorService;
 import java.util.concurrent.Executors;
 import com.aliyun.odps.Column;
 import com.aliyun.odps.Odps;
 import com.aliyun.odps.PartitionSpec;
 import com.aliyun.odps.TableSchema;
 import com.aliyun.odps.account.Account;
 import com.aliyun.odps.account.AliyunAccount;
 import com.aliyun.odps.data.Record;
 import com.aliyun.odps.data.RecordWriter;
 import com.aliyun.odps.tunnel.TableTunnel;
 import com.aliyun.odps.tunnel.TunnelException;
 import com.aliyun.odps.tunnel.TableTunnel.UploadSession;
 class UploadThread implements Callable<Boolean> {
         private long id;
         private RecordWriter recordWriter;
         private Record record;
         private TableSchema tableSchema;
         public UploadThread(long id, RecordWriter recordWriter, Record record,
                         TableSchema tableSchema) {
                 this.id = id;
                 this.recordWriter = recordWriter;
                 this.record = record;
                 this.tableSchema = tableSchema;
         }
         @Override
         public Boolean call() {
                 for (int i = 0; i < tableSchema.getColumns().size(); i++) {
                         Column column = tableSchema.getColumn(i);
                         switch (column.getType()) {
                         Case bigint:
                                 record.setBigint(i, 1L);
                                 Break;
                         Case Boolean:
                                 record.setBoolean(i, true);
                                 break;
                         case DATETIME:
                                 record.setDatetime(i, new Date());
                                 break;
                         case DOUBLE:
                                 record.setDouble(i, 0.0);
                                 break;
                         case STRING:
                                 record.setString(i, "sample");
                                 break;
                         default:
                                 throw new RuntimeException("Unknown column type: "
                                                 + column.getType());
                         }
                 }
                 for (int i = 0; i < 10; i++) {
                         try {
                                 recordWriter.write(record);
                         } catch (IOException e) {
                                 recordWriter.close();
                                 e.printStackTrace();
                                 return false;
                         }
                 }
                 recordWriter.close();
                 return true;
         }
 }
 public class UploadThreadSample {
         private static String accessId = "<your access id>";
         private static String accessKey = "<your access Key>";
         private static String odpsUrl = "<http://service.odps.aliyun.com/api>";
         private static String tunnelUrl = "<http://dt.cn-shanghai.maxcompute.aliyun-inc.com>";
                         //The tunnelURL must be set if you need to connect internal network, otherwise, the system uses public network as default. The example shows the Tunnel Endpoint of classical network in HuaDong 2, for other regions, see Access domain and data centers.
         private static String project = "<your project>";
         private static String table = "<your table name>";
         private static String partition = "<your partition spec>";
         private static int threadNum = 10;
         public static void main(String args[]) {
                 Account account = new AliyunAccount(accessId, accessKey);
                 Odps odps = new Odps(account);
                 odps.setEndpoint(odpsUrl);
                 odps.setDefaultProject(project);
                 try {
                         TableTunnel tunnel = new TableTunnel(odps);
                         tunnel.setEndpoint(tunnelUrl);  //set tunnelUrl
                         PartitionSpec partitionSpec = new PartitionSpec(partition);
                         UploadSession uploadSession = tunnel.createUploadSession(project,
                                         table, partitionSpec);
                         System.out.println("Session Status is : "
                                         + uploadSession.getStatus().toString());
                         ExecutorService pool = Executors.newFixedThreadPool(threadNum);
                         ArrayList<Callable<Boolean>> callers = new ArrayList<Callable<Boolean>>();
                         for (int i = 0; i < threadNum; i++) {
                                 RecordWriter recordWriter = uploadSession.openRecordWriter(i);
                                 Record record = uploadSession.newRecord();
                                 callers.add(new UploadThread(i, recordWriter, record,
                                                 uploadSession.getSchema()));
                         }
                         pool.invokeAll(callers);
                         pool.shutdown();
                         Long[] blockList = new Long[threadNum];
                         for (int i = 0; i < threadNum; i++)
                                 blockList[i] = Long.valueOf(i);
                         uploadSession.commit(blockList);
                         System.out.println("upload success!") ;
                 } catch (TunnelException e) {
                         e.printStackTrace();
                 } catch (IOException e) {
                         e.printStackTrace();
                 } catch (InterruptedException e) {
                         e.printStackTrace();
                 }
         }
 }
```

The Tunnel Endpoint can be specified or left blank.

-   If specified, the uploading data goes through the specified Endpoint.
-   If not specified, the uploading data goes through public network.

