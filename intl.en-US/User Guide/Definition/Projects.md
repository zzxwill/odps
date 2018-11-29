# Projects {#concept_th4_4v1_5db .concept}

Project is the basic unit of operation in MaxCompute. It is similar to the concept of Database or Schema in traditional databases, and sets the boundary for MaxCompute multi-user isolation and access control. You can have multiple project permissions at the same time and, by granting relevant authorization, users can access the objects of another project in their own project, such as [Table](intl.en-US/Product Introduction/Definition/Table.md), [Resource](intl.en-US/Product Introduction/Definition/Resource.md), [Function](intl.en-US/Product Introduction/Definition/Function.md) and [Instance](intl.en-US/Product Introduction/Definition/Instance.md).

To enter a project \(in this example, ‘my project’\), run the Use Project command, as follows:

```
use my_project -- Use this command to enter the project space named my_project.
```

After running the preceding command, you can enter a project named "my\_project" and all objects in this project can operated. Use Use Project is a command provided by the MaxCompute client.  Before giving a detailed introduction to this part, the document will give a brief introduction to the commonly used commands.For more commands, see [Common Commands](../../../../intl.en-US/User Guide/Common commands/Overview of Common commands.md).

