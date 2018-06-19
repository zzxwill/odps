# Security Configuration {#concept_c4m_qg1_wdb .concept}

MaxCompute is a multi-tenant data processing platform. Distinct tenants have distinct data security requirements.  Therefore, MaxCompute provides project-level security configurations to comply with the unique requirements of individual tenants.  Project owners can customize their external account support and authentication models.

MaxCompute provides multiple methods of orthogonal authorization, including access control list \(ACL\)  authorization and implicit authorization. Note: An object creator is automatically granted the object access permission.  Not all users need these security features. Users can properly configure the project authentication model based on their service security requirements and usage patterns.

```
    show SecurityConfiguration
      --View the project security configuration.
    set CheckPermissionUsingACL=true/false
      --Enable/Disable the ACL authorization mechanism. The default value is true.
    set ObjectCreatorHasAccessPermission=true/false
      --Enable/Disable automatic access permission granting to object creators. The default value is true.
    set ObjectCreatorHasGrantPermission=true/false
      --Enable/Disable automatic authorization permission granting to object creators. The default value is true.
    set ProjectProtection=true/false  
        --Enable/Disable project data protection to enable/disable data transfer from the project.
```

**Note:** 

You can also complete the security configuration of a project in a visualized technique using DataWorks. For more information, see  [Project Management](https://www.alibabacloud.com/help/zh/doc-detail/53040.htm).

