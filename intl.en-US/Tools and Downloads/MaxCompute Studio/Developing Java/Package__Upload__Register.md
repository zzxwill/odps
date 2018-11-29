# Package/Upload/Register {#concept_ndv_g3g_vdb .concept}

After a [user-defined function](EN-US_TP_12130.dita) or [MapReduce](EN-US_TP_12131.dita) is developed,  you must package and release it to the MaxCompute system.

## Package a UDF or MapReduce {#section_snv_j4j_gfb .section}

To release a UDF or MapReduce to the MaxCompute server for production use, you must complete **packaging** \> **uploading** \> **registration** in sequence.  You can use the one-click release function to complete these procedures. MaxCompute Studio runs the mvn clean package command, uploads a JAR package, and registers the UDF in one stop. To use this function, right-click the UDF or MapReduce  and select Deploy to server…. Make sure that the target class is in the **src** \> **main** \> **java** subdirectory and is successfully compiled on the Maven module.  The dialog box shown in the following figure appears. Select the MaxCompute  project to be deployed and enter a resource name and a function name. Click OK and wait until the operation in the background is complete.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/12133/15381305332060_en-US.png)

**Note:** If you require special packaging, you can modify relevant settings in the pom.xml file.  After packaging, follow these steps to upload the JAR package and register the UDF.

## Upload the JAR package {#section_e54_xrg_vdb .section}

After the JAR package is prepared, upload it to the MaxCompute server.

1.  Select Add Resource from the MaxCompute menu.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/12133/15381305332061_en-US.png)

2.  Select the MaxCompute project you want to upload the resource to, the JAR file path, and the resource name you want to register. Determine whether to force update when the resource or function already exists. Then click OK.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/12133/15381305332062_en-US.png)

3.  After uploading is successful, you can view the resource under the Resources node of the Project Explorer window.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/12133/15381305332063_en-US.png)


## Register the UDF {#section_j54_xrg_vdb .section}

After the JAR package is uploaded, register the UDF.

1.  Select **Create Function** from the MaxCompute menu.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/12133/15381305332064_en-US.png)

2.  Select the required resource JAR and JAR main class, and enter the function name. Click **OK**.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/12133/15381305332065_en-US.png)

3.  After the registration is successful, you can view the function under the Functions node of the Project Explorer window.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/12133/15381305332066_en-US.png)


## Apply the UDF {#section_c4d_btg_vdb .section}

-   Apply the UDF in SQL to complete subsequent development.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/12133/15381305332067_en-US.png)


