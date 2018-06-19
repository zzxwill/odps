# Tunnel common errors {#concept_ccs_p2w_tdb .concept}

The following is a common Tunnel exception message:

```
requestId=request_id, ErrorCode=InvalidArgument, ErrorMessage=Invalid argument
```

In the message, requestId indicates the unique ID of the request, ErrorCode indicates the error type, and ErrorMessage indicates the exception details.

The following table describes the common exception messages.

|Exception type|Exception information|Description|
|:-------------|:--------------------|:----------|
|AccessDenied|Access is denied.|You are unauthorized.|
|CorruptedDataStream|The data stream was corrupted, please try again later| |
|DataUnderReplication|The specified table data is under replication and you cannot initiate  upload or download at this time.  Please try again later.|The data is being copied across clusters and cannot be operated.|
|DataVersionConflict|The specified table has been modified since the upload or download  initiated and table data is being replicated at this time.  Please  initiate another download or upload later.|The data on the cluster is being copied and cannot be operated.  Retry later.|
|DataStoreError|Storage error|We recommend that you contact the administrator.|
|FlowExceeded|Your flow quota is exceeded|The data upload/download traffic exceeds the traffic limit.  We recommend that you check concurrency control. If you want to increase concurrency, contact the project owner or administrator to evaluate the traffic pressure.|
|InConsistentBlockList|The specified block list is not consistent with the uploaded block  list on server side.| |
|IncompleteBody|You did not provide the number of bytes specified by the  Content-Length HTTP header.| |
|InternalServerError|Service internal error, please try again later.|An internal server error occurs. We recommend that you retry or contact the administrator.|
|InvalidArgument| Invalid argument.|The parameter is invalid.|
|InvalidBlockID|The specified block id is not valid.|The Block ID is invalid.|
|InvalidColumnSpec|The specified columns is not valid.|The column name is invalid. This error is often caused by incorrect column name when you are specifying the columns to download.|
|InvalidRowRange|The specified row range is not valid.|The specified number of rows is invalid. Generally, the value is zero or exceeds the maximum data size. We recommend that you check related parameters.|
|InvalidStatusChange|You cannot change the specified upload or download status.| |
|InvalidResourceSpec|Normally because Project/Table/Partition information is different from Session|We recommend that you check related information and retry.|
|InvalidURI|Could not parse the specified URI.| |
|InvalidUriSpec|The specified uri spec is not valid| |
|InvalidProjectTable|Invalid project name or table name|We recommend that you check the project and table names.|
|InvalidPartitionSpec|Invalid partition information|Check the partition information. The correct value is as follows: pt=’1’, ct=’2016’.|
|MalformedDataStream|The data stream you provided was not well-formed or did not validate  against schema.|The data format is incorrect. This error is often caused by network disconnection or inconsistency between the schema and table.|
|MalformedHeaderValue|An HTTP header value was malformed.| |
|MalformedXML|The XML you provided was not well-formed or did not validate against  schema.| |
|MaxMessageLengthExceeded|Your request was too big.| |
|MethodNotAllowed|The specified method is not allowed against this resource.|The method that exports the view is not supported.  Currently, views cannot be exported.|
|MissingContentLength|You must provide the Content-Length HTTP header.| |
|MissingPartitionSpec|You must specify a partitionspec along with the specified  table.|Partition information is missing. The partition table actions must carry partition information. We recommend that you set the partition information.|
|MissingRequestBodyError|The request body is missing.| |
|MissingRequiredHeaderError|Your request was missing a required header.| |
|NoPermission|You do not have enough privilege to complete the specified  operation.| |
|NoSuchData|The uploaded data within this upload no longer exists.| |
|NoSuchPartition|The specified partition does not exist.|The specified partition does not exist. The Tunnel does not create partitions. You must create the partition for upload and download.|
|NoSuchUpload|The specified upload id does not exist.|The specified upload ID does not exist.|
|NoSuchDownload|The specified download id does not exist.|The specified download ID does not exist.|
|NoSuchProject|The specified project name does not exist|The project does not exist and it is recommended that you check the relevant names.|
|NoSuchTable|The specified table name does not exist|Table does not exist, and it is recommended that to check relevant name.|
|NoSuchVolume|The specified volume name does not exist|The specified volume name does not exist.|
|NoSuchVolumeFile|The specified volume file does not exist|The specified volume file does not exist.|
|Nosuchvolumepartition|The specified partition does not exist.|The specified volume partition does not exist.|
|NoPermission|No permission normally because no related permission or IP whitelist has been set|It is recommended that to check the permissions.|
|NotImplemented|A header you provided implies functionality that is not Implemented| |
|Objectmodified|The specified object has been modified since the specified timestamp| |
|Requesttimeout|Your socket connection to the server was not read from or written Within the timeout period| |
|ServiceUnavailable|Service is mostly unavailable, please try again later| |
|StatusConflict|You cannot complete the specified operation under the current upload or download status?|Session has expired or has been committed to recreate the session upload.|
|TableModified|The specified table has been modified since the upload or download Try initiate another download|The data is changed by other tasks during the upload or download, and it is recommended that you recreate the session and try again.|
|Unauthorized|The Request Authorization header is invalid or missing?|Normally it is wrong AccessId or AccessKey, or local device time has a 15-minute gap with server.|
|Unexpectedcontent|This request does not support content| |

