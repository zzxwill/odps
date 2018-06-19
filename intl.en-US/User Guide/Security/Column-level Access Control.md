# Column-level Access Control {#concept_nq3_zz1_wdb .concept}

Label-based security \(LabelSecurity\) is a required access control \(MAC\) policy at the project space level.  It allows project administrators to control the user access to column-level sensitive data with improved flexibility.

**Differences between MAC and DAC in MaxCompute**

In MaxCompute, MAC is independent of discretionary access control \(DAC\).  Two examples are provided to illustrate the differences between MAC and DAC.

A user who wants to read data in a MaxCompute project must first apply for the SELECT permission, similar to the person who wants to drive in a country must first apply for a driver’s license.  The permission application is within the scope of DAC.

Because the country has a high traffic accident rate, it adds a statute against drunk driving.  All drivers are required to have a driver’s license and consume no alcohol before driving.  In MaxCompute, prohibition against reading of highly sensitive data is analogous to the statute against drunk driving.  The read prohibition is within the scope of MAC.

## Data Sensitivity Classification {#section_axs_j1b_wdb .section}

LabelSecurity assigns security levels to data and the users who access the data. In the government and financial sectors, data sensitivity is usually classified into four levels: 0 \(Unclassified\), 1 \(Confidential\),  2 \(Sensitive\), and 3 \(Highly Sensitive\).  MaxCompute adopts such classification.  Project owners must define standards for data sensitivity classification and access level classification. The default access level of all users is 0, and the default sensitivity level of data is 0.

LabelSecurity supports data sensitivity classification at the column level. Administrators can set sensitivity labels for all the columns of a table. A table may have columns of different sensitivity levels.

Administrators can also set sensitivity labels for views. A view and its base table have independent sensitivity labels. The default sensitivity level of a new view is 0.

## Default Security Policies of LabelSecurity {#section_uqc_l1b_wdb .section}

LabelSecurity applies the following default security policies to the data and users assigned with sensitivity or security labels:

-   No-ReadUp: A user is not allowed to read data with a sensitivity level higher than the user level unless the user is explicitly authorized.
-   Trusted-User: A user is allowed to write data of all sensitivity levels. The default sensitivity level of new data is 0 \(unclassified\).

**Note:** 

-   In some traditional MAC systems, other complex security policies are applied to prohibit unauthorized data distribution in a project. For example, the No-WriteDown policy prohibits users from writing data with a sensitivity level not higher than the user level.  By default, MaxCompute does not support No-WriteDown, considering the costs arising from the management of data sensitivity levels by project administrators.  The effect of No-WriteDown can be attained by modifying the project security settings \(`Set  ObjectCreatorHasGrantPermission=false`\).
-   To prohibit data flowing among different projects, you can set the projects to the protected state \(ProjectProtection\).  With the setting, users can only access the data within their projects. This prevents data transfer beyond the project.

By default, projects disable LabelSecurity. The project owners can enable it as required.

After LabelSecurity is enabled, the default security policies are executed.  When a user accesses a data table, the user must have the SELECT permission and the access level required for sensitive data reading.  Compliance with LabelSecurity is a required but not the sufficient condition for passing CheckPermission.

## LabelSecurity Operations {#section_hyb_bbb_wdb .section}

-   **Enable or Disable LabelSecurity**

    ```
     Set LabelSecurity=true|false; 
       -- Enables or disables LabelSecurity. The default value is false.
       -- LabelSecurity can be enabled or disabled only by the project owner. Other operations can be performed by the project administrator.
    ```

-   **Set Security Labels for Users**

    ```
     SET LABEL <number> TO USER <username>;-- Value range of "number": [0, 9]. This operation can be performed only by the project owner or administrator.
     -Example:
     ADD USER aliyun$yunma@aliyun.com; --Adds a user with the default security label 0.
     ADD USER ram$yunma@aliyun.com:Allen; --Adds user Allen, which is a RAM subaccount of yunma@aliyun.com.
     SET LABEL 3 TO USER aliyun$yunma@aliyun.com; 
       -- Sets the security label of yunma to 3 to allow this user to access only the data with a sensitivity level not higher than 3.
     SET LABEL 1 TO USER ram$yunma@aliyun.com:Allen;  
       -- Sets the security label of subaccount Allen to 1 to allow this user to access only the data with a sensitivity level not higher than 1.
    
    ```

-   **Set Sensitivity Labels for Data**

    ```
     SET LABEL <number> TO TABLE tablename[(column_list)]; -- Value range of "number": [0, 9]. This operation can be performed only by the project owner or administrator.
     -Example:
     SET LABEL 1 TO TABLE t1;  --Sets the sensitivity label of table t1 to 1.
     SET LABEL 2 TO TABLE t1(mobile, addr); --Sets the sensitivity labels of the "mobile" and "addr" columns of table t1 to 2.
     SET LABEL 3 TO TABLE t1;  --Sets the sensitivity label of table t1 to 3.  The sensitivity labels of the "mobile" and "addr" columns are still 2.
    
    ```

    **Note:** The sensitivity labels explicitly set for the columns overwrites the sensitivity label set for the table, without consideration for the label setting order and the sensitivity level.

-   **Explicitly Authorize Lower-level Users to Access Specific Data Tables with a High Sensitivity Level**

    ```
     --Grant permissions:
     GRANT LABEL <number> ON TABLE <tablename>[(column_list)] TO USER <username> [WITH EXP <days>]; --The default validity period is 180 days. 
     -- Revoke the permissions:
     REVOKE LABEL ON TABLE <tablename>[(column_list)] FROM USER <username>;
     -- Clear the expired permissions:
     CLEAR EXPIRED GRANTS;
     -Example:
     GRANT LABEL 2 ON TABLE t1 TO USER ram$yunma@aliyun.com:Allen WITH EXP 1; --Explicitly authorizes Allen to access the data of table t1 with a sensitivity level not higher than 2 for a period of 1 day.
     GRANT LABEL 3 ON TABLE t1(col1, col2) TO USER ram$yunma@aliyun.com:Allen WITH EXP 1; --Explicitly authorizes Allen to access the data in col1 and col2 of table t1 with a sensitivity level not higher than 3 for a period of 1 day.
     REVOKE LABEL ON TABLE t1 FROM USER ram$yunma@aliyun.com:Allen; --Revokes the permission of Allen to access the sensitive data in table t1.
    
    ```

    **Note:** After the label-authorized permission of a user to access a table is revoked, this user’s permission to access the table fields is also revoked.

-   **List the Sensitive Data Sets that a User Can Access**

    ```
     SHOW LABEL [<level>] GRANTS [FOR USER <username>]; 
        --When [FOR USER <username>] is unspecified, the system lists the sensitive data sets that the current user can access.
       --When <level> is unspecified, the system lists the permissions granted by all label levels.When <level> is specified, the system lists only the permissions granted by a specific label level.
    
    ```

-   **List the Users Who Can Access a Specific Table Containing Sensitive Data**

    ```
     SHOW LABEL [<level>] GRANTS ON TABLE <tablename>;
       --Displays the label-authorized permissions on the specified table.
    
    ```

-   **List the Label-authorized Permissions of a User at All Levels to Access a Data Table**

    ```
     SHOW LABEL [<level>] GRANTS ON TABLE <tablename> FOR USER <username>;
       --Displays the label-authorized permissions of the specified user to access the columns of a specific table.
    
    ```

-   **List the Sensitivity Levels of All the Columns of a Table**

    ```
    DESCRIBE <tablename>;
    
    ```

-   **Control the Access Level of a Package Installer Regarding the Sensitive Resources of the Package**

    ```
     ALLOW PROJECT <prjName> TO INSTALL PACKAGE <pkgName> [USING LABEL <number>]; 
       --The package creator grants an access level to the package installer regarding the sensitive resources of the package.
    
    ```

    **Note:** 

    -   When `[USING LABEL  <number>]` is unspecified, the default access level is 0. The package installer can only access non-sensitive data.
    -   When accessing to sensitive data across projects, the access level defined by this command applies to all the users in the project of the package installer.

## LabelSecurity Use Cases {#section_rlb_dcb_wdb .section}

-   **Prohibit All the Users in a Project Except the Project Administrator from Reading Some Sensitive Columns of a Table**

    Scenario description: user\_profile is a table with sensitive data in a project. It has 100 columns, five of which contain sensitive data: id\_card,  credit\_card, mobile, user\_addr, and birthday.  DAC grants all users the SELECT permission on this table. The project owner wants to prohibit all the project users except the project administrator from reading the sensitive columns of the table.

    To achieve this purpose, the project owner can perform the following operations:

    ```
     set LabelSecurity=true; 
       --Enables LabelSecurity.
     set label 2 to table user_profile(mobile, user_addr, birthday); 
       --Sets the sensitivity level of the specified columns to 2.
     set label 3 to table user_profile(id_card, credit_card); 
       --Sets the sensitivity level of the specified columns to 3.
    ```

    **Note:** After the preceding operations, non-administrator users cannot access the data in the five columns.  If a user needs to access the sensitive data for business purposes, the user must be authorized by the project owner or administrator.

    Alice is a member of the project. For business purposes, she wants to apply for access to the data in the mobile column of table user\_profile for a period of one week.  To authorize Alice, the project administrator can perform the following operation:

    ```
    GRANT LABEL 2 ON TABLE user_profile TO USER ALIYUN$alice@aliyun.com WITH EXP 7;
    ```

    **Note:** Mobile, user\_addr, and birthday column contain data with a sensitivity level of 2.  Birthday. After authorization, Alice can access the data in these three columns.  The authorization causes the issue of excessive permission granting.  This issue can be avoided if the project administrator sets the column sensitivity properly.

-   **Prohibit the Project Users with Access to Sensitive Data from Copying and Distributing the Sensitive Data within the Project without Authorization**

    In the preceding use case, Alice is granted the access permission on the data with a sensitivity level of 2 for business purposes.  The project administrator worries that Alice may copy that data from table user\_profile to table user\_profile\_copy created by her and grants Bob the access permission on user\_profile\_copy.  The project administrator needs a method to prohibit such behavior on Alice.

    Considering security usability and management costs, LabelSecurity adopts the default security policy that allows for WriteDown. Users can write data to the columns with a sensitivity level not higher than the user level. MaxCompute cannot address the preceding requirement of the project administrator.  However, the project administrator can restrict the discretionary authorization behavior of Alice by allowing her to only access the data she created, but disallowing her to grant the data access permission to other users.  The procedure is as follows:

    ```
     SET ObjectCreatorHasAccessPermission=true; 
       --Allows the object creator to operate objects.
     SET ObjectCreatorHasGrantPermission=false; 
       --Prohibits the object creator from granting the object access permission to other users.
    ```


