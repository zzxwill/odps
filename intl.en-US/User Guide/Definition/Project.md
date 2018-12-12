# Project {#concept_th4_4v1_5db .concept}

project is the basic unit of operation in MaxCompute. It is similar to the concept of Database or Schema in traditional databases, and sets the boundary for MaxCompute multi-user isolation and access control.

You can have multiple project permissions at the same time and, by granting relevant authorization, users can access the objects of another project in their own project , such as [Table](reseller.en-US/User Guide/Definition/Table.md), [Resource](reseller.en-US/User Guide/Definition/Resource.md), [Function](reseller.en-US/User Guide/Definition/Function.md) and [Instance](reseller.en-US/User Guide/Definition/Instance.md).

To enter a project \(in this example, ‘my project’\), run the Use Project command, as follows:

```
use my_project -- Use this command to enter the project space named my_project.
```

After running the preceding command, you can enter a project named "my\_project" and all objects in this project can operated. Use Use Project is a command provided by the MaxCompute client.  Before giving a detailed introduction to this part, the document will give a brief introduction to the commonly used commands.For more commands, see [Common Commands](../../../../reseller.en-US/User Guide/Common commands/List of common commands.md).

**Note:** A MaxCompute project is the workspace of DataWorks.

