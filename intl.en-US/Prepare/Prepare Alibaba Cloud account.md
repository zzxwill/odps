# Prepare Alibaba Cloud account {#task_skw_3pw_5db .task}

Before using the MaxCompute service, you must register an Alibaba Cloud account.

1.   Register an Alibaba Cloud account 

    If you have not yet registered an Alibaba Cloud account, go to the Alibaba Cloud official website and at the upper-right corner click **Free Account**.

    **Note:** The account type you create is an Alibaba Cloud primary account, which is the account type with highest-level permissions. We recommend you keep the access credentials of this account in a secure place. We also recommend you do not allow others to use your account, and that you periodically change your password.

2.   Real name authentication 

    Only an Alibaba Cloud account that has been real name authenticated can purchase and use all products on Alibaba Cloud.  If your account has not successfully passed real name authentication, go to the [Real name authentication](https://account-intl.console.aliyun.com/#/secure) page to authenticate it. **To save you any trouble in the future, be sure to complete the real-name authentication.**

    For enterprise users, the enterprise-level real name authentication process is recommended. For more details, see **membership account & real-name certification**.

3.   Create an AccessKey 

    An AccessKey is primarily used for access permission verification between various Alibaba Cloud products. It is needed to guarantee the processing of tasks in the DataWorks development kit. An AccessKey is comprised of two parts: an Access Key ID and an Access Key Secret.  

    1.   Log in to Ali cloud and click **accesskeys** under the user name in the upper-right corner to Access  Key Management page. 
    2.   Click **Create Access Key** in the upper right corner, and click **Agree and Create** in the pop-up box. project: 
    3.   After the Access Key is successfully created, the system will jump to the Access Key  Key administration page, you can view the status of the corresponding AK and disable it, delete it, and so on.   

        Once an AccessKey is disabled, services that use that AccessKey encounter operational failures and report errors. Therefore, if you change your AccessKeys, you must update the corresponding changes for the products and services that use the disabled AccessKeys.

        **Note:** An AccessKey is critical to the security of your account. Therefore, we recommend you keep Access Key ID and Access Key Secret's security, do not let others know, once there is a risk of leakage, please disable and update in time.


