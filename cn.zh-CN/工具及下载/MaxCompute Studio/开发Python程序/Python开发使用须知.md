# Python开发使用须知 {#concept_vt2_sdd_xdb .concept}

MaxCompute Studio支持您在Intellij IDEA中完成Python相关的开发，包括UDF和PyODPS脚本，但使用前必须安装Python、PyODPS和IDEA的Python插件。

## 安装PyODPS {#section_ngm_b3d_xdb .section}

PyODPS是MaxCompute的PyODPS SDK，详情请参见[Python SDK](../../../../cn.zh-CN/SDK 参考/Python SDK.md)。

## 安装Python插件 {#section_ogm_b3d_xdb .section}

在Intellij IDEA的插件仓库中搜索Python Community Edition插件并安装。

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/13746/15447827463389_zh-CN.png)

## 配置Python依赖 {#section_ycn_z3d_xdb .section}

配置Studio Module对Python的依赖，即可进行MaxCompute Python的开发。

1.  导航至**File** \> **Project Structure**，添加Python SDK。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/13746/15447827463391_zh-CN.png)

2.  导航至**File** \> **Project Structure**，添加Python Facets。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/13746/15447827463392_zh-CN.png)

3.  导航至**File** \> **Project Structure**，配置Module依赖Python Facets。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/13746/15447827463393_zh-CN.png)


