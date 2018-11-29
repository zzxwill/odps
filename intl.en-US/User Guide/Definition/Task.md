# Task {#concept_hpg_f3b_5db .concept}

A task is a basic computing unit of MaxCompute. Computing tasks such as SQL DML and MapReduce functions are completed by task.

For most user-submitted tasks, MaxCompute first analyzes them and then generates a task execution plan. The execution plan is composed of multiple execution stages that are dependent on each other. An execution plan consists of multiple stages with dependency links.

Currently, an execution plan can be logically viewed as a directed graph whose vertices represent the stages and whose edges represent the dependency links of the stages. MaxCompute executes each stage according to the dependency links in the graph \(execution plan\). One stage contains multiple processes, also known as workers, that work together and complete the computation of this stage. Different workers in the same stage have completely the same execution logic. They process different data. Computational tasks are executed directly in MaxCompute instances, for example, [Status  Instance](../../../../intl.en-US/User Guide/Common commands/Instance.md) and [Kill Instance](../../../../intl.en-US/User Guide/Common commands/Instance.md).

For MaxCompute tasks that are not computational tasks, such as DDL statement in SQL, these tasks can only read and modify the metadata information in MaxCompute.  This means that no execution plan can be analyzed and generated from the task.

**Note:** Not all the requests are converted into tasks in MaxCompute, for example, the operations of [Project](intl.en-US/Product Introduction/Definition/Project.md), [Resource](intl.en-US/Product Introduction/Definition/Resources.md), [UDF](intl.en-US/Product Introduction/Definition/Function.md) and [Instance](intl.en-US/Product Introduction/Definition/Instance.md) can be completed without MaxCompute tasks.

