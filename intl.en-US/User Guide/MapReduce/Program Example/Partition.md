# Partition {#concept_lnj_ngl_vdb .concept}

The following example takes Partition as input and output.

Example 1:

```
     public static void main(String[] args) throws Exception {
     JobConf job = new JobConf();
     
     LinkedHashMap<String, String> input = new LinkedHashMap<String, String>();
     input.put("pt", "123456");
     InputUtils.addTable(TableInfo.builder().tableName("input_table").partSpec(input).build(), job);
     LinkedHashMap<String, String> output = new LinkedHashMap<String, String>();
     output.put("ds", "654321");
     Outpututils. addtable (tableinfo. builder (). tablename ("output_table "). partspec (output ). build (), job );
     JobClient.runJob(job);
    

```

Example 2:

```
    package com.aliyun.odps.mapred.open.example;
    
      public static void main(String[] args) throws Exception {
        if (args.length ! = 2) {
          System.err.println("Usage: WordCount <in_table> <out_table>");
          System.exit(2);
        
        JobConf job = new JobConf();
        job.setMapperClass(TokenizerMapper.class);
        job.setCombinerClass(SumCombiner.class);
        job.setReducerClass(SumReducer.class);
        job.setMapOutputKeySchema(SchemaUtils.fromString("word:string"));
        job.setMapOutputValueSchema(SchemaUtils.fromString("count:bigint"));
        Account account = new AliyunAccount("my_access_id", "my_access_key");
        Odps odps = new Odps(account);
        odps.setEndpoint("odps_endpoint_url");
        odps.setDefaultProject("my_project");
        Table table = odps.tables().get(tblname);
        TableInfoBuilder builder = TableInfo.builder().tableName(tblname);
        for (Partition p : table.getPartitions()) {
          if (applicable(p)) {
            LinkedHashMap<String, String> partSpec = new LinkedHashMap<String, String>();
            for (String key : p.getPartitionSpec().keys()) {
              partSpec.put(key, p.getPartitionSpec().get(key));
            
            InputUtils.addTable(builder.partSpec(partSpec).build(), conf);
          
        
        OutputUtils.addTable(TableInfo.builder().tableName(args[1]).build(), job);
        Jobclient. runjob (job );
      

```

**Note:** 

-   The preceding example combines the MaxCompute SDK and MapReduce SDK to achieve  a MapReduce task.
-   The code cannot be compiled and is only an example of main functions.
-   The Applicable function is user logic that determines whether the Partition can be used as the input of MapReduce job.

