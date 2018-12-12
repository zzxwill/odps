# Role management {#concept_r4v_qzz_vdb .concept}

A role is a defined set of access permissions. It assigns the same set of permissions to a group of users. Role-based authorization greatly simplifies the authorization process and reduces the authorization management cost. It must be used with priority.

When a project is created, an admin role is automatically created with a definite privilege authorized to the role, including access to all objects within the project, management of users and roles, and authorization to users and roles. In comparison to a project owner, the admin role cannot assign admin permission to any user, set the project security configuration, or change the authentication model for the project. Permissions of the admin role cannot be modified.

Role management related commands include the following:

```
    create role <rolename> --Create a role
    drop role <rolename> --Delete a role
    grant <rolename> to <username> --Grant a role to a user
    revoke <rolename> from <username> --Revoke a role from a user

```

**Note:** 

-   One role can be assigned to multiple users at the same time, and one user can be assigned multiple roles.
-   For more information about the mapping between the roles in DataWorks and in MaxCompute, and the platform permissions of these roles, see the project member management module in [Project Management](../../../../reseller.en-US/User Guide/Project management/Project configuration.md#).

## Create a role {#section_ntz_y11_wdb .section}

To create a role, use the following command :

```
CREATE ROLE;
```

Example:

To create a role player, enter the following command on the client:

```
create role player;
```

**Note:** The role permissions you create can view the specified user permissions through [Permission check](reseller.en-US/User Guide/Security/Permission check.md#section_rhj_fg1_wdb).

## Add a user to the role {#section_ytl_bb1_wdb .section}

To add a user to the role, use the following command:

```
GRANT <roleName> TO <full_username> ;
```

Example:

To assign user bob@aliyun.com the player role, enter the following command on the console:

```
grant player to bob@aliyun.com;
```

## Authorize role {#section_cvd_hb1_wdb .section}

The authorization statement for the role is similar to the authorization for the user. For more information, see [User authorization](reseller.en-US/User Guide/Security/Authorization.md#).

**Note:** After role authorization is complete, all users under this role have the same permissions.

Example:

Jack is the administrator of project prj1. Three new data auditors, Alice, Bob, and Charlie, are added to the project team. They must apply for the following permissions: view the table lists, submit the jobs, and read the table userprofile.

In this scenario, the project administrator can perform authorization by using the object-based [ACL Authorization](reseller.en-US/User Guide/Security/Authorization.md#).

The commands are as follows:

```
    use prj1;
    add user aliyun$alice@aliyun.com; --Add the user
    add user aliyun$alice@aliyun.com; --Add the user
    add user aliyun$charlie@aliyun.com;
    create role tableviewer; --Create a role
    grant List, CreateInstance on project prj1 to role tableviewer; --Grant permissions to the role
    grant Describe, Select on table userprofile to role tableviewer;
    grant tableviewer to aliyun$alice@aliyun.com; --Grant the tableviewer role to the user
    grant tableviewer to aliyun$bob@aliyun.com;
    grant tableviewer to aliyun$charlie@aliyun.com;
```

## Revoke the role from the user {#section_akv_mb1_wdb .section}

To revoke the role from the user, use the following command:

```
REVOKE <roleName> FROM <full_username>;
```

Example:

To remove the user bob@aliyun.com from the player role, use the following command on the client:

```
revoke player from bob@aliyun.com;
```

## Delete a Role {#section_ij1_qb1_wdb .section}

To delete a role, use the following command:

```
DROP ROLE <roleName>;
```

Example:

To delete the role of the player, use the following command:

```
drop role player;
```

**Note:** When a role is deleted a role, MaxCompute checks whether other users are in this role. If yes, this role cannot be deleted. The role can be successfully deleted only when all users in the role are revoked from this role.

