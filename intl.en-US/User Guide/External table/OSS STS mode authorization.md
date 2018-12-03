# OSS STS mode authorization {#concept_ln3_c4v_ydb .concept}

This article introduces you how to customize the permissions of MaxCompute to access OSS in RAM.

The location access OSS account supports the incoming plaintext `AccessKeyId` and `AccessKeySecret` when creating the external table, but there is a risk of leaking the account. In some scenarios, this risk is intolerable, so MaxCompute provides a more secure way to access OSS.

MaxCompute combines Alibaba Cloud's Access Control Service \(RAM\) and Token Service \(STS\) to address account security issues. You can grant permissions in two ways:

-   When the owner of MaxCompute and OSS are the same account, a one-click authorization operation can be performed directly on the RAM console.
-   Custom authorization.
    1.  The first thing you need to authorize MaxCompute to access the OSS permissions in RAM. Create a role, and the role name such as `AliyunODPSDefaultRole` or `AliyunODPSRoleForOtherUser` , and set the policy content:

        ```
        -- When the owner of MaxCompute and OSS are the same account
        
        "Statement ":[
        
        "Action": "STS: apererole ",
        "Effect": "allow ",
        "Principal ":{
        "Service ":[
        "Maid"
        
        }
        
        
        "Version": "1"
        
        -- When the owner of MaxCompute and OSS are not the same account
        
        "Statement ":[
        
        "Action": "STS: apererole ",
        "Effect": "allow ",
        "Principal ":{
        "Service ":[
        "MaxCompute's owner cloud account page"
        
        
        
        
        "Version": "1"
        
        ```

    2.  Grant the role the necessary permissions to access the OSS \* . As follows:

        ```
        
        Version: "1 ",
        "Statement ":[
        
        "Action ":[
         "Oss: listbuckets ",
         "Oss: GetObject ",
         "Oss: maid ",
         "Oss: putobject ",
         "Oss: deleteobject ",
         "Oss: maid ",
         "Oss: listparts"
        
        "Resource ":"*",
        "Effect": "allow"
        
        
        
        -- Can Customize other Permissions
        ```

    3.  The permission box is then granted to the role.

**Note:** After the authorization is complete, view the role details to obtain the Ran information of the Role. You need to specify this Ran information when you create the OSS external table.

