# Outstanding payment warning and suspension policies {#concept_ttw_bvm_xdb .concept}

The outstanding payment warning and suspension policies for MaxCompute are as follows.

|Billing method|Outstanding payment warning and suspension policies|
|:-------------|:--------------------------------------------------|
|Subscription| -   If your instance expires, the corresponding items in the instance are locked for 15 days \(your MaxCompute project data is retained during this time\).
-   If the instance is not renewed in the 15 days after the expiration date, the corresponding resources in the instance are released, and all MaxCompute project data associated with the instance cannot be recovered.

 |
|Pay-As-You-Go| -   If a bill that is due cannot be paid through the bank card associated with the billing account, your instance will enter an overdue payment state. By default, you will receive an expiration reminder.
-   If the bill remains unpaid 15 days after it is due, your MaxCompute services are locked.

 |

**Note:** 

MaxCompute services enter the outstanding payment or suspension state if, during the running of a calculation task, the following error occurs.

```
ODPS-0420095: Access Denied - Authorization Failed [4093], You have NO privilege to do the restricted operation on {acs:odps:*:projects/project_name}. Access Mode is AllDenied.
```

