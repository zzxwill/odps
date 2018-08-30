# User authentication {#concept_zql_zj4_vdb .concept}

MaxCompute supports the **Alibaba Cloud account system** and the **RAM account system**.

**Note:** MaxCompute recognizes the RAM account system but cannot recognize the RAM permission system. As a user, you can add any of your RAM sub-accounts to a MaxCompute project. However, MaxCompute skips the RAM  permission definitions when it verifies the permissions of the RAM sub-account.

By default, the MaxCompute project only recognizes the Alibaba Cloud account system. You can view the account system supported by this project by running `list  accountproviders;`.

Typically, only Alibaba Cloud accounts are displayed. To add the RAM account system, run the `add accountprovider  ram;` command. After the RAM account system is added, run `list  accountproviders;` to make sure it has been successfully added to the supported account systems.

## Apply for an Alibaba Cloud account {#section_wzg_5sz_vdb .section}

If you do not have an [Alibaba Cloud account](http://account.aliyun.com/), visit here to apply for one.

**Note:** A valid email address is needed, when you apply for an Alibaba Cloud account. Because this email address is used as the account name after registration. For example, Alice can use her email address alice@aliyun.com to register an Alibaba Cloud account. Her account name will be alice@aliyun.com after Alibaba Cloud account registration.

## Apply for AccessKey {#section_c1r_ysz_vdb .section}

Click here to create or manage your [AccessKey](http://i.aliyun.com/access_key) list after you register an Alibaba Cloud account.

An AccessKey consists of the AccessKeyID and AccessKeySecret. The AccessKeyID is used to retrieve the AccessKey, and the AccessKeySecret is used to sign the computing messages. You must secure your AccessKey for further use. If you need to update an AccessKey, create a new AccessKey and disable the existing one.

## Log on to MaxCompute with an Alibaba Cloud account {#section_eyr_zsz_vdb .section}

Configure the AccessKey in the configuration file conf/odps\_config.ini before you use odpscmd to log on. See the following example:

```
 project_name=myproject
  access_id=<Input the AccessKeyID here, excluding the angle brackets>
 access_key=<Input the AccessKey here, excluding the angle brackets>
 end_point=http://service.odps.aliyun-inc.com/api
```

**Note:** To enable or disable an AccessKey on the Alibaba Cloud website, wait for at least 15 minutes after the operation is complete.

