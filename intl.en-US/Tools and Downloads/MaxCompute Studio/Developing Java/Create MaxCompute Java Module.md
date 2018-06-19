# Create MaxCompute Java Module {#concept_twh_wxf_vdb .concept}

MaxCompute Studio supports Java user-defined function \(UDF\) and MapReduce development. First, a MaxCompute Java module must be created.

## Create a module {#section_l54_3yf_vdb .section}

Choose `File > New > Module`, set the module type to `MaxCompute Java`,  and configure `Java  JDK`.  Click **Next**, enter a module name, and click **Finish**.  MaxCompute Studio automatically creates a Maven  module and introduces MaxCompute dependencies.

## Module structure {#section_m54_3yf_vdb .section}

So far, a module for developing a MaxCompute Java program has been established, that is the mDev shown in the following figure.  Its main directories include:

-   src/main/java: Source code for Java program development.

-   examples: Sample code, including unit test \(UT\) examples. You can see the examples to develop or compile UT.

-   warehouse: Schema and data required for running locally.


![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/12129/1928_en-US.png)

