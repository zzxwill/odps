# MapReduce Common Errors {#concept_znv_42w_tdb .concept}

|Error message|Trigger condition|
|:------------|:----------------|
|ODPS-0710005：Exception occurred when connecting to meta store|Meta store connection error.|
|ODPS-0710015 MR task encountered meta store exception|A meta store error occurs when an MR task is running.|
|ODPS-0710025：Get partitions from meta store error|An error occurs when you obtain partition information from metastore.|
|ODPS-0720001： Invalid table name format|The table name format specified by the user is invalid.|
|ODPS-0720015：PartKeys size do not match partVals size|Table schema partitions are inconsistent.|
|ODPS-0720021： Column does not exist|The specified column does not exist.|
|ODPS-0720031 PARSER Cache resource between comma must not be empty|The specified resource list has blank spaces between commas.|
|ODPS-0720041：Resource not found|The specified resource does not exist.|
|ODPS-0720051：Resource table should not be a view|The specified resource table is a view.|
|ODPS-0720061： Table or partition not found for resource|A non-existing table or table partition is specified as the resource.|
|ODPS-0720071： Total size of cache resources is too big|The total number or size \(256/512 MB by default\) of the specified resource exceeds the upper limit.|
|ODPS-0720081： Job has not specified mapper class|The Mapper class is not specified for the job.|
|ODPS-0720091：jColumn duplicate|Repeated columns are specified in OB input.|
|ODPS-0720101： Input table should not be a view|The input table is a view.|
|ODPS-0720111：Output table should not be a view|The output table is a view.|
|ODPS-0720121：Invalid table partSpec|The specified table partSpec is invalid.|
|ODPS-0720131：Invalid multiple output|The multi output \(including duplicate label, same output tables, and  partitions\) is invalid.|
|ODPS-0720141： Memory value out of bound|The specified memory value is out of the range \[256 MB, 12 GB\].|
|ODPS-0720151： Cpu value out of bound|The specified CPU value is out of the range \[50, 800\].|
|ODPS-0720161： Invalid max attempts value|The specified value of odps.mapred.map/reduce.max.attempts is out of the range \(0, 6\).|
|ODPS-0720171 ： Invalid IO sort buffer|The specified I/O sort buffer is out of the range \(64 MB, odps.mapred.map/reduce.memory\).|
|ODPS-0720181：Classpath resource between comma must not be empty|Content between commas in classpath is null.|
|ODPS-0720191：Invalid input split mode|The specified split mod is invalid.|
|ODPS-0720201：Invalid map split size|The specified odps.mapred.map.min/max.split.size is invalid.|
|ODPS-0720211：Invalid number of map tasks|The specified odps.mapred.map.tasks is out of the range \[1, 100,000\].|
|ODPS-0720221：Invalid max splits number|The specified max sample split is invalid.|
|ODPS-0720231：Job input not set|No input is specified for the job.|
|ODPS-0720241：Num of map instance is too big|The number of map instances is greater than that set by odps.mapred.max.map.tasks.|
|ODPS-0720251： Num of reduce instance is invalid|The number of reduce instances is out of the range \[0, odps.mapred.reduce.tasks\].|
|ODPS-0720261：Invalid partition value|The specified partition value is invalid.|
|ODPS-0720271： Allow no input is conflict to split mode|When setting setallowNoInput\(true\), you set a split mode different from that of ALLOW\_NO\_INPUT.|
|ODPS-0720281: Invalid partitition format|The partition format is invalid.|
|ODPS-0720291:Invalid description json|The format of input description JSON is invalid.|
|ODPS-0720301:Too many job input|The specified input number exceeds odps.mapred.map.max.input.num \(1024 by default\).|
|ODPS-0720311:Invalid output label|The format of the output label is invalid.|
|ODPS-0720321: Too many job output|The specified output number exceeds odps.mapred.max.output.num \(256 by default\).|
|ODPS-0720331: Too many cache resources|The specified resource number exceeds odps.mapred.job.max.cache.resource.num.|
|ODPS-0730005:MR job internal error|An internal error that is out of user control occurs in MR task.|

