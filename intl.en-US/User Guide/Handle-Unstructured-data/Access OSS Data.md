# Access OSS Data {#concept_tzd_tlg_vdb .concept}

The following section details how to access OSS data in MaxCompute.

## Authorization with STS Mode {#section_uv5_1fb_wdb .section}

Authorize OSS data permission to MaxCompute account in advance, in order that MaxCompute can directly access the OSS. You can authorize permissions in the following two ways:

-   **When the MaxCompute and OSS owner are the same account**, you can directly log on Alibaba Cloud account and click [here to complete authorization](https://ram.console.aliyun.com/?spm=5176.100239.blogcont281191.24.uJg9dR#/role/authorize?request=%7B%22Requests%22:%20%7B%22request1%22:%20%7B%22RoleName%22:%20%22AliyunODPSDefaultRole%22,%20%22TemplateId%22:%20%22DefaultRole%22%7D%7D,%20%22ReturnUrl%22:%20%22https:%2F%2Fram.console.aliyun.com%2F%22,%20%22Service%22:%20%22ODPS%22%7D).
-   Custom authorization.
    1.  Firstly, you must authorize MaxCompute permission to access OSS in [RAM](https://www.alibabacloud.com/zh/product/ram). Log on to the [RAM console](https://account.alibabacloud.com/login/login.html)\(if MaxCompute and OSS are not the same account, you must log on to OSS account to authorize\). Create a role through [Role Management](https://ram.console.aliyun.com/#/role/list) in the console whose name likes AliyunODPSDefaultRole or AliyunODPSRoleForOtherUser.
    2.  Modify the policy content of role as follows:

        ```
        --When the MaxCompute and OSS owner are the same account:
        
        "Statement": [
        
         "Action": "sts:AssumeRole",
         "Effect": "Allow",
         "Principal": {
           "Service": [
             "odps.aliyuncs.com"
           
         }
        
        
        "Version": "1"
        
        --When the MaxCompute and OSS owner are not the same account:
        
        "Statement": [
        
         "Action": "sts:AssumeRole",
         "Effect": "Allow",
         "Principal": {
           "Service": [
             "MaxCompute's Owner account: id@odps.aliyuncs.com"
           
         
        
        
        "Version": "1"
        
        ```

    3.  Authorize the role necessary permissions **AliyunODPSRolePolicy** to access OSS . For example:

        ```
        
        "Version": "1",
        "Statement": [
        
         "Action": [
           "oss:ListBuckets",
           "oss:GetObject",
           "oss:ListObjects",
           "oss:PutObject",
           "oss:DeleteObject",
           "oss:AbortMultipartUpload",
           "oss:ListParts"
         
         "Resource ":"*",
         "Effect": "Allow"
        
        
        
        --You can customize other permissions.
        ```

    4.  Authorize the permission **AliyunODPSRolePolicy** to this role.

## Read OSS Data with Built-in Extractor {#section_ukx_nfb_wdb .section}

When accessing external data sources, you must use different custom extractors. You can also use MaxCompute’s internal extractor to read conventionally-formatted data stored in [OSS](https://www.alibabacloud.com/zh/product/oss). You only need to create an external table and use this table as the source table for query operations.

In this example, assume that you have a CSV data file in [OSS](https://www.alibabacloud.com/zh/product/oss). The endpoint is `oss-cn-shanghai-internal.aliyuncs.com`, the bucket is `oss-odps-test`, and the data file is stored in /demo/vehicle.csv.

## Create External table {#section_b23_tfb_wdb .section}

Use the following statements to create an external table:

```
CREATE EXTERNAL TABLE IF NOT EXISTS ambulance_data_csv_external

vehicleId int,
recordId int,
patientId int,
calls int,
Maid double,
locationLongtitue double,
recordTime string,
direction string

STORED BY 'com.aliyun.odps.CsvStorageHandler' -- (1)
WITH SERDEPROPERTIES (
 'odps.properties.rolearn'='acs:ram::xxxxx:role/aliyunodpsdefaultrole'
) -- (2)
LOCATION 'oss://oss-cn-shanghai-internal.aliyuncs.com/oss-odps-test/Demo/'; -- (3)(4)
```

The above statement is described below:

-   `com.aliyun.odps.CsvStorageHandler` is the built-in `StorageHandler` for processing CSV-format files. It defines how CSV files are read and written. You only need to specify this name. The relevant logic is implemented by the system.
-   The information in `odps.properties.rolearn` comes from the `Arn` information of `AliyunODPSDefaultRole` in RAM. You can get it through the [role details](https://ram.console.aliyun.com/#/role/detailAliyunODPSDefaultRole/info) in the RAM console.
-   You must specify an OSS directory for LOCATION. By default, the system reads all the files in this directory.
    -   We recommend you use the domain name of the intranet, to avoid incurring fees for the OSS data-flow.
    -   We recommend that the region you store the OSS data is the same as the region you open MaxCompute. Because MaxCompute can only be deployed in some regions, cross-regional data connectivity cannot be guaranteed.
    -   OSS connection format is `oss://<your-access-id>:<your-secret-key>@oss-xx-xxxx-internal.aliyuncs.com/<bucketname>/<directoryname>/`. You do not have to add a file name after the directory. Some common errors are shown as follows:

        ```
        http://oss-odps-test.oss-cn-shanghai-internal.aliyuncs.com/Demo/ -- HTTP connection is not supported.
        https://oss-odps-test.oss-cn-shanghai-internal.aliyuncs.com/Demo/  -- HTTPS connection is not supported.
        oss://oss-odps-test.oss-cn-shanghai-internal.aliyuncs.com/Demo  -- The connection address is incorrect.
        oss://oss://oss-cn-shanghai-internal.aliyuncs.com/oss-odps-test/Demo/vehicle.csv  -- You do not need to specify the file name.
        ```

-   In the MaxCompute system, external tables only record the associated OSS directory. If you DROP \(delete\) this table, the corresponding `LOCATION` data is not deleted.

    For more information about external tables, see [DDL statements](intl.en-US/User Guide/SQL/DDL SQL.md).


If you want to view the created external table structure, run the following statement:

```
desc extended <table_name>;
```

In the returned information, Extended Info contains external tables information such as StorageHandler and Location.

## Query an External Table {#section_ljx_ggb_wdb .section}

After creating an external table, you can use it as a normal table. Assume the data in `/demo/vehicle.csv` is:

```
1,1,51,1,46.81006,-92.08174,9/14/2014 0:00,S
1,2,13,1,46.81006,-92.08174,9/14/2014 0:00,NE
1,3,48,1,46.81006,-92.08174,9/14/2014 0:00,NE
1,4,30,1,46.81006,-92.08174,9/14/2014 0:00,W
1,5,47,1,46.81006,-92.08174,9/14/2014 0:00,S
1,6,9,1,46.81006,-92.08174,9/14/2014 0:00,S
1,7,53,1,46.81006,-92.08174,9/14/2014 0:00,N
1,8,63,1,46.81006,-92.08174,9/14/2014 0:00,SW
1,9,4,1,46.81006,-92.08174,9/14/2014 0:00,NE
1,10,31,1,46.81006,-92.08174,9/14/2014 0:00,N
```

Run the following SQL statement:

```
select recordId, patientId, direction from ambulance_data_csv_external where patientId > 25;
```

**Note:** Currently, external table can only be operated through MaxCompute SQL. MaxCompute MapReduce cannot operate the external table.

This statement submits a job, scheduling the built-in CSV extractor to read and process data from OSS. The result is as follows:

```

| recordId | patientId | direction |

| 1 | 51 | S |
| 3 | 48 | NE |
| 4 | 30 | W |
| 5 | 47 | S |
| 7 | 53 | N |
| 8 | 63 | SW |
| 10 | 31 | N |

```

## Read OSS Data Using a Custom Extractor {#section_lbc_ngb_wdb .section}

When OSS data is in a complex format, and the built-in extractor cannot meet your requirements, you must use a custom extractor to read data from OSS files.

For example, assume you have a TXT data file that is not in CSV format, and `|` is used as the column delimiter between records. For example, the data in `/demo/SampleData/CustomTxt/AmbulanceData/vehicle.csv` is:

```
1|1|51|1|46.81006|-92.08174|9/14/2014 0:00|S
1|2|13|1|46.81006|-92.08174|9/14/2014 0:00|NE
1|3|48|1|46.81006|-92.08174|9/14/2014 0:00|NE
1|4|30|1|46.81006|-92.08174|9/14/2014 0:00|W
1 | 5 | 47 | 1 | 46.81006 |-92.08174 | 9/14/2014 0: 00 | S
1|6|9|1|46.81006|-92.08174|9/14/2014 0:00|S
1|7|53|1|46.81006|-92.08174|9/14/2014 0:00|N
1|8|63|1|46.81006|-92.08174|9/14/2014 0:00|SW
1|9|4|1|46.81006|-92.08174|9/14/2014 0:00|NE
1|10|31|1|46.81006|-92.08174|9/14/2014 0:00|N
```

-   **Define an Extractor**

    Write a common extractor, using the delimiter as the parameter. This allows you to process all text files with similar formats. Examples are as follows:

    ```
    
     * Text extractor that extract schematized records from formatted plain-text(csv, tsv etc.)
     
    public class TextExtractor extends Extractor {
      private InputStreamSet inputs;
      private String columnDelimiter;
      private DataAttributes attributes;
      private BufferedReader currentReader;
      private boolean firstRead = true;
      public TextExtractor() {
        // default to ",", this can be overwritten if a specific delimiter is provided (via DataAttributes)
        this.columnDelimiter = ",";
      
      // no particular usage for execution context in this example
      @Override
      public void setup(ExecutionContext ctx, InputStreamSet inputs, DataAttributes attributes) {
        this.inputs = inputs; // inputs is an InputStreamSet, each call to next() returns an InputStream. This InputStream can read all the content in an OSS file.
        this.attributes = attributes;
        // check if "delimiter" attribute is supplied via SQL query
        String columnDelimiter = this.attributes.getValueByKey("delimiter"); //The delimiter parameter is supplied by a DDL statement.
        if ( columnDelimiter ! = null)
        
          this.columnDelimiter = columnDelimiter;
        
        // note: more properties can be inited from attributes if needed
      
      @Override
      public Record extract() throws IOException {//extractor() calls return one record, corresponding to one record in an external table.
        String line = readNextLine();
        if (line == null) {
          return null;  // A return value of NULL indicates that this table has no readable records.
        
        return textLineToRecord(line);  // textLineToRecord splits a row of data into multiple columns according to the delimiter.
      
      @Override
      public void close(){
        // no-op
      
    
    ```

    See [here](https://github.com/aliyun/aliyun-odps-java-sdk/blob/master/odps-sdk-impl/odps-udf-example/src/main/java/com/aliyun/odps/udf/example/text/TextExtractor.java) for a complete implementation of textLineToRecord splitting data.

    **Define StorageHandler**

    A StorageHandler acts as a centralized portal for custom external table logic.

    ```
    package com.aliyun.odps.udf.example.text;
    public class TextStorageHandler extends OdpsStorageHandler {
      @Override
      public Class<? extends Extractor> getExtractorClass() {
        return TextExtractor.class;
      
      @Override
      public Class<? extends Outputer> getOutputerClass() {
        return TextOutputer.class;
      
    
    ```

    **Compiling and Packaging**

    Compile your custom code into a package and upload it to MaxCompute.

    ```
    add jar odps-udf-example.jar;
    ```

-   **Create External Table**

    Similar to using the built-in extractor, first, you must create an external table. The difference is that, when specifying the external table access data, you must use a custom StorageHandler.

    Use the following statements to create an external table:

    ```
    CREATE EXTERNAL TABLE IF NOT EXISTS ambulance_data_txt_external
    
    vehicleId int,
    recordId int,
    patientId int,
    calls int,
    locationLatitute double,
    locationLongtitue double,
    recordTime string,
    direction string
    
    STORED BY 'com.aliyun.odps.udf.example.text.TextStorageHandler'   --STORED BY specifies the custom StorageHandler class name.
      with SERDEPROPERTIES (
    'delimiter'='\\|', -- SERDEPROPERITES can specify parameters, these parameters are passed through the DataAttributes to the Extractor code.
    'odps.properties.rolearn'='acs:ram::xxxxxxxxxxxxx:role/aliyunodpsdefaultrole'
    
    LOCATION 'oss://oss-cn-shanghai-internal.aliyuncs.com/oss-odps-test/Demo/SampleData/CustomTxt/AmbulanceData/'
    USING 'odps-udf-example.jar'; --You must also specify the Jar package containing the class definition.
    ```

-   **Query an External Table**

    Run the following SQL statement:

    ```
    select recordId, patientId, direction from ambulance_data_txt_external where patientId > 25;
    ```


## Read Unstructured Data by Using a Custom Extractor {#section_kqm_bhb_wdb .section}

Previously, you can use the built-in extractor or a custom extractor to conveniently process CSV and other text data stored in OSS. Next, using audio data \(.wav format files\) as an example, the following shows how to use a custom extractor to access and process non-text files in OSS.

**Here, starting from the last SQL statement, we introduce the use of MaxCompute SQL as a portal to process audio files stored in OSS.**

Create the External table SQL as follows:

```
CREATE EXTERNAL TABLE IF NOT EXISTS speech_sentence_snr_external

sentence_snr double,
id string

STORED BY 'com.aliyun.odps.udf.example.speech.SpeechStorageHandler'
WITH SERDEPROPERTIES (
    'mlfFileName'='sm_random_5_utterance.text.label' ,
    'speechSampleRateInKHz' = '16'

LOCATION 'oss://oss-cn-shanghai-internal.aliyuncs.com/oss-odps-test/dev/SpeechSentenceTest/'
USING 'odps-udf-example.jar,sm_random_5_utterance.text.label';
```

As in the preceding example, you must create an external table. Then, use the schema of this table to define the information that you want to extract from the audio file:

-   The statement signal-to-noise ratio\(SNR\) in an audio file: sentence\_snr.
-   The name of the audio file: id.

After creating the external table, use a standard Select statement to perform a query. This operation triggers the extractor to perform computation. When reading and processing OSS data, in addition to simple deserialization on text files, you can use custom extractors to perform more complex data processing and extraction logic. In this example, use the custom extractor encapsulated in `com.aliyun.odps.udf.example.speech.SpeechStorageHandler` to calculate the average SNR of valid statements in the audio file, and extract structured data for SQL operations \(WHERE sentence\_snr \> 10\). Once completed, the operation returns all audio files with an SNR that greater than 10 and their corresponding SNR values.

Multiple WAV-format files are stored at the OSS address `oss://oss-cn-hangzhou-zmf.aliyuncs.com/oss-odps-test/dev/SpeechSentenceTest/`. The MaxCompute framework reads all the files at this address and, when necessary, performs file-level sharding. It automatically allocates the file to multiple computing nodes for processing. On each computing node, the extractor is responsible for processing the file set allocated to the node by InputStreamSet. The special processing logic is similar to your single-host program. Your algorithm is implemented by using the single host method according to its class.

Details about the `SpeechSentenceSnrExtractor` formulation logic are as follows:

First, read the parameters in the `setup` interface to perform initialization and import the audio processing model \(using resource introduction\):

```
public SpeechSentenceSnrExtractor(){
    this.utteranceLabels = new HashMap<String, UtteranceLabel>();
  
  @Override
  public void setup(ExecutionContext ctx, InputStreamSet inputs, DataAttributes attributes){
    this.inputs = inputs;
    this.attributes = attributes;
    this.mlfFileName = this.attributes.getValueByKey(MLF_FILE_ATTRIBUTE_KEY);
    String sampleRateInKHzStr = this.attributes.getValueByKey(SPEECH_SAMPLE_RATE_KEY);
    this.sampleRateInKHz = Double.parseDouble(sampleRateInKHzStr);
    try {
      // read the speech model file from resource and load the model into memory
      BufferedInputStream inputStream = ctx.readResourceFileAsStream(mlfFileName);
      loadMlfLabelsFromResource(inputStream);
      inputStream.close();
    } catch (IOException e) {
      throw new RuntimeException("reading model from mlf failed with exception " + e.getMessage());
    
  

```

In the Extractor\(\) interface, the specific read and processing logic is implemented on the audio files. The SNR of the read data is calculated based on the audio model and results are written to a record in \[snr , id\] format.

The preceding example simplifies the implementation process and does not include the relevant audio processing algorithm logic. see the [example code](https://github.com/aliyun/aliyun-odps-java-sdk/blob/master/odps-sdk-impl/odps-udf-example/src/main/java/com/aliyun/odps/udf/example/speech/SpeechSentenceSnrExtractor.java) provided by the MaxCompute SDK in the open source community.

```
@ Override
  public Record extract() throws IOException {
    SourceInputStream inputStream = inputs.next();
    if (inputStream == null){
      return null;
    
    // process one wav file to extract one output record [snr, id]
    String fileName = inputStream.getFileName();
    fileName = fileName.substring(fileName.lastIndexOf('/') + 1);
    logger.info("Processing wav file " + fileName);
    String id = fileName.substring(0, fileName.lastIndexOf('.'));
    // read speech file into memory buffer
    long fileSize = inputStream.getFileSize();
    byte[] buffer = new byte[(int)fileSize];
    int readSize = inputStream.readToEnd(buffer);
    inputStream.close();
    // compute the avg sentence snr
    double snr = computeSnr(id, buffer, readSize);
    // construct output record [snr, id]
    Column[] outputColumns = this.attributes.getRecordColumns();
    ArrayRecord record = new ArrayRecord(outputColumns);
    record.setDouble(0, snr);
    record.setString(1, id);
    return record;
  
  private void loadMlfLabelsFromResource(BufferedInputStream fileInputStream)
          throws IOException {
    // skipped here
  
  // compute the snr of the speech sentence, assuming the input buffer contains the entire content of a wav file
  Private double computersnr (string ID, byte [] buffer, int validbufferlen ){
    // computing the snr value for the wav file (supplied as byte buffer array), skipped here
  
```

Run the query:

```
select sentence_snr, id
    from speech_sentence_snr_external
where sentence_snr > 10.0;
```

Results:

```

| sentence_snr | id |

| 34.4703 | J310209090013_H02_K03_042 |

| 31.3905 | tsh148_seg_2_3013_3_6_48_80bd359827e24dd7_0 |

| 35.4774 | tsh148_seg_3013_1_31_11_9d7c87aef9f3e559_0 |

| 16.0462 | tsh148_seg_3013_2_29_49_f4cb0990a6b4060c_0 |

| 14.5568 | tsh_148_3013_5_13_47_3d5008d792408f81_0 |

```

By using the customized extractor, you can process multiple voice data files stored on OSS on the SQL statement in a distributed way. Using a similar method, you can also use MaxCompute's large-scale computing power to easily process different types of unstructured data, such as image and video.

## Data partition {#section_avv_rhb_wdb .section}

In earlier sections, an external table linked data is implemented through designated OSS Directory on LOCATION. But while process, MaxCompute reads all data under Directory, **including all files in sub-directory**. For accumulated data directories along with time, because the data volume is too big, scan the entire directory may cause unnecessary extra IO and data processing time. Normally, there are 2 solutions for this problem.

-   Reduce access data volume: You are responsible for planning data storage address, and considering using multiple numbers of EXTERNAL TABLE to scan data in different parts, making each LOCATION of EXTERNAL TABLE point to a data subaggegate.
-   Partition data: EXTERNAL TABLE is the same as internal table, it **supports functions of partition table**, you are available to manage data systemization based on partition function.

It mainly introduces partition function of EXTERNAL TABLE in this section.

-   **Standard Organization Method and Path Format of Partition Data on OSS**

    Unlike its internal tables, MaxCompute does not have the authority to manage data stored in external memory \(such as OSS\). As such, if you must use the partition table function on your system, the storage path for data files on OSS needs to conform to a certain format. This format is as follows.

    ```
    partitionKey1=value1\partitionKey2=value2\...
    ```

    **Related examples are as follows**

    Assume that you save your daily LOG files on OSS and want to access part of the data when processed with MaxCompute, based on the granularity of Day. Assuming that these LOG files are CSV files \(usage of complicated and customized format is similar\), you can define data using the following **partitioned external table**.

    ```
    CREATE EXTERNAL TABLE log_table_external (
        click STRING,
        ip STRING,
        url STRING,
      
      PARTITIONED BY (
        year STRING,
        month STRING,
        Day string
      
      Stored by 'com. aliyun. ODPS. csvstoragehandler'
      WITH SERDEPROPERTIES (
     'odps.properties.rolearn'='acs:ram::xxxxx:role/aliyunodpsdefaultrole'
     
      LOCATION 'oss://oss-cn-hangzhou-zmf.aliyuncs.com/oss-odps-test/log_data/';
    ```

    In contrast to the external table example provided in the previous section, in the aforementioned three-tier partition example, when you define EXTERNAL TABLE, you specify the external table as a partition table using the syntax of PARTITION BY. The partition keys are year, month, and day.

    In order for a partition like this to work effectively, you must comply with the aforementioned path format when storing data on OSS. The following is an example of a valid path storage layout.

    ```
    osscmd ls oss://oss-odps-test/log_data/
    2017-01-14 08:03:35 128MB Standard oss://oss-odps-test/log_data/year=2016/month=06/day=01/logfile
    2017-01-14 08:04:12 127MB Standard oss://oss-odps-test/log_data/year=2016/month=06/day=01/logfile. 1
    2017-01-14 08:05:02 118MB Standard oss://oss-odps-test/log_data/year=2016/month=06/day=02/logfile
    2017-01-14 08:06:45 123MB Standard oss://oss-odps-test/log_data/year=2016/month=07/day=10/logfile
    2017-01-14 08:07:11 115 MB standard OSS: // OSS-ODPS-test/log_data/year = 2016/month = 08/day = 08/logfile
    
    ```

    **Note:** If you have prepared the offline data, that is, if you have uploaded the offline data to the OSS storage service with osscmd or other OSS tools, you then define the data path format.

    On this premise, partition information is imported to MaxCompute through the ALTER TABLE ADD PARTITION statement.

    An example of the corresponding DDL statement is as follows.

    ```
    ALTER TABLE log_table_external ADD PARTITION (year = '2016', month = '06', day = '01')
    ALTER TABLE log_table_external ADD PARTITION (year = '2016', month = '06', day = '02')
    ALTER TABLE log_table_external ADD PARTITION (year = '2016', month = '07', day = '10')
    ALTER TABLE log_table_external ADD PARTITION (year = '2016', month = '08', day = '08')
    
    ```

    **Note:** These operations are similar to the standard MaxCompute inner table operations, and you can see documentation if you are unfamiliar with the [partition tables](../../../../intl.en-US/Product Introduction/Definition/Partition.md) concept. When the data is ready and the PARTITION information has been imported into the system, the partitioning of the external table data on OSS can be performed by means of an SQL statement.

    Assuming that you only want to analyze how many different IPs there are in LOG on June 1, 2016, the following command can be used:

    ```
    SELECT count(distinct(ip)) FROM log_table_external WHERE year = '2016' AND month = '06' AND day = '01';
    ```

    At this point, for log\_table\_external, the directory that corresponds to the external table will only access the files under the `log_data/year=2016/month=06/day=01` subdirectory \(logfile and logfile .1\). **By not performing a full scan of all** the data in the entire log\_data/**directory, a lot of useless I/O operations can be avoided**.

    Similarly, if you only want to analyze the data for the second half of 2016, you can use the following command:

    ```
    SELECT count(distinct(ip)) FROM log_table_external 
    WHERE year = '2016' AND month > '06';
    ```

    At this point, only access the second half of the LOG stored on OSS.

-   **Customized Path of Partition Data on OSS**

    If you have historical data stored on OSS but it is not stored using the `partitionKey1=value1\partitionKey2=value2\...` path format, you can still access it using MaxCompute’s partition mode. MaxCompute also provides a way to import partitions through a customized path.

    Assume that only a simple partition value is on your data path \(and no partition key information\). The following is an example of the data path storage layout.

    ```
    osscmd ls oss://oss-odps-test/log_data_customized/
    2017-01-14 08:03:35 128MB Standard oss://oss-odps-test/log_data_customized/2016/06/01/logfile
    2017-01-14 08:04:12 127MB Standard oss://oss-odps-test/log_data_customized/2016/06/01/logfile. 1
    2017-01-14 08:05:02 118MB Standard oss://oss-odps-test/log_data_customized/2016/06/02/logfile
    2017-01-14 08:06:45 123MB Standard oss://oss-odps-test/log_data_customized/2016/07/10/logfile
    2017-01-14 08:07:11 115MB Standard oss://oss-odps-test/log_data_customized/2016/08/08/logfile
    
    ```


The external table builder DDL can see the previous example and also specify the partition key in the clause.

To bind different subdirectories to different partitions, you can do so by using a command similar to the following customized partition path.

```
ALTER TABLE log_table_external ADD PARTITION (year = '2016', month = '06', day = '01')
LOCATION 'oss://oss-cn-hangzhou-zmf.aliyuncs.com/oss-odps-test/log_data_customized/2016/06/01/';
```

When LOCATION information is added in ADD  PARTITION to customize a partition data path. Even if the data is not stored in the recommended format of `partitionKey1=value1\partitionKey2=value2\...`, you can still access the partition data of the subdirectory.

