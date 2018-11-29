# Manage permissions {#concept_wyr_4j1_wdb .concept}

## Manage users {#section_jdf_qj1_wdb .section}

|Statement|Description|
|:--------|:----------|
|list users|View all users added to the project.|
|add user <username\> `<username>`|Add a user.|
|remove user <username\> `<username>`|Remove the user.|

## Manage roles {#section_rfc_sj1_wdb .section}

|Statement|Description|
|:--------|:----------|
|list roles|View all created roles.|
|create role <rolename\> `<rolename>`|Create a role.|
|drop role <rolename\> `<rolename>`|Delete a role.|
|grant `<rolelist>` to `<username>`|Assign one or multiple roles to the user.|
|revoke `<rolelist>` from `<username>`|Revoke a role from the user.|

## ACL Authorization {#section_aft_tj1_wdb .section}

|Statement|Description|
|:--------|:----------|
|grant `<privList>` on `<objType>` `<objName>` to user `<username>`|Authorize a user.|
|grant `<privList>` on `<objType>` `<objName>` to role `<rolename>`|Authorize a role.|
|revoke `<privList>` on `<objType>` `<objName>` from user `<username>`|Revoke user authorization.|
|revoke `<privList>` on `<objType>` `<objName>` from role `<rolename>`|Revoke role authorization.|

## Permission review {#section_xq2_wj1_wdb .section}

|Statement|Description|
|:--------|:----------|
|whoami|View current user information.|
|show grants \[for `<username>`\] \[on type `<objectType>`\]|View user role and permissions.|
|show acl for `<objectName>` \[on type `<objectType>`\]|View specific object authorization information.|
|describe role `<roleName>`|View role authorization information and role assignments.|

