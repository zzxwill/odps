# How to develop UDF using Studio {#concept_fmq_1n4_vdb .concept}

How to develop the MaxCompute Java UDF using MaxCompute Studio?

1.  **Add a module:** Refer to [creating the maxcompute Java module](reseller.en-US/Tools and Downloads/MaxCompute Studio/Develop SQL procedure/Create MaxCompute Script module.md#), where our UDF code will be placed.
2.  **Develop the UDF**. For more information, see [UDF development](reseller.en-US/Tools and Downloads/MaxCompute Studio/Developing Java/Develop and debug UDF.md#). MaxCompute Studio provides the UDF development template. You can complete UDF development based on the template.
3.  Perform a **test**. MaxCompute Studio provides the mechanism for local UDF debugging. You can compile your own test cases based on the UDF test template.
4.  **Package the UDF source code**. You can use the packaging function provided by Data IDE to package the UDF source code to a .jar package.
5.  **Register and release the UDF:** After the .jar package is prepared, add resources and register functions.  After functions are registered,  the UDF can be viewed in the Functions node of the Project Explorer window of MaxCompute. The UDF can also be used in the script editor.

