# Role Management {#concept_r4v_qzz_vdb .concept}

A role is a defined set of access permissions. It can be used to assign the same set of permissions to a group of users. Role-based authorization can greatly simplify the authorization process and reduce the authorization management cost. Role-based authorization can be used with priority when user authorization is performed. Role-based authorization can greatly simplify the authorization procedure and reduce authorization management costs. When a user must be authorized, the owner should consider whether it would be better to use a role to authorize them.

When a project is created, an admin role is automatically created with a defined set of privileges authorized to the role. These privileges include access to all objects within the project, management, and authorization of users and roles.  In comparison to a project  In comparison to a project owner, the admin role cannot assign admin permission to any user, set the project security configuration, or change the authentication model for the project. Permissions of the admin role cannot be modified.

Role management commands are as follows:

```
    create role <rolename> --Create a role
    drop role <rolename> --Delete a role
    grant <rolename> to <username> --Grant a role to a user
    revoke <rolename> from <username> --Revoke a role from a user

```

**Note:** 

-   One role can be assigned to multiple users at the same time, and one user can also belong to multiple roles.
-   For more information about the mapping between the roles in DataWorks and in MaxCompute, and the platform permissions of these roles, see  the project member management module in [Project Management](https://www.alibabacloud.com/help/zh/doc-detail/47736.htm).

## Create a Role {#section_ntz_y11_wdb .section}

Use the following command format to create a role:

```
CREATE ROLE;
```

For example, 

assume the role player must be created. Enter the following command on the client:

```
create role player;
```

## Add a User to Role {#section_ytl_bb1_wdb .section}

To add a user to the role, use the following command format to add a user to the role:

```
GRANT <roleName> TO <full_username> ;
```

For example, 

assume the user bob@aliyun.com must be added to the player  role, enter the following command on the console:

```
grant player to bob@aliyun.com;
```

## Authorize Role {#section_cvd_hb1_wdb .section}

The authorization statement for the role is similar to the authorization for the user. For more information, see [User authorization](intl.en-US/User Guide/Security/Authorization.md).

**Note:** After role authorization is complete, all users under this role have the same permissions.

For example, 

suppose Jack is the administrator of project prj1. Three new data auditors, Alice, Bob, and Charlie, are added to the project team.  They must apply for the following permissions: view  the table lists, submit the jobs, and read the table userprofile.

In this scenario, the project administrator can perform authorization by using the object-based [ACL Authorization](intl.en-US/User Guide/Security/Authorization.md).

The procedure is as follows:

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

## Revoke the Role from a User {#section_akv_mb1_wdb .section}

To revoke the role from a user, use the following command format to revoke the role from a user:

```
REVOKE <roleName> FROM <full_username>;
```

For example, 

assume the user bob@aliyun.com must be removed from the player  role. Enter the following command on the client:

```
revoke player from bob@aliyun.com;
```

## Delete a Role {#section_ij1_qb1_wdb .section}

To delete a role, use the following command format to delete a role:

```
DROP ROLE <roleName>;
```

For example, 

assume the role player must be deleted:

```
drop role player;
```

**Note:** When you delete a role, MaxCompute  checks whether other users are in this role. If yes, this role cannot be deleted. The role can be successfully deleted only when all users in the role are revoked from this role. If there are such users, this role cannot be removed. Removing a role succeeds only if all of its users are already revoked from it.

