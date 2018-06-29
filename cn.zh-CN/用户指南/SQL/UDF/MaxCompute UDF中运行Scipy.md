# MaxCompute UDF中运行Scipy {#concept_i5x_ny2_h2b .concept}

新版MaxCompute Isolation Session支持Python UDF。也就是说，Python UDF中已经可以运行二进制包。本文将为您介绍如何在MaxCompute UDF中运行Scipy。

## 下载Scipy包并上传资源 {#section_zdb_cz2_h2b .section}

1.  从PyPI或其他镜像下载Scipy包。

    您需要下载后缀为cp27-cp27m-manylinux1\_x86\_64.whl的包，其他的包会无法加载，包括名为cp27-cp27mu的包，如下图中仅红框中的包可以直接使用。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/15171/6628_zh-CN.png)

2.  下载后将文件名更改为scipy.zip，在MaxCompute Console中执行下述语句。

    ```
    add archive scipy.zip;
    ```

    此时scipy.zip即被创建为MaxCompute Archive资源。

    **说明：** 不建议您使用其他类型的资源，因为在执行时，MaxCompute会自动解压Archive类型的资源，从而省去手动解压的步骤。


## 从非Whl包生成Whl包 {#section_wnp_lch_h2b .section}

如果列出的包中包含Whl，则可以直接上传并跳过此步骤。如果列出的包不包含whl（例如仅有图中的scipy-0.19.0.zip），则需要在Linux环境中手动编译并打包为whl。打包前，需要确保下列命令返回cp27m而不是cp27mu。

```
python -c "import pip; print pip.pep425tags.get_abi_tag()"
```

如果返回值为cp27mu，您需要使用--enable-unicode=no选项编译一个可用的Python 2.7，再使用编译得到的Python。如果返回值正确，通常可以在该环境下使用pythonsetup.pybdist\_wheel完成。

打包完成后，上传生成的whl包。

## 编写和创建UDF {#section_hmy_zch_h2b .section}

1.  您需要编写一个UDF支持计算psi。

    ```
    from odps.udf import annotate
    from odps.distcache import get_cache_archive
    
    def include_package_path(res_name):
        import os, sys
        archive_files = get_cache_archive(res_name)
        dir_names = sorted([os.path.dirname(os.path.normpath(f.name)) for f in archive_files
                           if '.dist_info' not in f.name], key=lambda v: len(v))
        sys.path.append(os.path.dirname(dir_names[0]))
    
    @annotate("double->double")
    class MyPsi(object):
        def __init__(self):        
            include_package_path('scipy.zip')
    
        def evaluate(self, arg0):
            from scipy.special import psi
            return float(psi(arg0))
    ```

    get\_cache\_archive返回一个包含包中所有文件的文件对象。先取出所有的文件名，此后获得最短的路径作为包的路径，并加入sys.path。此后，便可以正常import scipy这个包。

    **说明：** 因为MaxCompute会在执行前通过原有的沙箱检查UDF的输入/输出，因而include\_package\_path和import在函数外调用会报错。

2.  编写完成后，将代码保存为my\_psi.py，并在MaxCompute Console中执行`addpymy_psi.py;`。
3.  在MaxCompute Console中执行下述命令创建函数。

    ```
    create function my_psi as my_psi.MyPsi using my_psi.py,scipy.zip;
    ```

    **说明：** 在创建函数时，不要忘记加上之前上传的包，例如上面的scipy.zip。


## 执行 {#section_ymy_c2h_h2b .section}

创建UDF后，即可在MaxCompute Console中执行查询语句（暂不支持pypy，因此需禁用pypy）。

```
set odps.pypy.enabled=false;
set odps.isolation.session.enable = true;
select my_psi(sepal_length) from iris;
```

## 其他 {#section_ip5_g2h_h2b .section}

如果包依赖了其他Python包，需要一同上传并同时加入到UDF依赖中。

使用0.7.4以上的PyODPS DataFrame可以简化使用二进制包的UDF的编写，无需手动调用include\_package\_path。

