# Unstructured data exported to OSS {#concept_wsw_nnb_wdb .concept}

[Accessing OSS unstructured data](intl.en-US/User Guide/Handle-Unstructured-data/Access OSS data.md) shows you how MaxCompute can be accessed and processed by using external tables unstructured data stored in OSS, in fact, the unstructured framework of MaxCompute also supports output of MaxCompute data directly to OSS via insert, MaxCompute also associates OSS with external tables for data output.

Output Data to OSS is typically in two cases:

-   The MaxCompute internal table is output to the External table that is associated with the OSS.
-   After MaxCompute processes the external tables, the result isoutput directly to the external tables that are associated with the OSS.

Like accessing OSS data, MaxCompute supports output via built-in storagehandler and custom storagehandler.

## Output to OSS via built-in storagehandler {#section_xcn_snb_wdb .section}

Using the built-in storagehandler in MaxCompute And can be very convenient to output data in the agreed format to OSS for storage. You must create an external table that indicates the built-in storagehandler, it can be associated with this table, and the related logic is implemented by the system.

Currently MaxCompute supports 2 built-in storagehandlers:

-   com.aliyun.odps.CsvStorageHandler , Defines how to read and write CSV format data, data format Conventions: Usea comma \(,\) as a column separator`,` line Break is `\n`.
-   com.aliyun.odps.TsvStorageHandler, defines how to read and write CSV format data, data format Conventions: `\t`is a column separator, line Break is `\n`.

-   Create external TABLE

    ```
    CREATE EXTERNAL TABLE [IF NOT EXISTS] &lt;external_table&gt;
    (&lt;column schemas&gt;)
    [PARTITIONED BY (partition column schemas)]
    STORED BY '&lt;StorageHandler&gt;'  
    [WITH SERDEPROPERTIES (  'odps.properties.rolearn'='${roleran}') ]
    LOCATION 'oss://${endpoint}/${bucket}/${userfilePath}/';
    
    ```

    -   STORED By, if the data file that is required to be exported to OSS is a TSV file, then built-in `com.aliyun.odps.TsvStorageHandler` if the data file that is required to be exported to OSS is a CSV file, a built-in `com.aliyun.odps.CsvStorageHandler`.
    -   WITH Serdeproperties, when the associated OSS permission uses custom authorization of STS mode authorization, this parameter must bespecified 'odps.properties.rolearn 'property, attribute value is Ram Information about the specific use of custom role arns in.

        **Note:** For more information about STS mode authorization, see [accessing the unstructured data of OSS](intl.en-US/User Guide/Handle-Unstructured-data/Access OSS data.md).

    -   Location that specifies the path to the file that corresponds to the OSS storage. If the'odps.properties.rolearn'attribute is not set in WITH SERDEPROPERTIESand the authorization is in plaintext AK, the LOCATIONis

        ```
        Location
            'oss://${accessKeyId}:${accessKeySecret}@${endpoint}/${bucket}/${userPath}/'
        ```

-   Data output to OSS through insert operation on External table

    **Note:** The size of a single file from insert to OSS can not exceed 5G.

    When an external After table is associated with an OSS storage path, it is possible to do a standard SQL insert override/insert on an external table, the into operation can both output data to OSS.

    ```
    INSERT OVERWRITE|INTO TABLE &lt;external_tablename&gt; [PARTITION (partcol1=val1, partcol2=val2 ...)]
    select_statement
    FROM &lt;from_tablename&gt; 
    [WHERE where_condition];
    
    ```

    -   from\_tablename: It can be both an internal table or an external table \(including an external table for the associated OSS or OTs \).

    -   Insert will be specified according to External table 'stored' the format of 'storagehandler' \(that is, TSV or CSV\) is written to OSA.

    When the insert operation is completed successfully, you can see that the corresponding location on the OSS produces a series of files.

    Example: External table the corresponding location is oss://oss-cn-hangzhou-zmf.aliyuncs.com/oss-odps-test/tsv\_output\_folder/Then, you can see the generation of a series of files in the OSS corresponding path:

    ```
    osscmd ls oss://oss-odps-test/tsv_output_folder/
    2017-01-14 06:48:27 39.00B Standard oss://oss-odps-test/tsv_output_folder/.odps/.meta
    2017-01-14 06:48:12 4.80MB Standard oss://oss-odps-test/tsv_output_folder/.odps/20170113224724561g9m6csz7/M1_0_0-0.tsv
    2017-01-14 06:48:05 4.78MB Standard oss://oss-odps-test/tsv_output_folder/.odps/20170113224724561g9m6csz7/M1_1_0-0.tsv
    2017-01-14 06:47:48 4.79MB Standard oss://oss-odps-test/tsv_output_folder/.odps/20170113224724561g9m6csz7/M1_2_0-0.tsv
    ...
    ```

    You can see, through the oss-odps-test specified in the previous location, this OSS A 'was generated under the maid folder under the bucket '.odps 'folder, which will have some '. tsv' file, and '. meta 'file. Similar file structures are specific to MaxCompute's output to OSS:

    -   USE insert into/Overwrite for an OSS address via MaxCompute The External table will do the output operation, all data will be under the specified location '. the ODPS 'folder is generated.
    -   The .meta file in the .odps folder is an extra macro data file written by MaxCompute to record the valid data in the current folder. Typically, if the INSERT operation is successful, all the data in the current folder is valid. The macro data only needs to be parsed when a job fails. For insert, even if the job fails in the middle or is killed. The overwrite operation will run one more success.
    -   If it is a partition table, A corresponding partition sub-directory is generated based on the partition value specified by the insert statement under the fig folder and then the partition sub-directory inside is '.odps 'folder. For example, `test/tsv_output_folder/first-level partition name = partition value/.odps/20170113224724561g9m6csz7/M1_2_0-0.tsv`.
    For the TSV/CSV storagehandler processing built in by MaxCompute, the number of files generated is corresponding to the corresponding SQL Stage has the same degree of concurrency.

    If `INSER OVERWITE ... Select... From ... ;`The operation of the source data table \(FIG\) There are 1000 mapper allocated on, and a total of 1000 TSV/CSV files will be generated.


## Output to OSS via custom storagehandler {#section_tbq_44b_wdb .section}

In addition to using the built-in storagehandler to implement the output TSV/CSV common text format on the OSS, the MaxCompute unstructured framework provides a general-purpose SDK that supports external output of custom data format files.

As well as the built-in storagehandler, you need to "Create an External table" before "passing an insert on an external table" The operation implements the output of data to OSS ". The difference is that when creating an external table, stored by is a storagehandler that needs to be specified as a custom.

**Note:** The MaxCompute unstructured framework describes the processing of a variety of data storage formats through an interface called storagehandler. Specifically, the storagehandler acts as a Wrapper class, lets you specify a custom expractor \(for Data Reading, parsing, processing, etc\) And outputer \(for data processing and output, etc \). Custom storagehandler should inherit To implement the interface and the interface.

Next we use custom Extractor [access in accessing OSS unstructured data](intl.en-US/User Guide/Handle-Unstructured-data/Access OSS data.md) to show how MaxComputer can customize StorageHandler Output the data to the TXT file of the OSS, with '|' as the column separator, take '\\ n' as a line break.

**Note:** [MaxCompute After the studio](../../../../intl.en-US/Tools and Downloads/MaxCompute Studio/What is Studio.md)is configured with [MaxCompute Java module](../../../../intl.en-US/Tools and Downloads/MaxCompute Studio/Developing Java/Create MaxCompute Java Module.md), you can see the corresponding sample code in examples. Or click [here](https://github.com/aliyun/aliyun-odps-java-sdk/tree/master/odps-sdk-impl/odps-udf-example/src/main/java/com/aliyun/odps/udf/example/text) to see the complete code.

-   **Define outputer**

    Both output logic must implement the outputer interface:

    ```
    package com.aliyun.odps.examples.unstructured.text;
    import com.aliyun.odps.data.Record;
    import com.aliyun.odps.io.OutputStreamSet;
    import com.aliyun.odps.io.SinkOutputStream;
    import com.aliyun.odps.udf.DataAttributes;
    import com.aliyun.odps.udf.ExecutionContext;
    import com.aliyun.odps.udf.Outputer;
    import java.io.IOException;
    public class TextOutputer extends Outputer {
        private SinkOutputStream outputStream;
        private DataAttributes attributes;
        private String delimiter;
        public TextOutputer (){
            // default delimiter, this can be overwritten if a delimiter is provided through the attributes.
            this.delimiter = "|";
        }
        @Override
        public void output(Record record) throws IOException {
            this.outputStream.write(recordToString(record).getBytes());
        }
        // no particular usage of execution context in this example
        @Override
        public void setup(ExecutionContext ctx, OutputStreamSet outputStreamSet, DataAttributes attributes) throws IOException {
            this.outputStream = outputStreamSet.next();
            this.attributes = attributes;
        }
        @Override
        public void close() {
            // no-op
        }
        private String recordToString(Record record){
            StringBuilder sb = new StringBuilder();
            for (int i = 0; i &lt; record.getColumnCount(); i++)
            {
                if (null == record.get(i)){
                    sb.append("NULL");
                }
                else{
                    sb.append(record.get(i).toString());
                }
                if (i ! = record.getColumnCount() - 1){
                    sb.append(this.delimiter);
                }
            }
            sb.append("\n");
            return sb.toString();
        }
    }
    ```

    There are three outputer interfaces: setup, Output and close, which are essentially Symmetric With the extractor's three interfaces, setup, extract, and close. Where setup \(\) and close \(\) are called only once in an outputer. You can do initialization preparation work in setup, And you usually need to put setup \(\) the three parameters passed in are saved as class variable for ouputerd, Used in the output \(\) or close \(\) interface after convenience. The interface, close \(\), is used to sweep the end of the Code.

    Typically, most of the data processing occurs in the output \(record\) interface. The MaxCompute system calls output \(record\) Once based on each input record processed by the current outputer assignment \). Assuming that when an output \(record\) call returns, the Code has already consumed the record, So after the current output \(record\) returns, the system uses the memory used by the record for it, so when the information in record is used across multiple output \(\) function calls, the record for the current process needs to be invoked. clone \(\) method to save the current record.

-   **Define Extractor**

    Exatrractor is used for Data Reading, parsing, processing, and so on, if the output tables eventually do not need to be read by MaxCompute and so on, you do not need to define them.

    ```
    package com.aliyun.odps.examples.unstructured.text;
    import com.aliyun.odps.Column;
    import com.aliyun.odps.data.ArrayRecord;
    import com.aliyun.odps.data.Record;
    import com.aliyun.odps.io.InputStreamSet;
    import com.aliyun.odps.udf.DataAttributes;
    import com.aliyun.odps.udf.ExecutionContext;
    import com.aliyun.odps.udf.Extractor;
    import java.io.BufferedReader;
    import java.io.IOException;
    import java.io.InputStream;
    import java.io.InputStreamReader;
    /**
     Text extractor that extract schematized records from formatted plain-text(csv, tsv etc.)
     **/
    public class TextExtractor extends Extractor {
        private InputStreamSet inputs;
        private String columnDelimiter;
        private DataAttributes attributes;
        private BufferedReader currentReader;
        private boolean firstRead = true;
        public TextExtractor() {
            // default to ",", this can be overwritten if a specific delimiter is provided (via DataAttributes)
            this.columnDelimiter = ",";
        }
        // no particular usage for execution context in this example
        @Override
        public void setup(ExecutionContext ctx, InputStreamSet inputs, DataAttributes attributes) {
            this.inputs = inputs;
            this.attributes = attributes;
            // check if "delimiter" attribute is supplied via SQL query
            String columnDelimiter = this.attributes.getValueByKey("delimiter");
            if ( columnDelimiter ! = null)
            {
                this.columnDelimiter = columnDelimiter;
            }
            System.out.println("TextExtractor using delimiter [" + this.columnDelimiter + "].");
            // note: more properties can be inited from attributes if needed
        }
        @Override
        public Record extract() throws IOException {
            String line = readNextLine();
            if (line == null) {
                return null;
            }
            return textLineToRecord(line);
        }
        @Override
        public void close(){
            // no-op
        }
        private Record textLineToRecord(String line) throws IllegalArgumentException
        {
            Column[] outputColumns = this.attributes.getRecordColumns();
            ArrayRecord record = new ArrayRecord(outputColumns);
            if (this.attributes.getRecordColumns().length ! = 0){
                // string copies are needed, not the most efficient one, but suffice as an example here
                String[] parts = line.split(columnDelimiter);
                int[] outputIndexes = this.attributes.getNeededIndexes();
                if (outputIndexes == null){
                    throw new IllegalArgumentException("No outputIndexes supplied.");
                }
                if (outputIndexes.length ! = outputColumns.length){
                    throw new IllegalArgumentException("Mismatched output schema: Expecting "
                            + outputColumns.length + " columns but get " + parts.length);
                }
                int index = 0;
                for(int i = 0; i < parts.length; i++){
                    // only parse data in columns indexed by output indexes
                    if (index < outputIndexes.length && i == outputIndexes[index]){
                        switch (outputColumns[index].getType()) {
                            case STRING:
                                record.setString(index, parts[i]);
                                break;
                            case BIGINT:
                                record.setBigint(index, Long.parseLong(parts[i]));
                                break;
                            case BOOLEAN:
                                record.setBoolean(index, Boolean.parseBoolean(parts[i]));
                                break;
                            case DOUBLE:
                                record.setDouble(index, Double.parseDouble(parts[i]));
                                break;
                            case DATETIME:
                            case DECIMAL:
                            case ARRAY:
                            case MAP:
                            Default:
                                throw new IllegalArgumentException("Type " + outputColumns[index].getType() + " not supported for now.");
                        }
                        index++;
                    }
                }
            }
            return record;
        }
        /**
         * Read next line from underlying input streams.
         * @return The next line as String object. If all of the contents of input
         * streams has been read, return null.
         */
        private String readNextLine() throws IOException {
            if (firstRead) {
                firstRead = false;
                // the first read, initialize things
                currentReader = moveToNextStream();
                if (currentReader == null) {
                    // empty input stream set
                    return null;
                }
            }
            while (currentReader ! = null) {
                String line = currentReader.readLine();
                if (line ! = null) {
                    return line;
                }
                currentReader = moveToNextStream();
            }
            return null;
        }
        private BufferedReader moveToNextStream() throws IOException {
            InputStream stream = inputs.next();
            if (stream == null) {
                return null;
            } else {
                return new BufferedReader(new InputStreamReader(stream));
            }
        }
    }
    ```

    For more information, see[accessing the OSS unstructured data](intl.en-US/User Guide/Handle-Unstructured-data/Access OSS data.md) documentation.

-   **Define StorageHandler**

    ```
    package com.aliyun.odps.examples.unstructured.text;
    import com.aliyun.odps.udf.Extractor;
    import com.aliyun.odps.udf.OdpsStorageHandler;
    import com.aliyun.odps.udf.Outputer;
    public class TextStorageHandler extends OdpsStorageHandler {
        @Override
        public Class&lt;? extends Extractor&gt; getExtractorClass() {
            return TextExtractor.class;
        }
        @Override
        public Class&lt;? extends Outputer&gt;getOutputerClass() {
            return TextOutputer.class;
        }
    }
    ```

    If the table does not need to be read, you do not need to specify an extractor interface.

-   **Compile and package**

    Package custom code compilation and act as a jar The resource is uploaded to MaxCompute. If the jar package is named 'odps-TextStorageHandler.jar', upload to MaxCompute The resource is as follows:

    ```
    add jar odps-TextStorageHandler.jar;
    ```

-   **Creating External tables**

    Like using the built-in storagehandler, an External table needs to be created, the difference is that this time you need to specify that the data is output to an external table, using a custom storagehandler.

    ```
    CREATE EXTERNAL TABLE IF NOT EXISTS output_data_txt_external
    (
    vehicleId int,
    recordId int,
    patientId int,
    calls int,
    locationLatitute double,
    locationLongtitue double,
    recordTime string,
    direction string
    )
    STORED BY 'com.aliyun.odps.examples.unstructured.text.TextStorageHandler' 
    WITH SERDEPROPERTIES(
        'delimiter'='|'
        [, 'ODPS. properties. rolearn' = '$ {roleran}'])
    LOCATION 'oss://${endpoint}/${bucket}/${userfilePath}/'
    USING 'odps-TextStorageHandler.jar';
    ```

    **Note:** If you need 'odps.properties.rolearn'property, for more information, see **custom authorization** for STs mode authorization to[access the OSS unstructured data](intl.en-US/User Guide/Handle-Unstructured-data/Access OSS data.md). If not, you can refer to **one-click authorization** or use clear-text AK on top of location.

-   **Write unstructured files into External Table using INSERT**

    Creating external with custom storagehandler After table is associated with an OSS storage path, it is possible to do a standard SQL insert override/insert on an external table The into operation can both output data to OSS in the same manner as the built-in storagehandler:

    ```
    INSERT OVERWRITE|INTO TABLE &lt;external_tablename> [PARTITION (partcol1=val1, partcol2=val2 ...)]
    Select_statement
    FROM &lt;from_tablename&gt'; 
    [WHERE where_condition];
    
    ```

    When the insert operation is successful, it is the same as the built-in storagehandler, you can see a series of files generated in the OSS corresponding location path '.odps 'folder.


