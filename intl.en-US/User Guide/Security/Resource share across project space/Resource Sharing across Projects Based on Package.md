# Resource Sharing across Projects Based on Package {#concept_oq1_qk1_wdb .concept}

Assume that you are the owner or administrator \(admin role\) of some projects. One of your primary accounts has multiple projects, wherein the prj1 project has some resources \(including tables, resources, and custom functions\) that can be shared with other projects. However, adding users of other projects to prj1 and granting permissions to them one by one is complicated, and adding of users who are irrelevant but are added to the prj1 project \(if they exist\) complicates the project management.

This section describes cross-project resource sharing.

If resources need to be controlled by a user in fine-grained manner, and the user who applies for the control permission is a member of the business project team, we recommend that you use the [Project user and authorization management](intl.en-US/User Guide/Security/Authorization.md)  feature.

Package is used for sharing data and resources across projects. It solves the problem of cross-project user authorization.

Without Package the following problem cannot be effectively solved.

If members of the Alifinance project want to access data in the Alipay project, the administrator of the Alipay project must perform tedious authentication operations on them: first, add users in the Alifinance project to the Alipay project, and then perform general authentications on the newly added users, respectively.

Actually, the administrator of the Alipay project does not want to authenticate and manage all users in the Alifiance project. Instead, the administrator expects more efficient feature for autonomous authentication controls over permissive objects.

After Package is used, the administrator of the Alipay project can perform packaging authorization on the objects to be used by the Alifinance project \(that is, create a Package\), and then permit the Alifinance project to install the Package.  After the Alifinance project’s administrator installs the Package, the administrator can determine whether to grant permissions of the Package to the users of the Alifinance project as required.

