# Project Operations {#concept_qg3_s32_vdb .concept}

## Enter the project {#section_mv5_sj2_vdb .section}

**Command format:**

```
use <project_name>;
```

**Action:**

-   Enter the specified project.  After entering the project, all objects in this project can be operated by the user.
-   If the project does not exist or the current user is not in this project, an exception is returned.

**Example:**

```
odps:my_project>use my_project; --my_project is a project the user has privilege to access.
```

**Note:** The preceding examples uses the MaxCompute client.  All  MaxCompute command keywords, project names, table names, column names are case insensitive.

After running the command, a user can access the objects of this project. In the following example, assume that test\_src exists in the project ‘my\_project’. Run the following command:

```
odps:my_project>select * from test_src;
```

MaxCompute automatically searches the table in my\_project. If the table exists, it returns the data of this table. If the table does not exist, an exception is thrown. To access the table test\_src in another project, such as ‘my\_project2’, through the project ‘my\_project’, you must first specify the project name as follows:

```
odps:my_project>select * from my_project2.test_src;
```

The returned data is the data in my\_project2, not the initial data of test\_src in my\_project.

MaxCompute does not support commands to create or delete projects.  You can use the MaxCompute console for additional configurations and operations as needed.

