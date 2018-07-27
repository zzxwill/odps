# Tunnel commands {#concept_rkf_2wc_5db .concept}

## Features {#section_hcs_btf_vdb .section}

The [Client](../../../../intl.en-US/Tools and Downloads/Client.md) provides [Tunnel](intl.en-US/User Guide/Data upload and download/Tunnel commands.md) commands for you to use the functions of the original Dship tool.

Tunnel commands are mainly used to upload data or download data.

-   Upload: Supports file or directory\(level-one\) uploading. Data can only be uploaded to a single table or table partition each time. For partitioned tables, the destination partition must be specified.

    ```
    tunnel upload log.txt test_project.test_table/p1="b1",p2="b2";
    -- Uploads data in log.txt to the test_project project's test_table table, partitions: p1="b1",p2="b2".
    tunnel upload log.txt test_table --scan=only;
    -- Uploads data from log.txt to the test_table table.--The scan parameter indicates that the data in log.txt must be scanned to determine if it complies with the test_table definitions.If it does not, the system reports an error and the upload is stopped.
    ```

-   Download: You can only download data to a single file. Only data in one table or partition can be downloaded to one file each time. For partitioned tables, the source partition must be specified.

    ```
    tunnel download test_project.test_table/p1="b1",p2="b2" test_table.txt;
    -- Download data from the table to the test_table.txt file.
    ```

-   Resume:  If an error occurs due to the network or the Tunnel service, you can resume transmission of the file or directory after interruption. This command allows you to resume a previous data upload operation, but does not support download operations.

    ```
    tunnel resume;
    ```

-   Show: Displays the performed commands.

    ```
    tunnel show history -n 5;
    -- Displays details for the last five data upload/download commands.
    tunnel show log;
    --Displays the log for the last data upload/download.
    ```

-   Purge: Clears the session directory. By default, use this command to clear information of the last three days.

    ```
    tunnel purge 5;
    --Clears logs from the previous five days.
    ```


## Tunnel upload and download limits {#section_an5_42t_j2b .section}

Tunnel command does not support uploading and downloading data of the Array, Map, and Struct types.

Each session has a 24-hour life cycle on the server. It can be used within 24 hours after being created, and can be shared among processes or threads. The block ID of each session must be unique.

## Use of Tunnel commands {#section_g2l_1wf_vdb .section}

Tunnel commands allows you to obtain help information using the Help subcommand on the client. Each command and selection supports short command format.

```
odps@ project_name>tunnel help;
    Usage: tunnel <subcommand> [options] [args]
    Type 'tunnel help <subcommand>' for help on a specific subcommand.
Available subcommands:
    upload (u)
    download (d)
    resume (r)
    show (s)
    purge (p)
    help (h)
tunnel is a command for uploading data to / downloading data from MaxCompute.
```

**Parameters information:**

-   upload: Uploads data to a MaxCompute table.
-   download: Downloads data from a MaxCompute table.
-   resume: If data fails to be uploaded, use the Resume command to resume the upload from where it was interrupted. This command cannot be used for download operations. Each data upload or download operation is called a session. Run the Resume command and specify the session ID to be resumed.
-   show: Displays the performed operations.
-   purge: Clears the session directory.
-   help: Outputs Tunnel help information.

## Upload {#section_uwr_pwf_vdb .section}

Import data of local files to MaxCompute tables in the append mode. Its subcommands are used as follows:

```
odps@ project_name>tunnel help upload;
usage: tunnel upload [options] <path> <[project.]table[/partition]>
              upload data from local file
 -acp,-auto-create-partition <ARG> auto create target partition if not
                                     exists, default false
 -bs,-block-size <ARG> block size in MiB, default 100
 -c,-charset <ARG> specify file charset, default ignore.
                                     set ignore to download raw data
 -cp,-compress <ARG> compress, default true
 -dbr,-discard-bad-records <ARG> specify discard bad records
                                     action(true|false), default false
 -dfp,-date-format-pattern <ARG> specify date format pattern, default
                                     yyyy-MM-dd HH:mm:ss; 
 -fd,-field-delimiter <ARG> specify field delimiter, support
                                     unicode, eg \u0001. default ","
 -h,-header <ARG> if local file should have table
                                     header, default false
 -mbr,-max-bad-records <ARG> max bad records, default 1000
 -ni,-null-indicator <ARG> specify null indicator string,
                                     default ""(empty string)
 -rd,-record-delimiter <ARG> specify record delimiter, support
                                     unicode, eg \u0001. default "\r\n"
 -s,-scan <ARG> specify scan file
                                     action(true|false|only), default true
 -sd,-session-dir <ARG> set session dir, default
                                     D:\software\odpscmd_public\plugins\ds
                                     hip
 -ss,-strict-schema <ARG> specify strict schema mode. If false,
                                     extra data will be abandoned and
                                     insufficient field will be filled
                                     with null. Default true
 -te,-tunnel_endpoint <ARG> tunnel endpoint
    -threads <ARG> number of threads, default 1
 -tz,-time-zone <ARG> time zone, default local timezone:
                                     Asia/Shanghai
For example:
    tunnel upload log.txt test_project.test_table/p1="b1",p2="b2"
```

**Parameters information:**

-   -acp: Determines if the operation automatically creates the destination partition if it does not exist. This one is disabled by default.
-   -bs: Specifies the size of each data block uploaded using Tunnel. Default value: 100MiB\(1MiB=1024\*1024B\) .
-   -c: Specifies the local data file encoding. Default value: UTF-8.  When not set, the encoding of the downloaded source data is used by default.
-   -cp: Determines whether the local file is compressed before being uploaded, reducing traffic usage. This one is enabled by default.
-   -dbr: Determines whether to ignore corrupted data \(such as extra or missing columns and mismatched column data types\).
    -   If this value is true, all data not satisfying table definitions is ignored.
    -   When the parameter is set to false, the system displays error messages in case of corrupted data, but the raw data in the destination table is not contaminated.
-   -dfp: Specifies the format of DateTime data. Default value: yyyy-MM-dd HH:mm:ss. If you want to specify the time format to the level of milliseconds, you can use tunnel upload -dfp 'yyyy-MM-dd HH:mm:ss.SSS',for details of DateTime data types, please refer to [../../../../dita-oss-bucket/SP\_76/DNODPS1892969/EN-US\_TP\_11923.md\#](../../../../intl.en-US/Product Introduction/Definition/Data types.md#).
-   -fd: Specifies the column delimiter of the local data file. The default value is comma.
-   -h: Determines whether the data file contains the header. If it is set to true, Dship skips the header and starts uploading from the second row.
-   -mbr: By default, if more than 1,000 rows of corrupted data uploaded, the upload is terminated. This parameter allows you to adjust the tolerated volume of corrupted data.
-   -ni: Specifies the NULL data identifier. Default value: “ “\(blank string\).
-   -rd: Specifies the row delimiter of the local data file. Default value: \\r\\n. 
-   -s: Determines whether to scan the local data file. Default value: false.
    -   If set to true, the system scans the data first, and then imports the data if the format is correct.
    -   If set to false, the system imports the data directly without scanning.
    -   If the value is only, only local data will be scanned. No data will be imported after scanning.
-   -sd: Sets the session directory.
-   -te: Specifies the tunnel endpoint.
-   -threads: Specifies the number of threads. Default value: 1.
-   -tz: Specifies the time zone. The default value is the local time zone: Asia/Shanghai.

**Examples:**

-   To create a destination table:

```
CREATE TABLE IF NOT EXISTS sale_detail(
      shop_name STRING,
      customer_id STRING,
      total_price DOUBLE)
PARTITIONED BY (sale_date STRING,region STRING);
```

-   To add a partition:

    ```
    alter table sale_detail add partition (sale_date='201312', region='hangzhou');
    ```

-   Prepare the data file data.txt with the following content:

    ```
    shop9,97,100
    shop10,10,200
    shop11,11
    ```

    The data of the third row of this file are not consistent with the definition in Table sale\_detail. Three columns are defined by sale\_detail, but this row only has two.

-   To import data:

    ```
    odps@ project_name>tunnel u d:\data.txt sale_detail/sale_date=201312,region=hangzhou -s false
    Upload session: 201506101639224880870a002ec60c
    Start upload:d:\data.txt
    Total bytes:41 Split input to 1 blocks
    2015-06-10 16:39:22 upload block: '1'
    ERROR: column mismatch -,expected 3 columns, 2 columns found, please check data or delimiter
    ```

    Because data.txt contains corrupted data, data import fails. The system displays the session ID and error message.

-   To verify data:

    ```
    odps@ odpstest_ay52c_ay52> select * from sale_detail where sale_date='201312';
    ID = 20150610084135370gyvc61z5
    +-----------+-------------+-------------+-----------+--------+
    | shop_name | customer_id | total_price | sale_date | region |
    +-----------+-------------+-------------+-----------+--------+
    +-----------+-------------+-------------+-----------+--------+
    ```

    Because there is dirty data, the data import fails and the table contains no data.


## Show {#section_wzb_xxf_vdb .section}

Displays historical records. The subcommands are used as follows:

```
odps@ project_name>tunnel help show;
usage: tunnel show history [options]
              show session information
 -n,-number <ARG> lines
For example:
    tunnel show history -n 5
    tunnel show log
```

**Parameter:**

-n: Specifies the number of rows to display.

**Examples:**

```
odps@ project_name>tunnel show history;
201506101639224880870a002ec60c failed 'u --config-file /D:/console/conf/odps_config.ini --project odpstest_ay52c_ay52 --endpoint http://service.odps.aliyun.com/api --id UlVxOHuthHV1QrI1 --key 2m4r3WvTZbsNJjybVXj0InVke7UkvR d:\data.txt sale_detail/sale_date=201312,region=hangzhou -s false'
```

**Note:** 201506101639224880870a002ec60c is the session ID of the failed data importing in the previous section.

## Resume {#section_wgq_jyf_vdb .section}

Repairs and re-executes historical records \(only valid for data uploads\). The subcommands are used as follows:

```
odps@ project_name>tunnel help resume;
usage: tunnel resume [session_id] [-force]
              resume an upload session
 -f,-force force resume
For example:
    tunnel resume
```

**Examples:**

Modify the data.txt file as follows:

```
shop9,97,100
shop10,10,200
```

To re-upload the repaired data:

```
odps@ project_name>tunnel resume 201506101639224880870a002ec60c --force;
start resume
201506101639224880870a002ec60c
Upload session: 201506101639224880870a002ec60c
Start upload:d:\data.txt
Resume 1 blocks 
2015-06-10 16:46:42 upload block: '1'
2015-06-10 16:46:42 upload block complete, blockid=1
upload complete, average speed is 0 KB/s
OK
```

**Note:** 201506101639224880870a002ec60c is session ID.

To verify data:

```
odps@ project_name>select * from sale_detail where sale_date='201312';
 ID = 20150610084801405g0a741z5
 +-----------+-------------+-------------+-----------+--------+
 | shop_name | customer_id | total_price | sale_date | region |
 +-----------+-------------+-------------+-----------+--------+
 | shop9 | 97 | 100.0 | 201312 | hangzhou |
 | shop10 | 10 | 200.0 | 201312 | hangzhou |
 +-----------+-------------+-------------+-----------+--------+
```

## Download {#section_qxw_2zf_vdb .section}

The subcommands are used as follows:

```
odps@ project_name>tunnel help download;
usage: tunnel download [options] <[project.]table[/partition]> <path>
              download data to local file
 -c,-charset <ARG> specify file charset, default ignore.
                                   set ignore to download raw data
 -ci,-columns-index <ARG> specify the columns index(starts from
                                   0) to download, use comma to split each
                                   index
 -cn,-columns-name <ARG> specify the columns name to download,
                                   use comma to split each name
 -cp,-compress <ARG> compress, default true
 -dfp,-date-format-pattern <ARG> specify date format pattern, default
                                   yyyy-MM-dd HH:mm:ss
 -e,-exponential <ARG> When download double values, use
                                   exponential express if necessary.
                                   Otherwise at most 20 digits will be
                                   reserved. Default false
 -fd,-field-delimiter <ARG> specify field delimiter, support
                                   unicode, eg \u0001. default ","
 -h,-header <ARG> if local file should have table header,
                                   default false
    -limit <ARG> specify the number of records to
                                   download
 -ni,-null-indicator <ARG> specify null indicator string, default
                                   ""(empty string)
 -rd,-record-delimiter <ARG> specify record delimiter, support
                                   unicode, eg \u0001. default "\r\n"
 -sd,-session-dir <ARG> set session dir, default
                                   D:\software\odpscmd_public\plugins\dshi
                                   p
 -te,-tunnel_endpoint <ARG> tunnel endpoint
    -threads <ARG> number of threads, default 1
 -tz,-time-zone <ARG> time zone, default local timezone:
                                   Asia/Shanghai
usage: tunnel download [options] instance://<[project/]instance_id> <path>
              download instance result to local file
 -c,-charset <ARG> specify file charset, default ignore.
                                   set ignore to download raw data
 -ci,-columns-index <ARG> specify the columns index(starts from
                                   0) to download, use comma to split each
                                   index
 -cn,-columns-name <ARG> specify the columns name to download,
                                   use comma to split each name
 -cp,-compress <ARG> compress, default true
 -dfp,-date-format-pattern <ARG> specify date format pattern, default
                                   yyyy-MM-dd HH:mm:ss
 -e,-exponential <ARG> When download double values, use
                                   exponential express if necessary.
                                   Otherwise at most 20 digits will be
                                   reserved. Default false
 -fd,-field-delimiter <ARG> specify field delimiter, support
                                   unicode, eg \u0001. default ","
 -h,-header <ARG> if local file should have table header,
                                   default false
    -limit <ARG> specify the number of records to
                                   download
 -ni,-null-indicator <ARG> specify null indicator string, default
                                   ""(empty string)
 -rd,-record-delimiter <ARG> specify record delimiter, support
                                   unicode, eg \u0001. default "\r\n"
 -sd,-session-dir <ARG> set session dir, default
                                   D:\software\odpscmd_public\plugins\dshi
                                   p
 -te,-tunnel_endpoint <ARG> tunnel endpoint
    -threads <ARG> number of threads, default 1
 -tz,-time-zone <ARG> time zone, default local timezone:
                                   Asia/Shanghai
For example:
    tunnel download test_project.test_table/p1="b1",p2="b2" log.txt
    tunnel download instance://test_project/test_instance log.txt
```

**Parameters information:**

-   -c: Specifies the local data file encoding. Default value: UTF-8.
-   -ci: Specifies the column index \(starts from 0\) for downloading. Separate multiple entries with commas\(,\).
-   -cn: Specifies the names of the columns to download. Separate multiple entries with commas\(,\).
-   -cp, -compress: Determines whether the data is compressed before being downloaded, reducing traffic usage. It is enabled by default.
-   -dfp: Specifies the format of DateTime data. Default value: yyyy-MM-dd HH:mm:ss.
-   -e: When downloading Double type data, you can use this parameter to express the values as exponential functions. Otherwise, a maximum of 20 digits can be retained.
-   -fd: Specifies the column delimiter of the local data file. The default value is comma.
-   -h: Determines whether the data file contains the header. If set to ‘true’, Dship skips the header and starts downloading from the second row.

    **Note:** `-h=true` and `threads>1`cannot be used together.

-   -limit: Specifies the number of files to download.
-   -ni: Specifies the NULL data identifier. Default value: “  “\(blank string\).
-   -rd: Specifies the row delimiter of the local data file. Default value: \\r\\n.
-   -sd: Sets the session directory.
-   -te: Specifies the tunnel endpoint.
-   -threads: Specifies the number of threads. Default value: 1.
-   -tz: Specifies the time zone. The default value is the local time zone: Asia/Shanghai.

**Examples:**

To download data to the result.txt:

```
$ ./tunnel download sale_detail/sale_date=201312,region=hangzhou result.txt;
    Download session: 201506101658245283870a002ed0b9
    Total records: 2
    2015-06-10 16:58:24 download records: 2
    2015-06-10 16:58:24 file size: 30 bytes
    OK
```

To verify the content of the result.txt:

```
shop9,97,100.0
shop10,10,200.0
```

## Purge {#section_l3y_51g_vdb .section}

Purge the session directory. By default, sessions from the past 3 days are purged. The subcommands are used as follows:

```
odps@ project_name>tunnel help purge;
usage: tunnel purge [n]
              force session history to be purged.([n] days before, default
              3 days)
For example:
    tunnel purge 5
```

**Data types**:

| Type|Required?|
|:----|:--------|
|STRING|String type data. The length cannot exceed 8MB.|
|BOOLEN|Upload values are only supported for true, false, 0, and 1. Only the values true/false \(not case-sensitive\) are supported for downloading.|
|BIGINT|Value range: \[-9223372036854775807, 9223372036854775807\].|
|DOUBLE| -   16-bit valid
-   Uploads support expression in scientific notation.
-   Support only numerical expression for downloading.
-   Max value: 1.7976931348623157E308.
-   Min value: 4.9E-324.
-   Positive infinity: Infinity.
-   Negative infinity: -Infinity.

 |
|DATETIME|By default, Datetime data supports the UTC+8 time zone for data uploading. You can use the command to specify the format pattern for the date in your data.|

If you upload DATETIME type data, you must specify the time and date format. For more information on specific formats, see SimpleDateFormat.

```
"yyyyMMddHHmmss": data format "20140209101000"
"yyyy-MM-dd HH:mm:ss" (default): data format "2014-02-09 10:10:00"
"MM/dd/yyyy": data format "09/01/2014"
```

**Examples:**

```
tunnel upload log.txt test_table -dfp "yyyy-MM-dd HH:mm:ss"
```

**Null**: All data types can be Null.

-   By default, a blank string indicates a Null value.
-   The parameter -null-indicator can be used at the command line to specify the Null string.

```
tunnel upload log.txt test_table -ni "NULL"
```

**Character encoding**: You can specify the character encoding of the file. Default value: UTF-8.

```
tunnel upload log.txt test_table -c "gbk"
```

**Delimiter**: The Tunnel commands allow support custom file delimiters. The row delimiter is ‘-record-delimiter’, and the column delimiter is -field-delimiter.

Description:

-   Row and column delimiters of multiple characters are supported.
-   A column delimiter cannot contain the row delimiter.
-   Only the follow escape character delimiters are supported at the command line: \\r, \\n, and \\t.

**Examples:**

```
tunnel upload log.txt test_table -fd "||" -rd "\r\n"
```

