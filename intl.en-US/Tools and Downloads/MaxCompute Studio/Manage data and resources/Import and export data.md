# Import and export data {#concept_zrp_lyx_5db .concept}

MaxCompute Studio can import local data files in CSV or TSV format to MaxCompute tables and export MaxCompute table data to local files. MaxCompute Studio completes data import and export by using Batch data Tunnel provided by the MaxCompute platform.

## Usage instructions {#section_rgn_3rz_5db .section}

-   The MaxCompute Tunnel service must be used for data import and export. Therefore, the MaxCompute project added in Studio must be configured with the Tunnel service.
-   Related permissions must be granted for table import and export.

## Import data {#section_pvn_lrz_5db .section}

1.  Open the Project Explorer window, right-click a table name or a field attribute in Data preview of Table details and select **Import Data Into Table**.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/12122/1637_en-US.png)

2.  In the Import Data dialog box that appears, select the path of the imported data file, column separator, size limitation, and number of lines for an error tolerance, and click **OK** .

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/12122/1638_en-US.png)

3.  If **Import Data Success** is displayed, data import is successful and imported data can be viewed in the table.


## Export data {#section_cnd_hsz_5db .section}

1.  Two methods are provided for table data export.
    -   Right-click a table name and select **Export Data From Table**.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/12122/1640_en-US.png)

    -   Right-click a field attribute in **Data Preview** of Table details and select **Export Data From Table**.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/12122/1641_en-US.png)

2.  In the Export Data dialog box that appears, select the path for saving the exported data file, column separator, size limitation, and number of lines for an error tolerance, and click OK.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/12122/1642_en-US.png)

3.  If **Export Data Success** is displayed, data export is successful and exported data can be viewed in the target file.


You can also right-click Data Preview of Table and choose **Export Grid Data** to export data.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/12122/1643_en-US.png)

**Note:** The Data Preview function in Data preview is used only to export data displayed in Data sample instead of all data in the table.

## New Type Import Export {#section_lqj_2rz_5db .section}

Simply generate text in the agreed format and store it in CSV or TSV format, you can import to table through studio.

Conversion rules for each data type are described in detail below.

**Basic Type**

1.  Tinyint, smallint, Int, bigint is stored directly as an integer string, and the numeric value exceeds the type boundary is reported as wrong
2.  Float, double to store fractional strings or floating-point forms, such as: 2.342 1x + 7
3.  Varchar is stored directly as a string, which is automatically truncated above the upper limit, and no errors will be reported
4.  String is stored directly as a string
5.  Decimal string that supports shaping or floating-point
6.  Binary needs to encode binary data to base64 string
7.  Datetime date time requires that the format specified in import dialog is consistent and that the format mismatches will be reported incorrectly
8.  Timestamp timestamp needs to follow yyyy-\[m\] M-\[d\] d hh \\: mm \\: ss \[. f...\] Format is stored as a string
9.  Boolean true or false string?

**Composite Type**

1.  Array needs to be stored as a JSON array. The array elements are converted to strings according to the rules agreed in this article, array elements support any type.
2.  Map needs to be stored as a JSON object, and map key, value are converted to a string according to the rules agreed in this article, value supports any type of nesting.
3.  Struct needs to be stored as a JSON object, the struct field name is string, and the key converted to a JSON object, strect field values converted to JSON The value of the object, the value of the field that defines the rule transformation in this article.

**Example**

**Array type**

For Table structures as follows:

|Column name|Column data types|
|:----------|:----------------|
|c\_1|ARRAY<TINYINT\>|
|c\_2|ARRAY<INT\>|
|c\_3|ARRAY<FLOAT\>|
|c\_4|ARRAY<DATETIME\>|
|c\_6|ARRAY<TIMESTAMP\>|
|c\_7|ARRAY<STRING\>|

You can import data in the CSV format shown below:

```

c_1,c_2,c_3,c_4,c_6,c_7
"[" "1" "," "2 "", "3" "]", "[" 1 "", "2", "3", "4"], "[" "1.2" "," 2.0 ""] ", "[" "3-00:00:00", "3-5-00:00:00 "", "00:00:00", "[" At 00:00:00. 123456789 "", "At 00:00:00. 123456789 "", "At 00:00:00. 123456789 ""] "," ["AAA" "," Steamboat "", "4C" "]"
"[""1"",""2"",""3""]","[""1"",""2"",""3"",""4""]","[""1.2"",""2.0""]","[""2017-11-11 00:00:00"",""2017-11-11 00:00:00"",""2017-11-11 00:00:00""]","[""2017-11-11 00:00:00.123456789"",""2017-11-11 00:00:00.123456789"",""2017-11-11 00:00:00.123456789""]","[""aaa"",""bbb"",""ccc""]"
"[""1"",""2"",""3""]","[""1"",""2"",""3"",""4""]","[""1.2"",""2.0""]","[""2017-11-11 00:00:00"",""2017-11-11 00:00:00"",""2017-11-11 00:00:00""]","[""2017-11-11 00:00:00.123456789"",""2017-11-11 00:00:00.123456789"",""2017-11-11 00:00:00.123456789""]","[""aaa"",""bbb"",""ccc""]"
```

**Note:** The CSV format needs to escape double quotes, which are expressed by two double quotes, you can refer specifically to the CSV format specification.

**Map Type**

For Table structures as follows:

|Column name|Column data types|
|:----------|:----------------|
|c\_1|MAP<TINYINT,STRING\>|
|c\_2|MAP<STRING,INT\>|
|c\_3|MAP<FLOAT,STRING\>|
|c\_4|MAP<STRING,DATETIME\>|
|c\_5|MAP<STRING,STRING\>|
|c\_6|MAP<TIMESTAMP,STRING\>|

You can import data in the CSV format shown below:

```

c_1,c_2,c_3,c_4,c_5,c_6
"{1:" "2345" "}", "{" 123 "": "2", "3": "4 ""}", "{2.0:" "223445" ", 1.2:" 1111 ""}", "{" "AAA" ":" "hub11 00:00:00 "", "4C" ":" China "11 00:00:00" "," Steamboat "": "00:00:00"} "," ckey "": "cvalue"} "," {"" hub11 01:00:00. 123456789 "": "dddd" "," "hub11 00:00:00. 123456789 "": "AAA" "," 027 11 00:01:00. 123456789 "": "DDD ""}"
"{1:" "2345" "}", "{" 123 "": "2", "3": "4 ""}", "{2.0:" "223445" ", 1.2:" 1111 ""}", "{" "AAA" ":" "hub11 00:00:00 "", "4C" ":" China "11 00:00:00" "," Steamboat "": "00:00:00"} "," ckey "": "cvalue"} "," {"" hub11 01:00:00. 123456789 "": "dddd" "," "hub11 00:00:00. 123456789 "": "AAA" "," 027 11 00:01:00. 123456789 "": "DDD ""}"
"{1:""2345""}","{""123"":""2"",""3"":""4""}","{2.0:""223445"",1.2:""1111""}","{""aaa"":""2017-11-11 00:00:00"",""ccc"":""2017-11-11 00:00:00"",""bbb"":""2017-11-11 00:00:00""}","{""ckey"":""cvalue""}","{""2017-11-11 01:00:00.123456789"":""dddd"",""2017-11-11 00:00:00.123456789"":""aaa"",""2017-11-11 00:01:00.123456789"":""ddd""}"
```

**Struct Type**

For Table structures as follows:

|Column name|Column data types|
|:----------|:----------------|
|C\_struct|<RUCT<x:INT,y:VARCHAR\(256\),z:STRUCT<a:TINYINT,b:STRING\>\>|

You can import data in the CSV format shown below:

```

c_struct
"{""x"":""1000"",""y"":""varchar_test"",""z"":{""a"":""123"",""b"":""stringdemo""}}"
"{""x"":""1000"",""y"":""varchar_test"",""z"":{""a"":""123"",""b"":""stringdemo""}}"
```

