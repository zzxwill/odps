# Resource {#concept_pps_h1f_vdb .concept}

This article shows how to use the common commands to operate resources in the MaxCompute client.

You can also search and upload resources using the visualized online data development tools in DataWorks. For more information, see [Resource management](https://www.alibabacloud.com/help/doc-detail/56960.html).

## Add resource {#section_lqp_41f_vdb .section}

**The command format is as follows:**

```
add file <local_file> [as alias] [comment 'cmt'][-f];
add archive <local_file> [as alias] [comment 'cmt'][-f];
add table <table_name> [partition <(spec)>] [as alias] [comment 'cmt'][-f];
add jar <local_file.jar> [comment 'cmt'][-f];
```

**Parameters information:**

-   file/archive/table/jar: Indicates the resource type. For more information, see [Resource](../../../../intl.en-US/Product Introduction/Definition/Resource.md).
-   local\_file: Indicates path of the local file,  and take this file name as the resource name. Resource name is a unique identifier of a resource.
-   table\_name: Indicates table name in MaxCompute. Currently, external tables cannot be added into resource.
-   \[PARTITION  \(spec\)\]: When the resource to be added is a partition table, MaxCompute only supports taking a partition as a resource, not a whole partition table.
-   alias: Specify the resource name. If this parameter is not added, the file name is the resource name by deafault. Jar and Python resources do not support the function.
-   \[comment ‘cmt’\]: Add comment for a resource.
-   \[-f\]: If the same name resource exists, to add this parameter cover the original resource. If this parameter is not specified and the same name resource exists, the operation fails.

**Examples:**

```
odps@ odps_public_dev>add table sale_detail partition (ds='20150602') as sale.res comment 'sale detail on 20150602' -f;
OK: Resource 'sale.res' have been updated.
---Add a resource named sale.res in MaxCompute.
```

**Note:** Currently, each resource file size cannot exceed 500 MB. The resource size referenced by single SQL or MapReduce job cannot exceed 2048 MB. For more information about restrictions, see [MR Restrictions](intl.en-US/User Guide/MapReduce/MR Restrictions.md).

## Delete a resource {#section_nbx_kbf_vdb .section}

**The command format is as follows:**

```
DROP RESOURCE <resource_name>; --resource_name：a specified resource name.
```

## View resource list {#section_t5b_mbf_vdb .section}

**The command format is as follows:**

```
LIST RESOURCES; 
```

**Action: **

View all resources in the current project.

**Examples:**

```
odps@ $project_name>list resources;
Resource Name Comment Last Modified Time Type
1234.txt 2014-02-27 07:07:56 file
mapred.jar 2014-02-27 07:07:57 jar
```

## Download resources {#section_il1_rbf_vdb .section}

**The command format is as follows:**

```
GET RESOURCE <resource_name> <path>;
```

**Action:**

Download resources of the file type to your local device. The resource type must be file, jar, archive, or py.

**Examples:**

```
odps@ $project_name>get resource odps-udf-examples.jar d:\;
OK
```

