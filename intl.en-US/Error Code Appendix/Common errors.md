# Common Errors {#concept_orf_tcw_tdb .concept}

Common error messages for MaxCompute are as follows:

```
Error code: General description - Context description
```

Example: `ODPS-0130131:Table not found - "myproject" "mytable"`.

The error code `ODPS-0130131` and general description `Table not found` have a one-to-one mapping relationship Context description provides information that helps error locating.

The error code format is `ODPS-MMCCCCX`.

-   MM indicates the module ID, which is a two-digit integer.  For example, 00 indicates the common module; 01 indicates SQL Task.

-   CCCC indicates the error code, which is a four-digit integer.

-   X indicates the severity of the error, which is a one-digit integer.  1 indicates a minor error, such as incorrect user input. 9 indicates an error of the highest severity, such as an atomicity error.


For a list of errors related to SQL Task, MapReduce Task, and Tunnel Task commonly used by MaxCompute, see:

-   [MaxCompute SQL Common errors for MaxCompute SQL Task](reseller.en-US/Error Code Appendix/SQL common errors.md)

-   [MaxCompute MapReduce Common errors for MaxCompute MapReduce Task](reseller.en-US/Error Code Appendix/MapReduce Common Errors.md)

-   [MaxCompute Tunnel Common errors for MaxCompute Tunnel Task](reseller.en-US/Error Code Appendix/Tunnel Common Errors.md)


