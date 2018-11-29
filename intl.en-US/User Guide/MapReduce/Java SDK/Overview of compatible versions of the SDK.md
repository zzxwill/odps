# Overview of compatible versions of the SDK {#concept_ytg_mcg_vdb .concept}

A detailed list of maxcompute compatible versions of mapreduce compatibility with hadoop mapreduce, as shown in the following table:

|Type|Interface|Is it compatible?|
|:---|:--------|:----------------|
|Mapper|void map\(KEYIN key, VALUEIN value, org.apache.hadoop.mapreduce.Mapper.Context context\)|Yes|
|Mapper|void run\(org.apache.hadoop.mapreduce.Mapper.Context context\)|Yes|
|Mapper|void setup\(org.apache.hadoop.mapreduce.Mapper.Context context\)|Yes|
|Reducer|Void cleanup \(Org. Apache. hadoop. mapreduce. reducer. Context Context\)|Yes|
|Reducer|void reduce\(KEYIN key, VALUEIN value, org.apache.hadoop.mapreduce.Reducer.Context context\)|Yes|
|Reducer|void run\(org.apache.hadoop.mapreduce.Reducer.Context context\)|Yes|
|Reducer|void setup\(org.apache.hadoop.mapreduce.Reducer.Context context\)|Yes|
|Partitioner|int getPartition\(KEY key, VALUE value, int numPartitions\)|Yes|
|Mapcontext \(inheritance\)|InputSplit getInputSplit\(\)|No, throw exception|
|ReduceContext|nextKey\(\)|Yes|
|ReduceContext|getValues\(\)|Yes|
|TaskInputOutputContext|getCurrentKey\(\)|Yes|
|TaskInputOutputContext|getCurrentValue\(\)|Yes|
|TaskInputOutputContext|getOutputCommitter\(\)|No, throw exception|
|TaskInputOutputContext|nextKeyValue\(\)|Yes|
|TaskInputOutputContext|write\(KEYOUT key, VALUEOUT value\)|Yes|
|TaskAttemptContext|getCounter\(Enum < \> counterName\)|Yes|
|TaskAttemptContext|getCounter\(String groupName, String counterName\)|Yes|
|TaskAttemptContext|setStatus\(String msg\)|Empty implementation|
|TaskAttemptContext|getStatus\(\)|Empty implementation|
|TaskAttemptContext|getTaskAttemptID\(\)|No, throw exception|
|TaskAttemptContext|getProgress\(\)|No, throw exception|
|TaskAttemptContext|progress\(\)|Yes|
|Job|addArchiveToClassPath\(Path archive\)|No|
|Job|addCacheArchive\(URI uri\)|No|
|Job|addCacheFile\(URI uri\)|No|
|Job|addFileToClassPath\(Path file\)|No|
|Job|cleanupProgress\(\)|No|
|Job|createSymlink\(\)|No, throw exception|
|Job|failTask\(TaskAttemptID taskId\)|No|
|Job|getCompletionPollInterval\(Configuration conf\)|Empty implementation|
|Job|getCounters\(\)|Yes|
|Job|getFinishTime\(\)|Yes|
|Job|getHistoryUrl\(\)|Yes|
|Job|getInstance\(\)|Yes|
|Job|getInstance\(Cluster ignored\)|Yes|
|Job|getInstance\(Cluster ignored, Configuration conf\)|Yes|
|Job|getInstance\(Configuration conf\)|Yes|
|Job|getInstance\(Configuration conf, String jobName\)|Empty implementation|
|Job|getInstance\(JobStatus status, Configuration conf\)|No, throw exception|
|Job|getJobFile\(\)|No, throw exception|
|Job|getJobName\(\)|Empty implementation|
|Job|getJobState\(\)|No, throw exception|
|Job|getPriority\(\)|No, throw exception|
|Job|getProgressPollInterval\(Configuration conf\)|Empty implementation|
|Job|getReservationId\(\)|No, throw exception|
|Job|getSchedulingInfo\(\)|No, throw exception|
|Job|getStartTime\(\)|Yes|
|Job|getStatus\(\)|No, throw exception|
|Job|getTaskCompletionEvents\(int startFrom\)|No, throw exception|
|Job|getTaskCompletionEvents\(int startFrom, int numEvents\)|No, throw exception|
|Job|getTaskDiagnostics\(TaskAttemptID taskid\)|No, throw exception|
|Job|getTaskOutputFilter\(Configuration conf\)|No, throw exception|
|Job|getTaskReports\(TaskType type\)|No, throw exception|
|Job|getTrackingURL\(\)|Yes|
|Job|isComplete\(\)|Yes|
|Job|isRetired\(\)|No, throw exception|
|Job|isSuccessful\(\)|Yes|
|Job|isUber\(\)|Empty implementation|
|Job|killJob\(\)|Yes|
|Job|killTask\(TaskAttemptID taskId\)|No|
|Job|mapProgress\(\)|Yes|
|Job|monitorAndPrintJob\(\)|Yes|
|Job|reduceProgress\(\)|Yes|
|Job|setCacheArchives\(URI\[\] archives\)|No, throw exception|
|Job|setCacheFiles\(URI\[\] files\)|No, throw exception|
|Job|setCancelDelegationTokenUponJobCompletion\(boolean value\)|No, throw exception|
|Job|setCombinerClass\(Class<? extends Reducer\> cls\)|Yes|
|Job|setCombinerKeyGroupingComparatorClass\(Class<? extends RawComparator\> cls\)|Yes|
|Job|setGroupingComparatorClass\(Class<? extends RawComparator\> cls\)|Yes|
|Job|setInputFormatClass\(Class<? extends InputFormat\> cls\)|Empty implementation|
|Job|setJar\(String jar\)|Yes|
|Job|setJarByClass\(Class<? \> cls\)|Yes|
|Job|setJobName\(String name\)|Empty implementation|
|Job|setJobSetupCleanupNeeded\(boolean needed\)|Empty implementation|
|Job|setMapOutputKeyClass\(Class<? \> theClass\)|Yes|
|Job|setMapOutputValueClass\(Class<? \> theClass\)|Yes|
|Job|setMapperClass\(Class<? extends Mapper\> cls\)|Yes|
|Job|setMapSpeculativeExecution\(boolean speculativeExecution\)|Empty implementation|
|Job|setMaxMapAttempts\(int n\)|Empty implementation|
|Job|setMaxReduceAttempts\(int n\)|Empty implementation|
|Job|setNumReduceTasks\(int tasks\)|Yes|
|Job|setOutputFormatClass\(Class<? extends OutputFormat\> cls\)|No, throw exception|
|Job|setOutputKeyClass\(Class<? \> theClass\)|Yes|
|Job|setOutputValueClass\(Class<? \> theClass\)|Yes|
|Job|setPartitionerClass\(Class<? extends Partitioner\> cls\)|Yes|
|Job|setPriority\(JobPriority priority\)|No, throw exception|
|Job|setProfileEnabled\(boolean newValue\)|Empty implementation|
|Job|setProfileParams\(String value\)|Empty implementation|
|Job|setProfileTaskRange\(boolean isMap, String newValue\)|Empty implementation|
|Job|setReducerClass\(Class<? extends Reducer\> cls\)|Yes|
|Job|setReduceSpeculativeExecution\(boolean speculativeExecution\)|Empty implementation|
|Job|setReservationId\(ReservationId reservationId\)|No, throw exception|
|Job|setSortComparatorClass\(Class<? extends RawComparator\> cls\)|No, throw exception|
|Job|setSpeculativeExecution\(boolean speculativeExecution\)|Yes|
|Job|setTaskOutputFilter\(Configuration conf, org.apache.hadoop.mapreduce.Job.TaskStatusFilter newValue\)|No, throw exception|
|Job|setupProgress\(\)|No, throw exception|
|Job|setUser\(String user\)|Empty implementation|
|Job|setWorkingDirectory\(Path dir\)|Empty implementation|
|Job|submit\(\)|Yes|
|Job|toString\(\)|No, throw exception|
|Job|waitForCompletion\(boolean verbose\)|Yes.|
|Task Execution & Environment|mapreduce.map.java.opts|Empty implementation|
|Task Execution & Environment|mapreduce.reduce.java.opts|Empty implementation|
|Task Execution & Environment|mapreduce.map.memory.mb|Empty implementation|
|Task Execution & Environment|mapreduce.reduce.memory.mb|Empty implementation|
|Task Execution & Environment|mapreduce.task.io.sort.mb|Empty implementation|
|Task Execution & Environment|mapreduce.map.sort.spill.percent|Empty implementation|
|Task Execution & Environment|mapreduce.task.io.soft.factor|Empty implementation|
|Task Execution & Environment|mapreduce.reduce.merge.inmem.thresholds|Empty implementation|
|Task Execution & Environment|mapreduce.reduce.shuffle.merge.percent|Empty implementation|
|Task Execution & Environment|mapreduce.reduce.shuffle.input.buffer.percent|Empty implementation|
|Task Execution & Environment|mapreduce.reduce.input.buffer.percent|Empty implementation|
|Task Execution & Environment|mapreduce.job.id|Empty implementation|
|Task Execution & Environment|mapreduce.job.jar|Empty implementation|
|Task Execution & Environment|mapreduce.job.local.dir|Empty implementation|
|Task Execution & Environment|mapreduce.task.id|Empty implementation|
|Task Execution & Environment|mapreduce.task.attempt.id|Empty implementation|
|Task Execution & Environment|mapreduce.task.is.map|Empty implementation|
|Task Execution & Environment|mapreduce.task.partition|Empty implementation|
|Task Execution & Environment|mapreduce.map.input.file|Empty implementation|
|Task Execution & Environment|mapreduce.map.input.start|Empty implementation|
|Task Execution & Environment|mapreduce.map.input.length|Empty implementation|
|Task Execution & Environment|mapreduce.task.output.dir|Empty implementation|
|JobClient|cancelDelegationToken\(Token <DelegationTokenIdentifier\> token\)|No, throw exception|
|JobClient|close\(\)|Empty implementation|
|JobClient|displayTasks\(JobID jobId, String type, String state\)|No, throw exception|
|JobClient|getAllJobs\(\)|No, throw exception|
|JobClient|getCleanupTaskReports\(JobID jobId\)|No, throw exception|
|JobClient|getClusterStatus\(\)|No, throw exception|
|JobClient|getClusterStatus\(boolean detailed\)|No, throw exception|
|JobClient|getDefaultMaps\(\)|No, throw exception|
|JobClient|getDefaultReduces\(\)|No, throw exception|
|JobClient|getDelegationToken\(Text renewer\)|No, throw exception|
|JobClient|getFs\(\)|No, throw exception|
|JobClient|getJob\(JobID jobid\)|No, throw exception|
|JobClient|getJob\(String jobid\)|No, throw exception|
|JobClient|getJobsFromQueue\(String queueName\)|No, throw exception|
|JobClient|getMapTaskReports\(JobID jobId\)|No, throw exception|
|JobClient|getMapTaskReports\(String jobId\)|No, throw exception|
|JobClient|getQueueAclsForCurrentUser\(\)|No, throw exception|
|JobClient|getQueueInfo\(String queueName\)|No, throw exception|
|JobClient|getQueues\(\)|No, throw exception|
|JobClient|getReduceTaskReports\(JobID jobId\)|No, throw exception|
|JobClient|getReduceTaskReports\(String jobId\)|No, throw exception|
|JobClient|getSetupTaskReports\(JobID jobId\)|No, throw exception|
|JobClient|getStagingAreaDir\(\)|No, throw exception|
|JobClient|getSystemDir\(\)|No, throw exception|
|JobClient|getTaskOutputFilter\(\)|No, throw exception|
|JobClient|getTaskOutputFilter\(JobConf job\)|No, throw exception|
|JobClient|init\(JobConf conf\)|No, throw exception|
|JobClient|isJobDirValid\(Path jobDirPath, FileSystem fs\)|No, throw exception|
|JobClient|jobsToComplete\(\)|No, throw exception|
|JobClient|monitorAndPrintJob\(JobConf conf, RunningJob job\)|No, throw exception|
|JobClient|renewDelegationToken\(Token<DelegationTokenIdentifier\> token\)|No, throw exception|
|JobClient|run\(String\[\] argv\)|No, throw exception|
|JobClient|runJob\(JobConf job\)|Yes|
|JobClient|setTaskOutputFilter\(JobClient.TaskStatusFilter newValue\)|No, throw exception|
|JobClient|setTaskOutputFilter\(JobConf job, JobClient.TaskStatusFilter newValue\)|No, throw exception|
|JobClient|submitJob\(JobConf job\)|Yes|
|JobClient|submitJob\(String jobFile\)|No, throw exception|
|JobConf|deleteLocalFiles\(\)|No, throw exception|
|Jobconf|deleteLocalFiles\(String subdir\)|No, throw exception|
|Jobconf|normalizeMemoryConfigValue\(long val\)|Empty implementation|
|Jobconf|setCombinerClass\(Class<? extends Reducer\> theClass\)|Yes|
|Jobconf|setCompressMapOutput\(boolean compress\)|Empty implementation|
|Jobconf|setInputFormat\(Class<? extends InputFormat\> theClass\)|No, throw exception|
|JobConf|setJar\(String jar\)|No, throw exception|
|JobConf|setJarByClass\(Class cls\)|No, throw exception|
|JobConf|setJobEndNotificationURI\(String uri\)|No, throw exception|
|JobConf|setJobName\(String name\)|Empty implementation|
|JobConf|setJobPriority\(JobPriority prio\)|No, throw exception|
|JobConf|setKeepFailedTaskFiles\(boolean keep\)|No, throw exception|
|JobConf|setKeepTaskFilesPattern\(String pattern\)|No, throw exception|
|JobConf|setKeyFieldComparatorOptions\(String keySpec\)|No, throw exception|
|JobConf|setKeyFieldPartitionerOptions\(String keySpec\)|No, throw exception|
|JobConf|setMapDebugScript\(String mDbgScript\)|Empty implementation|
|JobConf|setMapOutputCompressorClass\(Class<? extends CompressionCodec\> codecClass\)|Empty implementation|
|JobConf|setMapOutputKeyClass\(Class<? \> theClass\)|Yes|
|JobConf|setMapOutputValueClass\(Class<? \> theClass\)|Yes|
|JobConf|setMapperClass\(Class<? extends Mapper\> theClass\)|Yes|
|JobConf|setMapRunnerClass\(Class<? extends MapRunnable\> theClass\)|No, throw exception|
|JobConf|setMapSpeculativeExecution\(boolean speculativeExecution\)|Empty implementation|
|JobConf|setMaxMapAttempts\(int n\)|Empty implementation|
|JobConf|setMaxMapTaskFailuresPercent\(int percent\)|Empty implementation|
|JobConf|setMaxPhysicalMemoryForTask\(long mem\)|Empty implementation|
|JobConf|setMaxReduceAttempts\(int n\)|Empty implementation|
|JobConf|setMaxReduceTaskFailuresPercent\(int percent\)|Empty implementation|
|JobConf|setMaxTaskFailuresPerTracker\(int noFailures\)|Empty implementation|
|JobConf|setMaxVirtualMemoryForTask\(long vmem\)|Empty implementation|
|JobConf|setMemoryForMapTask\(long mem\)|Yes|
|JobConf|setMemoryForReduceTask\(long mem\)|Yes|
|JobConf|setNumMapTasks\(int n\)|Yes|
|JobConf|setNumReduceTasks\(int n\)|Yes|
|JobConf|setNumTasksToExecutePerJvm\(int numTasks\)|Empty implementation|
|JobConf|setOutputCommitter\(Class<? extends OutputCommitter\> theClass\)|No, throw exception|
|JobConf|setOutputFormat\(Class<? extends OutputFormat\> theClass\)|Empty implementation|
|JobConf|setOutputKeyClass\(Class<? \> theClass\)|Yes|
|JobConf|setOutputKeyComparatorClass\(Class<? extends RawComparator\> theClass\)|No, throw exception|
|JobConf|setOutputValueClass\(Class<? \> theClass\)|Yes|
|JobConf|setOutputValueGroupingComparator\(Class<? extends RawComparator\> theClass\)|No, throw exception|
|JobConf|setPartitionerClass\(Class<? extends Partitioner\> theClass\)|Yes|
|JobConf|setProfileEnabled\(boolean newValue\)|Empty implementation|
|JobConf|setProfileParams\(String value\)|Empty implementation|
|JobConf|setProfileTaskRange\(boolean isMap, String newValue\)|Empty implementation|
|JobConf|setQueueName\(String queueName\)|No, throw exception|
|JobConf|setReduceDebugScript\(String rDbgScript\)|Empty implementation|
|JobConf|setReducerClass\(Class<? extends Reducer\> theClass\)|Yes|
|JobConf|setReduceSpeculativeExecution\(boolean speculativeExecution\)|Empty implementation|
|JobConf|setSessionId\(String sessionId\)|Empty implementation|
|JobConf|setSpeculativeExecution\(boolean speculativeExecution\)|No, throw exception|
|JobConf|setUseNewMapper\(boolean flag\)|Yes|
|JobConf|setUseNewReducer\(boolean flag\)|Yes|
|JobConf|setUser\(String user\)|Empty implementation|
|JobConf|setWorkingDirectory\(Path dir\)|Empty implementation|
|FileInputFormat|Not involved|No, throw exception|
|TextInputFormat|Not involved|Yes|
|InputSplit|mapred.min.split.size.|No, throw exception|
|FileSplit|map.input.file|No, throw exception|
|RecordWriter|Not involved|No, throw exception|
|RecordReader|Not involved|No, throw exception|
|OutputFormat|Not involved|No, throw exception|
|OutputCommitter|abortJob\(JobContext jobContext, int status\)|No, throw exception|
|OutputCommitter|abortJob\(JobContext context, JobStatus.State runState\)|No, throw exception|
|OutputCommitter|abortTask\(TaskAttemptContext taskContext\)|No, throw exception|
|OutputCommitter|abortTask\(TaskAttemptContext taskContext\)|No, throw exception|
|OutputCommitter|cleanupJob\(JobContext jobContext\)|No, throw exception|
|OutputCommitter|cleanupJob\(JobContext context\)|No, throw exception|
|OutputCommitter|commitJob\(JobContext jobContext\)|No, throw exception|
|OutputCommitter|commitJob\(JobContext context\)|No, throw exception|
|OutputCommitter|commitTask\(TaskAttemptContext taskContext\)|No, throw exception|
|OutputCommitter|needsTaskCommit\(TaskAttemptContext taskContext\)|No, throw exception|
|OutputCommitter|needsTaskCommit\(TaskAttemptContext taskContext\)|No, throw exception|
|OutputCommitter|setupJob\(JobContext jobContext\)|No, throw exception|
|OutputCommitter|setupJob\(JobContext jobContext\)|No, throw exception|
|OutputCommitter|setupTask\(TaskAttemptContext taskContext\)|No, throw exception|
|OutputCommitter|setupTask\(TaskAttemptContext taskContext\)|No, throw exception|
|Counter|getDisplayName\(\)|Yes|
|Counter|getName\(\)|Yes|
|Counter|getValue\(\)|Yes|
|Counter|increment\(long incr\)|Yes|
|Counter|setValue\(long value\)|Yes|
|Counter|setDisplayName\(String displayName\)|Yes|
|DistributedCache|CACHE\_ARCHIVES|No, throw exception|
|DistributedCache|CACHE\_ARCHIVES\_SIZES|No, throw exception|
|DistributedCache|CACHE\_ARCHIVES\_TIMESTAMPS|No, throw exception|
|Distributed cache|CACHE\_FILES|No, throw exception|
|DistributedCache|CACHE\_FILES\_SIZES|No, throw exception|
|DistributedCache|CACHE\_FILES\_TIMESTAMPS|No, throw exception|
|DistributedCache|CACHE\_LOCALARCHIVES|No, throw exception|
|DistributedCache|CACHE\_LOCALFILES|No, throw exception|
|DistributedCache|CACHE\_SYMLINK|No, throw exception|
|DistributedCache|addArchiveToClassPath\(Path archive, Configuration conf\)|No, throw exception|
|DistributedCache|addArchiveToClassPath\(Path archive, Configuration conf, FileSystem fs\)|No, throw exception|
|DistributedCache|addCacheArchive\(URI uri, Configuration conf\)|No, throw exception|
|DistributedCache|addCacheFile\(URI uri, Configuration conf\)|No, throw exception|
|DistributedCache|addFileToClassPath\(Path file, Configuration conf\)|No, throw exception|
|DistributedCache|addFileToClassPath\(Path file, Configuration conf, FileSystem fs\)|No, throw exception|
|DistributedCache|addLocalArchives\(Configuration conf, String str\)|No, throw exception|
|DistributedCache|addLocalFiles\(Configuration conf, String str\)|No, throw exception|
|DistributedCache|checkURIs\(URI\[\] uriFiles, URI\[\] uriArchives\)|No, throw exception|
|DistributedCache|createAllSymlink\(Configuration conf, File jobCacheDir, File workDir\)|No, throw exception|
|DistributedCache|createSymlink\(Configuration conf\)|No, throw exception|
|DistributedCache|getArchiveClassPaths\(Configuration conf\)|No, throw exception|
|DistributedCache|getArchiveTimestamps\(Configuration conf\)|No, throw exception|
|DistributedCache|getCacheArchives\(Configuration conf\)|No, throw exception|
|DistributedCache|getCacheFiles\(Configuration conf\)|No, throw exception|
|DistributedCache|getFileClassPaths\(Configuration conf\)|No, throw exception|
|DistributedCache|getFileStatus\(Configuration conf, URI cache\)|No, throw exception|
|DistributedCache|getFileTimestamps\(Configuration conf\)|No, throw exception|
|DistributedCache|getLocalCacheArchives\(Configuration conf\)|No, throw exception|
|DistributedCache|getLocalCacheFiles\(Configuration conf\)|No, throw exception|
|DistributedCache|getSymlink\(Configuration conf\)|No, throw exception|
|DistributedCache|getTimestamp\(Configuration conf, URI cache\)|No, throw exception|
|DistributedCache|setArchiveTimestamps\(Configuration conf, String timestamps\)|No, throw exception|
|DistributedCache|setCacheArchives\(URI\[\] archives, Configuration conf\)|No, throw exception|
|DistributedCache|setCacheFiles\(URI\[\] files, Configuration conf\)|No, throw exception|
|DistributedCache|setFileTimestamps\(Configuration conf, String timestamps\)|No, throw exception|
|DistributedCache|setLocalArchives\(Configuration conf, String str\)|No, throw exception|
|DistributedCache|setLocalFiles\(Configuration conf, String str\)|No, throw exception|
|IsolationRunner|Not involved|No, throw exception|
|Profiling|Not involved|Empty implementation|
|Debugging|Not involved|Empty implementation|
|Data Compression|Not involved|Yes|
|Skipping Bad Records|Not involved|No, throw exception|
|Job Authorization|mapred.acls.enabled|No, throw exception|
|Job Authorization|mapreduce.job.acl-view-job|No, throw exception|
|Job Authorization|mapreduce.job.acl-modify-job|No, throw exception|
|Job Authorization|mapreduce.cluster.administrators|No, throw exception|
|Job Authorization|mapred.queue.queue-name.acl-administer-jobs|No, throw exception|
|MultipleInputs|Not involved|No, throw exception|
|Multi\{anchor:\_GoBack\}pleOutputs|Not involved|Yes|
|org.apache.hadoop.mapreduce.lib.db|Not involved|No, throw exception|
|org.apache.hadoop.mapreduce.security|Not involved|No, throw exception|
|org.apache.hadoop.mapreduce.lib.jobcontrol|Not involved|No, throw exception|
|org.apache.hadoop.mapreduce.lib.chain|Not involved|No, throw exception|
|org.apache.hadoop.mapreduce.lib.db|Not involved|No, throw exception|

