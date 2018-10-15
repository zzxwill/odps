# Permission check {#concept_dqq_xf1_wdb .concept}

MaxCompute provides the ability to view multiple permissions, including the permissions of certain users or roles, and authorization lists of specified objects.

MaxCompute uses the markup characters A, C, D, and G when showing the permissions of users or roles. The meanings of these markup characters are as follows:

-   A: Access allowed.
-   D: Access denied.
-   C: Access granted with conditions. It appears only in a policy authorization system.
-   G: Access granted with conditions. Permission can be granted to objects.

An example of viewing permissions is as follows:

```
    odps@test_project> show grants for aliyun$odpstest1@aliyun.com;
    [roles]
    dev
    Authorization Type: ACL
    [role/dev]
    A projects/test_project/tables/t1: Select
    [user/odpstest1@aliyun.com]
    A projects/test_project: CreateTable | CreateInstance | CreateFunction | List
    A projects/test_project/tables/t1: Describe | Select
    Authorization Type: Policy
    [role/dev]
    AC projects/test_project/tables/test_*: Describe
    DC projects/test_project/tables/alifinance_*: Select
    [user/odpstest1@aliyun.com]
    A projects/test_project: Create* | List
    AC projects/test_project/tables/alipay_*: Describe | Select
    Authorization Type: ObjectCreator
    AG projects/test_project/tables/t6: All
    AG projects/test_project/tables/t7: All
```

**Note:** Currently, desc role only displays ACL information of project and table authorization types, while ACL of other objects \(function, resource, instance, job\) does not support display.

## View permissions of a specified user {#section_rhj_fg1_wdb .section}

```
    show grants; --View permissions of the current user.
    show grants for <username>; --View access permissions of a specified user. The operation can be executed by project owners and administrators.

```

Example:

To view the user Alibaba Cloud account bob@aliyun.com permissions in the current project, run the following command on the client:

```
show grants for ALIYUN$bob@aliyun.com;
```

To view RAM sub-account permissions:

```
show grants for RAM$account:sub-account;
```

Example:

```
show grants for RAM$bob@aliyun.com:Alice;
```

## View permissions of a specified role: {#section_ewd_kg1_wdb .section}

```
describe role --View access permissions granted to a specified role
```

**Note:** In the public cloud environment, description role currently only displays ACL information of the object authorization type of project and table, while ACL information of other objects \(such as function, resource, instance, job\) is not displayed.

## View the authorization list of a specified object: {#section_zhs_lg1_wdb .section}

```
show acl for [on type ];--View the user and role authorization list of a specified object
```

**Note:** When \[on type <objectType\>\] is excluded, the default type is Table.

