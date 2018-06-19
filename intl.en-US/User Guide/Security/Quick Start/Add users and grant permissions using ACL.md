# Add users and grant permissions using ACL {#concept_kjy_2sz_vdb .concept}

In the following scenario, Jack is the project administrator of a project called prj1.  In the scenario, three new data auditors, Alice, Bob, and  Charlie, are added to the project team.  They need to all apply for the following permissions: view table lists, submit jobs, and read the table userprofile.

In this scenario, the project administrator can perform authorization by using the object-based [ACL Authorization](https://www.alibabacloud.com/help/zh/doc-detail/27935.htm) .

The following procedure is performed by Jack, the project administrator:

```
    use prj1;
    add user aliyun$alice@aliyun.com; --Add the user
    add user aliyun$bob@aliyun.com;
    add user aliyun$charlie@aliyun.com;
    create role tableviewer; --Create a role
    grant List, CreateInstance on project prj1 to role tableviewer; --Grant permissions to the role
    grant Describe, Select on table userprofile to role tableviewer;
    grant tableviewer to aliyun$alice@aliyun.com; --Grant the tableviewer role to the user
    grant tableviewer to aliyun$bob@aliyun.com;
    grant tableviewer to aliyun$charlie@aliyun.com;
```

