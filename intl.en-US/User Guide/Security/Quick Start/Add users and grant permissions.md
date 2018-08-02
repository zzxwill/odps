# Add users and grant permissions {#concept_oxp_xrz_vdb .concept}

In the following scenario, Jack is the project administrator of a project called prj1. A new team member named Alice, who already has the Alibaba Cloud account  alice@aliyun.com\), applies to join the prj1project. Alice requests the following permissions: view table lists, submit jobs, and create tables.

The following procedure is performed by Jack, the project administrator:

```
    use prj1;
    add user aliyun$alice@aliyun.com; --Add the user
    grant List, CreateTable, CreateInstance on project prj1 to user aliyun$alice@aliyun.com; --Authorize the user by using the GRANT statement
```

