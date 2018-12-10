# Configure MaxCompute Studio {#concept_nvh_s2y_5db .concept}

After the MaxCompute Studio plug-in is installed, you can find configuration items of MaxCompute Studio on the left bar of  the Settings page of IntelliJ IDEA. For more information about how to open the IntelliJ IDEA configuration page, see [IntelliJ IDEA Documentation](https://www.jetbrains.com/help/idea/2016.3/accessing-settings.html).

## MaxCompute Studio configuration option page {#section_h45_v34_vdb .section}

The MaxCompute Studio configuration option page provides the following configuration items:

1.  **Path for storing the local metadata base**

    Specifies the path for locally storing metadata of a MaxCompute project. On MaxCompute Studio,  the metadata is stored in the hidden directory .odps.studio/meta of the local user directory by default.

2.  **Version update options**

    -   You can use the **Automatically checks for new version** check box to control whether MaxCompute Studio  automatically checks for new version updates.
    -   You can use the **Check new versions** button to manually check new versions. After you click this button, if a new version is available, the **Install new version** button is displayed.  You can click this button to install the new version, and restart IntelliJ IDEA after the installation is complete.

## SDK and Console configuration option page {#section_k45_v34_vdb .section}

The SDK and Console configuration option page provides the following configuration items:

1.  **Path for installing a MaxCompute client**

    Specifies the path for local installation of MaxCompute client. MaxCompute Studio detects the version of the MaxCompute  client installed in the path. If detection fails, an error message is prompted.

    **Note:** MaxCompute Studio later than the 2.6.1 version provides the latest MaxCompute client. You do not need to specify the path.  If you must use a MaxCompute client of a specific version,  you can specify the path.


## MaxCompute SQL configuration option page {#section_m45_v34_vdb .section}

The MaxCompute SQL configuration option page provides the following configuration items:

1.  **Enable syntax coloring**

    Select **Enable syntax coloring** to enable the syntax highlighting feature.

2.  **Enable code completion**

    Select **Enable code completion** to enable the automatic code complementing feature.

3.  **Enable code formatting**

    Select **Enable code formatting** to enable the code formatting feature.

4.  **Compiler options**

    These are global default compiler options. The following options can be separately set for each file on the toolbar of the SQL compiler.

    -   **Compiler Mode**
        -   Statement Mode: In this mode, the compiler compiles and submits a single statement of an SQL file as a unit.
        -   Script Mode: In this mode, the compiler compiles and submits an entire SQL file as a unit. *NOTE: Script Mode enables the compiler and optimizer to optimize the execution plan and improve the overall execution efficiency. This mode is in the test phase now.*
    -   **Type System**
        -   Legacy TypeSystem: Indicates the type system of original MaxCompute.
        -   MaxCompute TypeSystem:  Indicates the new type system introduced by MaxCompute 2.0.
        -   Hive Compatible TypeSystem:  Indicates the type system in Hive compatibility mode introduced by MaxCompute 2.0.
    -   **Compiler Version**
        -   Default Version: Indicates the default version of the compiler.
        -   Flighting Version: Indicates the experimental version of the compiler, which includes new features of the compiler being tested.

## Account configuration option page {#section_s45_v34_vdb .section}

You can add or manage accounts used to access MaxCompute on the Account configuration option page. For more information, see [User authentication](../../../../reseller.en-US/User Guide/Security/User authentication.md).

You must specify an account on MaxCompute Studio to access a MaxCompute project and run or submit jobs.  MaxCompute Studio currently supports the following account type:

-   Alibaba Cloud account \(AccessKey\)

**Add an account**

On the Account configuration option page, follow these steps:

1.  Click **+** or press `Ctrl-N`.
2.  Select the account type **Aliyun Account by AccessKey**.
3.  In the displayed Add Account window, set the following items:
    -   Account Name: Indicates the name of the account on MaxCompute Studio.
    -   Using properties file: Read the AccessKey ID and  AccessKey Secret from the configuration file.
        -   Select the configuration file  `conf/odps_config.ini` after you process [User authentication](../../../../reseller.en-US/User Guide/Security/User authentication.md).
    -   Using properties: Manually enter the AccessKey ID and AccessKey  Secret.
        -   Access Id: Enter the AccessKey ID of your Alibaba Cloud account.
        -   Access Key: Enter the AccessKey Secret of your Alibaba Cloud account.   

            ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/12143/15444389082461_en-US.png)

4.  Click **OK** to complete addition.  Then, the account will be displayed in the Account list  on the Account configuration option page.

**Delete an account**

On the Account configuration option page, follow these steps: \(This operation only deletes the account configuration on Studio configuration, which does not affect your account.\)

1.  Select the account to be deleted in the account list.
2.  Click **-**.
3.  In the displayed dialog box, click **OK**.

**Modify the AccessKey of an account**

On the Account configuration option page, follow these steps:

1.  Select the account to be deleted in the Account list.
2.  Click the pencil icon.
3.  In the displayed Edit Account window, modify the account information. The content is similar to that in the preceding section Add  Account.

View the opening and connection of MaxCompute Region and the settings of Endpoint, see [Endpoints and Data Centers](../../../../reseller.en-US/Prepare/Configure Endpoint.md#).

