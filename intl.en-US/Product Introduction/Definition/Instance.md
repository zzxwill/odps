# Instance {#concept_vpx_s3b_5db .concept}

In MaxCompute, most [tasks](intl.en-US/Product Introduction/Definition/Task.md) are initiated in MaxCompute instances. MaxCompute instances can be in one of two phases:  Running and Terminated.

The status of the running phase is ‘Running’, while the status of the Terminated phase can be ‘Success’, ‘Failed’ or ‘Canceled’. You can query or change the status using the instance ID assigned by MaxCompute. For example:

```
status <instance_id>; --View the status of a certain instance.
            kill <instance_id>; --Stop an instance and set its status as ‘Canceled’.
            wait <instance_id>; --View the running logs of a certain instance.
```

