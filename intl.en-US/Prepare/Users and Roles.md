# Users and Roles {#concept_v1c_zzx_5db .concept}

[Projects](../../../../intl.en-US/Product Introduction/Definition/Projects.md) are the basic unit of multi-tenant architecture, data management, computing, and are also a subject of billing in MaxCompute. Once user becomes a project owner, all objects \(tables, instances, resources, UDFs\) in this project belongs to the user. If project owner does not grant permissions to others, no one else has permission to access this project.

**Note:** If the RAM sub-account has been bound to the MaxCompute or DateWorks project, unbind the project before deleting the sub-account. Otherwise, the sub-account remains in the project.

-   If you want to add and authorize users using DataWorks, see [Add Users and Roles](https://www.alibabacloud.com/help/doc-detail/58185.html).
-   If you use the MaxCompute security management commands for [user management](../../../../intl.en-US/User Guide/Security/User Management.md), see User Management for how to create, authorize, and delete users \(including RAM sub-accounts\).
-   If you use the MaxCompute security management commands for role management, see [Role Management](../../../../intl.en-US/User Guide/Security/Role Management.md) for how to create, authorize, and delete roles.
-   For more information about authorization and permission check, see [Authorization](../../../../intl.en-US/User Guide/Security/Authorization.md) and [Permission Check](../../../../intl.en-US/User Guide/Security/Permission Check.md).

