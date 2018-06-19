# Project data protection {#concept_yyj_jsz_vdb .concept}

In the following scenario, Jack is the project administrator of a project called prj1. The project involves a large volume of sensitive data including user IDs, shopping records, and data mining algorithms with proprietary intellectual property rights. Jack wants to properly protect the sensitive data and algorithms and restrict project users to only have access to data within the project. Jack wants to make sure that data can only flow within the project. Many data mining algorithms with proprietary intellectual property right are included in the project. Jack hopes that these sensitive data and algorithms can be properly protected and only accessible to users within the project, and that data flow in the project only, and not out of the project.

To protect the project data, Jack must follow these steps:

```
    use prj1;
    set ProjectProtection=true; --Enable the project data protection mechanism
```

Once the project data protection is enabled, data within the project cannot be transferred out of the project. All data flows only within the project.

There may be scenarios where users want to export data tables out of the project under the approval of the project administrator.  In such cases, MaxCompute provides the TrustedProject configuration to support external data export from the protected project.  In this type of scenario, to configure project prj2 as a trusted project of prj1 and to enable data flow from prj1 to prj2, perform the following command:

```
    use prj1;
    add trustedproject prj2;
```

