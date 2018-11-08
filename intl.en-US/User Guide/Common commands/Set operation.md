# Set operation {#concept_yys_xby_rfb .concept}

This article shows you how to use the set command to set maxcompute or a user-defined system variable, and how to clear the set command settings.

## Set {#section_pqs_bff_vdb .section}

Command Format:

```
Set <key> = <value>
```

Actioin:

You can use the set command to set MaxCompute or a user-defined system variables that affects the MaxCompute operation.

**Currently, the system variables supported in MaxCompute are as follow:**

```
--Set commands supported by MaxCompute SQL and Mapreduce (new version)
set odps.sql.allow.fullscan=  --Set whether to allow a full table scan on a partitioned table. True means allow, and false means not allow.
set odps.stage.mapper.mem=   --Set the memory size of each map worker. Unit is M and default value is 1024M.
set odps.stage.reducer.mem= --Set the memory size for each reduce worker in the unit of M. The default value is 1,024M.
set odps.stage.joiner.mem=   --Set the memory size of each join worker. Unit is M and default value is 1024M.
set odps.stage.mem =    --Set the memory size of all workers in MaxCompute specified job. The priority is lower than preceding three set key. Unit is M and no default value.
set odps.stage.mapper.split.size=   -- Modify the input data quantity of each map worker; that is the size of input file burst. Thus control the worker number of each map stage. Unit is M and the default value is 256M.
set odps.stage.reducer.num=    --Modify the worker number of each reduce stage and no default value.
set odps.stage.joiner.num=    --Modify the worker number of each join stage and no default value.
set odps.stage.num=    --Modify the worker concurrency of all stages in MaxCompute specified job. The priority is lower than preceding three set key and no default value.
set odps.sql.type.system.odps2=    --The default value is false. You must set true when there are new data types such as TINYINT, SMALLINT, INT, FLOAT, VARCHAR, TIMESTAMP, and BINARY in SQL statement.
```

## Show Flags {#section_ft4_jff_vdb .section}

Command Format:

```
show flags;  --Display the parameters set by the Set command.
```

Action:

Running the Use Project command can clear the configurations set by the Set command.

