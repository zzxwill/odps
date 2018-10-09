# Client {#concept_dvj_dzw_5db .concept}

This article describes how to use the basic functions of the MaxCompute using client command line tool.  Before using the MaxCompute client, you must [install and configure the client](../../../../intl.en-US/Prepare/Install and configure a client.md) .

**Note:** 

-   Do not perform the analysis operation based on the output format of the client.  The output format of the client is not ensured for forward compatibility. Clients of different versions are different in their command formats and behaviors.
-   For more information about basic commands of the client, see [Basic commands](../../../../intl.en-US/User Guide/Common commands/Overview of Common commands.md). 
-   Download [MaxCompute console](http://repo.aliyun.com/download/odpscmd/latest/odpscmd_public.zip)
-   The client supports JDK 1.9 from the 0.28.0 version, and the previous version can only use JDK 1.8.

After the client is installed and configured, you can use a command line to perform the following operations.

## Get Help {#section_p5h_41x_5db .section}

To view the help information of the console, the command format is as follows:

```
odps @>./bin/odpscmd-h;
```

You can also input `h;` or `help;`\(case-insensitive\) in an interactive mode. 

The console also provides the `help [keyword];` command to get the command prompts related to the keyword.  For example, input `help table;` to get command prompts related to the table operation as follows:

```

odps @ odps> help table;
Usage: alter table merge smallfiles
Usage: show tables [in]
      a list of tables | ls [-p,-project]
Usage: describe deserves mention | [.] [partition ()]
Usage: read [.]  [partition ()] [line_num]
```

## Start parameters {#section_u5h_41x_5db .section}

When start the console, you can specify a series of parameters as follows:

```

Usage: odpscmd [option]...
where options include:
    --help (-h) for help
    --project= use project
    --endpoint= set endpoint
    -u-p user name and password
    -k will skip begining queries and start from specified position
    -r set retry times
    -f <"file_path;"> execute command in file
    -e <"command; [command;]..."> execute command, include sql command
    -C will display job counters
```

Take the -f parameter as an example, the operation is as follows:

1.  Prepare the local script file script.txt. Suppose that the file is located in the disk D, and the content is shown as follows:

    ```
    
    DROP TABLE IF EXISTS test_table_mj;
    CREATE TABLE test_table_mj (id string, name string);
    DROP TABLE test_table_mj;
    ```

2.  Run the following command:

    ```
    odpscmd\bin>odpscmd -f ./script.sql;
    ```


## Interactive mode {#section_evh_41x_5db .section}

Run the console to directly enter the interactive mode:

```

[admin: ~]$odpscmd
Aliyun ODPS Command Line Tool
Version 1.0
@Copyright 2012 Alibaba Cloud Computing Co., Ltd. All rights reserved.
odps@ odps> INSERT OVERWRITE TABLE DUAL SELECT * FROM DUAL;
```

Enter the command at the cursor position \(use a semicolon as a statement terminator\), and press Enter to run.

## Continuous running {#section_hvh_41x_5db .section}

-   When using -e or -f option to run a command, if there are multiple statements, and you want to start running from a middle statement, you can specify the parameter -k, indicating to ignore the previous statements and to start running from the specified position.  When the parameter <= 0 is specified,  the execution starts from the first statement.
-   Each statement separated by a semicolon is considered as a valid statement. The statements which run successfully or fail to run are printed out at runtime.

For example, 

suppose there are three SQL statements in the file /tmp/dual.sql:

```

drop table dual;
create table dual (dummy string);
insert overwrite table dual select count(*) from dual;
```

To ignore the first two statements, and start running from the third statement, the command format is as follows:

```
odpscmd–k 3 –f dual.sql
```

## Get current logon user {#section_lvh_41x_5db .section}

To get current logon user, the command format is as follows:

```
whoami;
```

Use example:

```

odps@ hiveut>whoami;
Name: odpstest@aliyun.com
End_Point: http://service.odps.aliyun.com/api
Project: lijunsecuritytest
```

Use the preceding command to get the current logon user Alibaba Cloud account, endpoint configuration, and project name.

## Exit {#section_ovh_41x_5db .section}

To exit the console, the command format is as follows:

```
odps@ > quit;
```

You can also use the following command to exit the console:

```
odps@ > q;
```

