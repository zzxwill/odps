# User Authentication {#concept_zql_zj4_vdb .concept}

MaxCompute supports the  **Alibaba Cloud account system**  and the **RAM account system** .

**Note:** MaxCompute can recognize the RAM account system  but cannot recognize the RAM permission system.  Users can add any of their RAM sub-accounts to a project of MaxCompute. However, MaxCompute skips the RAM  permission definitions when it verifies the permissions of the RAM sub-account.

By default, the MaxCompute project only recognizes the Alibaba Cloud account system. You can view the account system supported by this project by running `list  accountproviders;`.

Typically, only Alibaba Cloud accounts are displayed. To add the RAM account system, run the `add accountprovider  ram;`command.  After the RAM account system is added, you can run `list  accountproviders;` to make sure it has been successfully added to the supported account systems.

## Apply for an Alibaba Cloud Account {#section_wzg_5sz_vdb .section}

If you do not have an [Alibaba Cloud account](http://account.aliyun.com/), visit here to apply for one.

**Note:** You need a valid email address when you apply for an Alibaba Cloud account. The email address is then used as the account name after registration.  For example, Alice can use her email address alice@aliyun.com to  register an Alibaba Cloud account. Her account name is alice@aliyun.com after Alibaba Cloud account registration.

## Apply for AccessKey {#section_c1r_ysz_vdb .section}

Click here to create or manage your  [AccessKey](http://i.aliyun.com/access_key) list after you register an Alibaba Cloud account.

An AccessKey consists of the AccessKeyID and AccessKeySecret.  The AccessKeyID is used to retrieve the AccessKey,  and the AccessKeySecret is used to sign the computing messages. You must secure your AccessKey from disclosure.  When an AccessKey needs updated, you can create a new AccessKey and disable the existing  AccessKey.

## Log on to MaxCompute with an Alibaba Cloud Account {#section_eyr_zsz_vdb .section}

You must configure the AccessKey in the configuration file conf/odps\_config.ini before you use odpscmd to log on.  The following is an example:

```
 project_name=myproject
  access_id=<Input the AccessKeyID here, excluding the angle brackets>
 access_key=<Input the AccessKey here, excluding the angle brackets>
 end_point=http://service.odps.aliyun-inc.com/api
```

**Note:** To enable or disable an AccessKey on the Alibaba Cloud website, it takes 15 minutes after the operation is completed.

