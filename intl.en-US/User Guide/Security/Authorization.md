# Authorization {#concept_ubm_4yz_vdb .concept}

Authorization allows a user to perform operations including read, write, and view on tables, tasks, resources, and other objects of the MaxCompute. After the [user](reseller.en-US/User Guide/Security/User management.md#) is added, the project owner or the project administrator must authorize the user. The user can perform operations only after obtaining the permission. 

MaxCompute provides Access Control List \(ACL\)  authorization, cross-project resource sharing, and project resource protection. Authorization typically includes three elements: subject, object, and action. In MaxCompute, the subject refers to a user or a role and the object refers to various types of objects in a project.

ACL authorization includes following MaxCompute objects: [Project](reseller.en-US/User Guide/Definition/Project.md#),[Table](reseller.en-US/User Guide/Definition/Table.md#) ,[Function](reseller.en-US/User Guide/Definition/Function.md#) , [Resource](../../../../reseller.en-US/User Guide/Definition/Resource.md), and [Instance](reseller.en-US/User Guide/Definition/Instance.md#). Operations are related to specific object types, therefore different types of objects support different types of actions.

MaxCompute projects support the following object types and actions:

|Object|Action|Description|
|:-----|:-----|:----------|
|Project|Read|View project information \(excluding any project objects\), such as the creation time.|
|Project|Write|Update project information \(excluding any project objects\), such as comments.|
|Project|List|View the list of all types of objects in the project.|
|Project|CreateTable|Create a table in the project.|
|Project|CreateInstance|Create an instance in the project.|
|Project|CreateFunction|Create a function in the project.|
|Project|CreateResource|Create a resource in the project.|
|Project|All|Grant all of the preceding permissions.|
|Table|Describe|Read the metadata of the table.|
|Table|Select|Read the table data.|
|Table|Alter|Change the metadata of the table and add or delete a partition.|
|Table|Update|Overwrite or add table data.|
|Table|Drop|Delete a table.|
|Table|All|Grant all the preceding permissions.|
|Function|Read|Read and run permissions.|
|Function|Write|Update.|
|Function|Delete|Delete.|
|Function|Run|Run.|
|Function|All|Grant all the preceding permissions.|
|Resource|Read|Read.|
|Resource|Write|Update.|
|Resource|Delete|Delete.|
|Resource|All|Grant all the preceding permissions.|
|Instance|Read|Read.|
|Instance|Write|Update.|
|Instance|All|Grant all the preceding permissions.|

**Note:** 

-   The CreateTable action for the objects of Project type must work with the CreateInstance permission for the Project object. The Select, Alter, Update, and Drop actions for the objects of Table type must work with the CreateInstance permission for the Project object.
-   If the CreateInstance permission is not granted, the corresponding operations cannot be performed even though the mentioned permissions are granted. This is related to the internal implementation of MaxCompute. The Select permission for Table type objects must work with the CreateInstance permission. While performing cross-project operation, such as selecting the table of project B in the project A, you must have the project A CreateInstance and the project B Table select permissions.
-   After a user or role is added, you must grant permissions to the user or role. MaxCompute authorization is an object-based authorization method. The permission data authorized by ACL is considered as a type of sub-resource of the object. Authorization can be performed only if the object exists. When the object is deleted, the authorized permission data is automatically deleted.

-   **SQL92 Authorization**

    MaxCompute supports authorization using the syntax similar to the GRANT and REVOKE commands defined by SQL92. It grants or revokes permissions to/from the existing project object through simple authorization statements. The authorization syntax is as follows:

    ```
        grant actions on object to subject
        revoke actions on object from subject
        actions ::= action_item1, action_item2, ...
        object ::= project project_name | table schema_name |
                   instance inst_name | function func_name |
                    resource res_name
        subject ::= user full_username | role role_name
    ```

    Users familiar with GRANT and REVOKE commands defined by SQL92 or with Oracle database security management can identify that the ACL authorization syntax of MaxCompute does not support \[WITH GRANT OPTION\] authorization parameters. For example, when User A authorizes User B to access an object, User B cannot grant the permission to User C. In this scenario, all permissions can be granted by one of the following three roles:

    -   Project owner
    -   Project administrator
    -   Object creator
-   **Use example of ACL authorization**

    In the following scenario, the Alibaba Cloud account user alice@aliyun.com is a newly added member to the project test\_project\_a,  and Allen is a RAM-sub account added to bob@aliyun.com. In test\_project\_a,  they both must submit jobs, create tables, and view existing objects in the project.

    The project administrator performs the following authorization operations:

    ```
        use test_project; --Open the project
        add user aliyun$alice@aliyun.com; --Add the user
        add user aliyun$alice@aliyun.com; --Add the user
        create role worker; --Create a role
        grant worker TO aliyun$alice@aliyun.com; --Grant the role
        grant worker TO aliyun$bob@aliyun.com; --Grant the role
        grant CreateInstance, CreateResource, CreateFunction, CreateTable, List ON PROJECT test_project_a TO ROLE worker; --Authorize the role
    ```

-   **Cross-project Table/Resource/Function sharing**

    Following the preceding example, aliyun$alice@aliyun.com and ram$bob@aliyun.com:Allen have certain permissions in test\_project\_a.  These two users must query table prj\_b\_test\_table in test\_project\_b,  and use test\_project\_b. UDF  prj\_b\_test\_udf.

    The project administrator performs the following authorization operations for test\_project\_b:

    ```
        use test_project_b; --Open the project
        add user aliyun$alice@aliyun.com; --Add the user
        add user ram$bob@aliyun.com:Allen; --Add th RAM sub-account
        create role prj_a_worker; --Create a role
        grant prj_a_worker TO aliyun$alice@aliyun.com; --Grant the role
        grant prj_a_worker TO ram$bob@aliyun.com:Alice; --Grant the role
        grant Describe , Select ON TABLE prj_b_test_table TO ROLE prj_a_worker; --Authorize the role
        grant Read ON Function prj_b_test_udf TO ROLE prj_a_worker; --Authorize the role
        grant Read ON Resource prj_b_test_udf_resource TO ROLE prj_a_worker; --Authorize the role
    --After authorization, the two users query table and use udf in test_project_a as follows:
    use test_project_a;
    select test_project_b:prj_b_test_udf(arg0, arg1) as res from test_project_b.prj_b_test_table;
    ```


**Note:** If UDF is created in test\_project\_a, then only Resource authorization is required. Use the following code:

```
create function function_name as 'com.aliyun.odps.compiler.udf.PlaybackJsonShrinkUdf' using 'test_project_b/resources/odps-compiler-playback.jar' -f;.
```

