# Task {#concept_hpg_f3b_5db .concept}

A task is the basic computing unit of MaxCompute. Computing tasks such as those involving SQL, DML and MapReduce functions are completed using tasks.

For most user-submitted tasks, such as [SQL DML statement](../../../../reseller.en-US/User Guide/SQL/Insert Operation/INSERT OVERWRITE__INTO.md#), [MapReduce](../../../../reseller.en-US/User Guide/MapReduce/Summary/MapReduce.md), etc.MaxCompute first analyzes them and then generates a task execution plan. The execution plan is composed of multiple execution stages that are dependent on each other. An execution plan consists of multiple stages with dependency links.

Currently, an execution plan can be logically viewed as a directed graph whose vertices represent the stages and whose edges represent the dependency links of the stages.  MaxCompute executes each stage according to the dependencies in the graph \(execution plan\). A single stage comprises multiple threads, also known as workers. These workers complete the computing in this stage. Different workers in the same stage have exactly the same execution logic, but they process different data. Computational tasks are executed directly in MaxCompute instances, for example, [Status  Instance](../../../../reseller.en-US/User Guide/Common commands/Instances.md) and [Kill Instance](../../../../reseller.en-US/User Guide/Common commands/Instances.md).

For MaxCompute tasks that are not computational tasks, such as DDL statement in SQL, these tasks can only read and modify the metadata information in MaxCompute.  This means that no execution plan can be analyzed and generated from the task.

**Note:** Not all the requests are converted into tasks in MaxCompute, for example, the operations of [Project](reseller.en-US/User Guide/Definition/Project.md), [Resource](reseller.en-US/User Guide/Definition/Resource.md), [UDF](reseller.en-US/User Guide/Definition/Function.md) and [Instance](reseller.en-US/User Guide/Definition/Instance.md) can be completed without MaxCompute tasks.

