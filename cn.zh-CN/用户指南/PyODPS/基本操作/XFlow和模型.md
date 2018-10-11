# XFlow和模型 {#concept_j4r_nvf_cfb .concept}

XFlow是ODPS对算法包的封装，使用PyODPS可以执行XFlow。

## XFlow {#section_ffq_2rg_cfb .section}

对于下面的PAI命令：

```
PAI -name AlgoName -project algo_public -Dparam1=param_value1 -Dparam2=param_value2 ...
```

可以使用如下方法调用：

```
 # 异步调用
 inst = o.run_xflow('AlgoName', 'algo_public',
                       parameters={'param1': 'param_value1', 'param2': 'param_value2', ...})
```

或者使用同步调用：

```
 # 同步调用
 inst = o.execute_xflow('AlgoName', 'algo_public',
                           parameters={'param1': 'param_value1', 'param2': 'param_value2', ...})
```

参数不应包含命令两端的引号（如果有），也不应该包含末尾的分号。

这两个方法都会返回一个Instance对象。由于XFlow的一个Instance包含若干个子 Instance，需要使用下面的方法来获得每个Instance的LogView：

```
 for sub_inst_name, sub_inst in o.get_xflow_sub_instances(inst).items():
     print('%s: %s' % (sub_inst_name, sub_inst.get_logview_address()))
```

需要注意的是，

`get_xflow_sub_instances`

返回的是Instance当前的子Instance，可能会随时间变化，因而可能需要定时查询。 为简化这一步骤，可以使用

`iter_xflow_sub_instances 方法`

。该方法返回一个迭代器，会阻塞执行直至发现新的子Instance或者主Instance结束：

```
 # 此处建议使用异步调用
 inst = o.run_xflow('AlgoName', 'algo_public',
                       parameters={'param1': 'param_value1', 'param2': 'param_value2', ...})
 for sub_inst_name, sub_inst in o.iter_xflow_sub_instances(inst):  # 此处将等待
     print('%s: %s' % (sub_inst_name, sub_inst.get_logview_address()))
```

在调用run\_xflow或者execute\_xflow时，也可以指定运行参数，指定的方法与SQL类似：

```
 parameters = {'param1': 'param_value1', 'param2': 'param_value2', ...}
 o.execute_xflow('AlgoName', 'algo_public', parameters=parameters, hints={'odps.xxx.yyy': 10})
```

使用options.ml.xflow\_settings可以配置全局设置：

```
 from odps import options
 options.ml.xflow_settings = {'odps.xxx.yyy': 10}
 parameters = {'param1': 'param_value1', 'param2': 'param_value2', ...}
 o.execute_xflow('AlgoName', 'algo_public', parameters=parameters)
```

PAI命令的文档可以参考 [这份文档](https://help.aliyun.com/document_detail/42703.html) 。

XFlow模型

在线模型是ODPS提供的模型在线部署能力。

-   在线模型

    用户可以通过Pipeline部署自己的模型。详细信息请参考“机器学习平台——在线服务”章节。

    需要注意的是，在线模型的服务使用的是独立的Endpoint，需要配置Predict Endpoint。通过

    ```
     o = ODPS('your-access-id', 'your-secret-access-key', 'your-default-project',
             endpoint='your-end-point', predict_endpoint='predict_endpoint')
    ```

    即可在ODPS对象上添加相关配置。Predict Endpoint的地址请参考相关说明或咨询管理员。

-   部署离线模型上线

    PyODPS提供了离线模型的部署功能。部署方法为：

    ```
     model = o.create_online_model('online_model_name', 'offline_model_name')
    ```

-   部署自定义Pipeline上线

    含有自定义Pipeline的在线模型可自行构造ModelPredictor对象，例子如下：

    ```
     from odps.models.ml import ModelPredictor, ModelProcessor, BuiltinProcessor, PmmlProcessor, PmmlRunMode
     predictor = ModelPredictor(target_name='label')
     predictor.pipeline.append(BuiltinProcessor(offline_model_name='sample_offlinemodel',
                                                offline_model_project='online_test'))
     predictor.pipeline.append(PmmlProcessor(pmml='data_preprocess.xml',
                                             resources='online_test/resources/data_preprocess.xml',
                                             run_mode=PmmlRunMode.Converter))
     predictor.pipeline.append(CustomProcessor(class_name='SampleProcessor',
                                               lib='libsample_processor.so',
                                               resources='online_test/resources/sample_processor.tar.gz'))
    model = o.create_online_model('online_model_name', predictor)
    ```

    其中，BuiltinProcessor、PmmlProcessor 和 CustomProcessor 分别指 ODPS OfflineModel 形成的 Pipeline 节点、PMML 模型文件形成的 Pipeline 节点和用户自行开发的 Pipeline 节点。

-   在线模型操作

    与其他 ODPS 对象类似，创建后，可列举、获取和删除在线模型：

    ```
     models = o.list_online_models(prefix='prefix')
     model = o.get_online_model('online_model_name')
     o.delete_online_model('online_model_name')
    ```

    可使用模型名和数据进行在线预测，输入数据可以是 Record，也可以是字典或数组和 Schema 的组合：

    ```
     data = [[4, 3, 2, 1], [1, 2, 3, 4]]
     result = o.predict_online_model('online_model_name', data,
                                     schema=['sepal_length', 'sepal_width', 'petal_length', 'petal_width'])
    ```

    也可为模型设置 ABTest。参数中的 modelx 可以是在线模型名，也可以是 get\_online\_model 获得的模型对象本身，而 percentagex 表示 modelx 在 ABTest 中所占的百分比，范围为 0 至 100：

    ```
     result = o.config_online_model_ab_test('online_model_name', model1, percentage1, model2, percentage2)
    ```

    修改模型参数可以通过修改 OnlineModel 对象的属性，再调用 update 方法实现，如

    ```
     model = o.get_online_model('online_model_name')
     model.cpu = 200
     model.update()
    ```

    与其他对象不同的是，在线模型的创建和删除较为耗时。PyODPS 默认 create\_online\_model 和 delete\_online\_model 以及 OnlineModel 的 update 方法在整个操作完成后才返回。用户可以通过 async 选项控制是否要在模型创建请求提交后立即返回， 然后自己控制等待。例如，下列语句

    ```
    model = o.create_online_model('online_model_name', 'offline_model_name')
    ```

    等价于

    ```
    model = o.create_online_model('online_model_name', 'offline_model_name', async=True)
    model.wait_for_service()
    ```

    而

    ```
    o.delete_online_model('online_model_name')
    ```

    等价于

    ```
    o.delete_online_model('* online_model_name *', async=True)
     model.wait_for_deletion()
    ```


