# Security Configuration of a Project {#concept_mjw_2j1_wdb .concept}

## Authentication Configuration {#section_syc_jj1_wdb .section}

|Statements|Description|
|:---------|:----------|
|show SecurityConfiguration|View the security configuration of the project.|
|set CheckPermissionUsingACL=true/false|Enable/Disable the ACL-based authorization.|
|set CheckPermissionUsingPolicy=true/false|Enable/Disable the policy authorization.|
|set ObjectCreatorHasAccessPermission=true/false|Grant/Revoke default access permissions to/from object creators.|
|set ObjectCreatorHasGrantPermission=true/false|Grant/Revoke default authorization permissions to/from object creators.|

## Data Protection {#section_tyy_kj1_wdb .section}

|Statement|Description|
|:--------|:----------|
|set ProjectProtection=false|Disable data protection.|
|list TrustedProjects|View the list of trusted projects.|
|add TrustedProject <projectName\> `<projectName>`|Add a trusted project.|
|remove TrustedProject  `<projectName>`|Remove a trusted project.|

