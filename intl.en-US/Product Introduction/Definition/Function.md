# Function {#concept_tsg_whb_5db .concept}

MaxCompute provides SQL computing capabilities. In MaxCompute SQL, you can use the [system’s built-in functions](../../../../reseller.en-US/User Guide/SQL/Builtin Function/Mathematical Functions.md) to perform common computing and counting tasks.  If the built-in functions do not meet your requirements, you can use the Java programming interface provided by MaxCompute to develop  user-defined functions \(UDFs\).

[UDFs](../../../../reseller.en-US/User Guide/SQL/UDF/UDF Summary.md) can be divided into scalar valued functions, user-defined aggregate functions \(UDAFs\), and user-defined tables functions \(UDTFs\). 

After writing the code for a UDF, you must compile the code into a JAR package and upload this package to MaxCompute. Then, you can register the UDF in MaxCompute.

**Note:** UDFs are used in the same way as built-in functions, in that you specify the UDF name and input relevant parameters in SQL.

For more information,  see [Function introduction](../../../../reseller.en-US/User Guide/Common commands/Functions.md).

