# Permission Check {#concept_dqq_xf1_wdb .concept}

MaxCompute provide the ability to view multiple permissions, including the permissions of certain users or roles, and authorization lists of specified objects.

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

## View the Permissions of a Specified User {#section_rhj_fg1_wdb .section}

```
    show grants; --View permissions of the current user.
    show grants for <username>; --View access permissions of a specified user. The operation can be executed by project owners and administrators.

```

For example:

to view the user Alibaba Cloud account bob@aliyun.com permissions  in the current project, run the following command on the client:

```
show grants for ALIYUN$bob@aliyun.com;
```

View RAM sub-account permissions:

```
show grants for RAM$account:sub-account;
```

For example:

```
show grants for RAM$bob@aliyun.com:Alice;
```

## View the Permissions of a Specified Role {#section_ewd_kg1_wdb .section}

```
describe role --View access permissions granted to a specified role
```

## View the Authorization List of a Specified Object {#section_zhs_lg1_wdb .section}

```
show acl for [on type ];--View the user and role authorization list of a specified object
```

**Note:** When \[on type <objectType\>\] is omitted, the default type is  Table.

