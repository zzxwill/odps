# User management {#concept_kvw_stz_vdb .concept}

Any user, except the project owner, must be added to the MaxCompute project and granted the corresponding permissions to manage data, jobs, resources, and functions in MaxCompute.  This article describes how a project owner can add, authorize, and remove other users, including RAM sub-accounts to MaxCompute.

If you are a project owner, we recommend that you read this article carefully. If you are a typical user, we recommend that you submit an application to the project owner to be added to the corresponding project. We recommend all users to read the subsequent sections.

All the operations mentioned in this article are executed on the console. For Linux, run ./bin/odpscmd and for Windows, run ./bin/odpscmd.bat.

## Add a user {#section_qg5_xtz_vdb .section}

In this example, the project owner, Alice, wants to authorize another user, therefore she must add the user to the project first. **Only a user who has been added to the project can be authorized**.

The command to add a user is as follows:

```
add user
```

The <username\> of an Alibaba Cloud account is a valid email address registered with Alibaba Cloud, or a RAM sub-account of an Alibaba Cloud account that runs the command.  For example:

```
add user ALIYUN$odps_test_user@aliyun.com;
add user RAM$ram_test_user;
```

Assume that the Alibaba Cloud account of Alice is alice@aliyun.com. When Alice runs these statements, the following results are returned by running the `list users;` command:

```
RAM$alice@aliyun.com:ram_test_user
ALIYUN$odps_test_user@aliyun.com
```

This indicates that the Alibaba Cloud account odps\_test\_user@aliyun.com and the sub-account ram\_test\_user created by Alice using RAM have been added to the project.

## Add a RAM sub-account {#section_udf_j5z_vdb .section}

The two ways to add a RAM sub-account are as follows:

-   By using DataWorks, for more information, see [Prepare a RAM account](https://www.alibabacloud.com/help/doc-detail/30264.htm).
-   By using MaxCompute client commands as described in this document.

    **Note:** 

    -   MaxCompute only allows a primary account to add its own RAM sub-accounts to a project. RAM sub-accounts of other Alibaba Cloud accounts are not allowed. Therefore, you can skip to specify the name of the primary account before the RAM sub-accounts when `add user`.  MaxCompute determines by default that the account which runs the command is the corresponding sub-account.
    -   MaxCompute only recognizes the RAM account system and does not recognize the RAM permission system. Users can add any of their RAM sub-accounts to a MaxCompute project, but MaxCompute does not consider the permission limits in RAM when performing permission verification of RAM sub-accounts.
    By default, MaxCompute project only recognizes Alibaba Cloud account systems. To view the supported account systems use the `list accountproviders;` command. Typically, only the ALIYUN account is visible, for example:

    ```
    odps@ ****>list accountproviders;
    ALIYUN
    ```

    **Note:** Only the project owner has the permission to perform operations related to `accountproviders`.

    As shown in the preceding command, you can only see the `ALIYUN` account system. If you want to add RAM accounts support, run the `add accountprovider ram; as follows: ram;` as follows:

    ```
    odps@ odps_pd_inter>add accountprovider ram;
    OK
    ```

    The user will still not be able to operate MaxCompute successfully. This is because, the user must be granted certain permissions to operate MaxCompute within the permissive limits. For more information, see  [Authorization](reseller.en-US/User Guide/Security/Authorization.md).


## User Authorization {#section_ncq_jvz_vdb .section}

Once the user is added, the project owner or project administrator must authorize the user. The user can perform the operations only after obtaining the permissions.

MaxCompute provides ACL authorization, cross-project resource sharing, and project resource protection. The following are two common scenarios, for more information, see  [ACL Authorization](reseller.en-US/User Guide/Security/Authorization.md).

**Scenario 1**

In the following scenario, Jack is the administrator of the project prj1. A new project team member Alice \(Alibaba Cloud account: alice@aliyun.com\) applies to join the project prj1, and for permission to view table lists, submit jobs, and create tables.

The admin or the project owner can run the following command on the client:

```
use prj1; --Open the project prj1  
add user aliyun$alice@aliyun.com; --Add the user  
grant List, CreateTable, CreateInstance on project prj1 to user aliyun$alice@aliyun.com; --Authorize the user
```

**Scenario 2**

In the following scenario, assume Alibaba Cloud account user \(bob@aliyun.com\) has been added to a project \($user\_project\_name\), and must be granted permission to create tables, obtain table information, and run functions.

The admin or the project owner can run the following command on the client:

```
grant CreateTable on PROJECT $user_project_name to USER ALIYUN$bob@aliyun.com;
  --Grant CreateTable permission on project “$user_project_name” to bob@aliyun.com
grant Describe on Table $user_table_name to USER ALIYUN$bob@aliyun.com;
  --Grant Describe permission on table “$user_table_name” to bob@aliyun.com
grant Execute on Function $user_function_name to USER ALIYUN$bob@aliyun.com;
  --Grant Run permission on function "$user_function_name" to bob@aliyun.com
```

## Authorize RAM Sub-account {#section_l3q_tvz_vdb .section}

To check accounts support, run `list  accountproviders;` command as follows:

```
odps@ ****>list accountproviders;
ALIYUN, RAM
```

In this project, RAM accounts are also supported. You can add a RAM sub-account to this project and grant `Describe` permission on the tables. For example:

```
odps@ ****>add user ram$bob@aliyun.com:Alice;
OK: DisplayName=RAM$bob@aliyun.com:Alice
odps@ ****>grant Describe on table src to user ram$bob@aliyun.com:Alice;
OK
```

After running these commands, Alice account, which is a RAM sub-account of bob@aliyun.com , can logon to MaxCompute with the **AccessKeyID** and **AccessKeySecret**, and run `desc` on the table src.

**Note:** 

-   For more information about how to create a RAM sub-account `AccessKeyID` and `AccessKeySecret`,  see [RCreate a RAM user](https://www.alibabacloud.com/help/doc-detail/28637.htm).
-   For more information about how to add or remove users on MaxCompute, see the corresponding content of this article.
-   For more information about authorizing a user, see  [Authorization](reseller.en-US/User Guide/Security/Authorization.md).

## Remove a User {#section_itn_ywz_vdb .section}

When a user leaves the project team, Alice must remove the user from the project. Once removed from the project, the user no longer has any access permission to the project resources. 

The command to remove a user from a project is as follows:

```
remove user 
```

**Note:** 

-   A user removed from a project immediately loses an authority to access resources of the project.
-   Revoke all the roles of the user, before removing a user whom the roles are assigned. For more information about roles, see  [Role Management](reseller.en-US/User Guide/Security/Role management.md).
-   After a user is removed, all [ACL Authorization](reseller.en-US/User Guide/Security/Authorization.md) data related to the user is retained. After a user is added to a project again, the ACL Authorization of this user is enabled again.
-   MaxCompute does not support complete removal of a user and all permission data from a project.

To remove corresponding users, Alice can run the following commands:

```
remove user ALIYUN$odps_test_user@aliyun.com;
remove user RAM$ram_test_user;
```

To make sure the users are removed, run the following command:

```
LIST USERS;
```

If those two accounts are no longer listed after running the command, it indicates that the accounts have been removed from the project.

## Remove a RAM Sub-account {#section_f4c_qxz_vdb .section}

Similarly, RAM  sub-account can be removed by using the `remove user` command. For example:

```
odps@ ****>revoke describe on table src from user ram$bob@aliyun.com:Alice;
OK
-- Revoke Alice sub-account permissions
odps@ ****>remove user ram$bob@aliyun.com:Alice;
Confirm to "remove user ram$bob@aliyun.com:Alice;" (yes/no)? yes
OK
-- Remove sub-account
```

If you are the project owner, you can also remove the RAM account system from the current project by `remove accountprovider` as follows:

```
odps@ ****>remove accountprovider ram;
Confirm to "remove accountprovider ram;" (yes/no)? yes
OK
odps@ ****>list accountproviders;
ALIYUN
```

