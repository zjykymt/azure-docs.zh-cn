---
title: 用于高级方案的创作条目脚本
titleSuffix: Azure Machine Learning entry script authoring
author: gvashishtha
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 07/31/2020
ms.author: gopalv
ms.openlocfilehash: 0499cd6885454604e89ce4cadc313b2f68c45156
ms.sourcegitcommit: 8def3249f2c216d7b9d96b154eb096640221b6b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/03/2020
ms.locfileid: "87544551"
---
# <a name="advanced-entry-script-authoring"></a>高级条目脚本创作

本文介绍如何编写用于专用用例的入口脚本。

## <a name="prerequisites"></a>必备知识

本文假设你已有一个要使用 Azure 机器学习部署的经过训练的机器学习模型。 若要了解有关模型部署的详细信息，请参阅[此教程](how-to-deploy-and-where.md)。

## <a name="automatically-generate-a-swagger-schema"></a>自动生成 Swagger 架构

若要为 Web 服务自动生成架构，请在一个已定义的类型对象的构造函数中提供输入和/或输出的示例。 该类型和示例用于自动创建架构。 Azure 机器学习随后会在部署过程中创建 Web 服务的 [OpenAPI](https://swagger.io/docs/specification/about/) (Swagger) 规范。

当前支持以下类型：

* `pandas`
* `numpy`
* `pyspark`
* 标准 Python 对象

若要使用架构生成，请在依赖项文件中包括开源 `inference-schema` 包。 若要详细了解此包，请参阅 [https://github.com/Azure/InferenceSchema](https://github.com/Azure/InferenceSchema)。 定义 `input_sample` 和 `output_sample` 变量中的输入和输出示例格式，它们表示 Web 服务的请求和响应格式。 在 `run()` 函数的输入和输出函数修饰器中使用这些示例。 以下 scikit-learn 示例使用架构生成功能。


## <a name="power-bi-compatible-endpoint"></a>Power BI 兼容终结点 

以下示例演示如何使用数据帧将输入数据定义为 `<key: value>` 字典。 此方法支持使用 Power BI 中已部署的 Web 服务。 （[详细了解如何使用 Power BI 中的 Web 服务](https://docs.microsoft.com/power-bi/service-machine-learning-integration)。）

```python
import json
import pickle
import numpy as np
import pandas as pd
import azureml.train.automl
from sklearn.externals import joblib
from azureml.core.model import Model

from inference_schema.schema_decorators import input_schema, output_schema
from inference_schema.parameter_types.numpy_parameter_type import NumpyParameterType
from inference_schema.parameter_types.pandas_parameter_type import PandasParameterType


def init():
    global model
    # Replace filename if needed.
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'model_file.pkl')
    # Deserialize the model file back into a sklearn model.
    model = joblib.load(model_path)


input_sample = pd.DataFrame(data=[{
    # This is a decimal type sample. Use the data type that reflects this column in your data.
    "input_name_1": 5.1,
    # This is a string type sample. Use the data type that reflects this column in your data.
    "input_name_2": "value2",
    # This is an integer type sample. Use the data type that reflects this column in your data.
    "input_name_3": 3
}])

# This is an integer type sample. Use the data type that reflects the expected result.
output_sample = np.array([0])

# To indicate that we support a variable length of data input,
# set enforce_shape=False
@input_schema('data', PandasParameterType(input_sample, enforce_shape=False))
@output_schema(NumpyParameterType(output_sample))
def run(data):
    try:
        result = model.predict(data)
        # You can return any data type, as long as it is JSON serializable.
        return result.tolist()
    except Exception as e:
        error = str(e)
        return error
```


## <a name="binary-ie-image-data"></a><a id="binary-data"></a>二进制（即图像）数据

如果模型接受二进制数据（如映像），则必须修改用于部署的 `score.py` 文件以接受原始 HTTP 请求。 若要接受原始数据，请在入口脚本中使用 `AMLRequest` 类，并向 `run()` 函数添加 `@rawhttp` 修饰器。

下面是接受二进制数据的 `score.py` 的示例：

```python
from azureml.contrib.services.aml_request import AMLRequest, rawhttp
from azureml.contrib.services.aml_response import AMLResponse


def init():
    print("This is init()")


@rawhttp
def run(request):
    print("This is run()")
    print("Request: [{0}]".format(request))
    if request.method == 'GET':
        # For this example, just return the URL for GETs.
        respBody = str.encode(request.full_path)
        return AMLResponse(respBody, 200)
    elif request.method == 'POST':
        reqBody = request.get_data(False)
        # For a real-world solution, you would load the data from reqBody
        # and send it to the model. Then return the response.

        # For demonstration purposes, this example just returns the posted data as the response.
        return AMLResponse(reqBody, 200)
    else:
        return AMLResponse("bad request", 500)
```

> [!IMPORTANT]
> `AMLRequest` 类位于 `azureml.contrib` 命名空间中。 此命名空间中的实体会频繁更改，因为我们正在改进服务。 此命名空间中的任何内容都应被视为预览版，Microsoft 并不完全支持这些内容。
>
> 如果需要在本地开发环境中对此进行测试，可以使用以下命令安装这些组件：
>
> ```shell
> pip install azureml-contrib-services
> ```

`AMLRequest` 类仅允许访问 score.py 中的原始发布数据，没有客户端组件。 从客户端中，像往常一样发布数据。 例如，以下 Python 代码读取图像文件并发布数据：

```python
import requests
# Load image data
data = open('example.jpg', 'rb').read()
# Post raw data to scoring URI
res = requests.post(url='<scoring-uri>', data=data, headers={'Content-Type': 'application/octet-stream'})
```

<a id="cors"></a>

## <a name="cross-origin-resource-sharing-cors"></a>跨域资源共享 (CORS)

通过跨源资源共享 (CORS) 可以从其他域请求网页上的资源。 CORS 通过 HTTP 标头工作，这些标头通过客户端请求发送并随服务响应返回。 若要详细了解 CORS 和有效标头，请参阅维基百科上的[跨域资源共享 (CORS)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing)。

若要配置模型部署以支持 CORS，请在入口脚本中使用 `AMLResponse` 类。 使用此类，可设置响应对象的标头。

以下示例在入口脚本中设置响应的 `Access-Control-Allow-Origin` 标头：

```python
from azureml.contrib.services.aml_request import AMLRequest, rawhttp
from azureml.contrib.services.aml_response import AMLResponse

def init():
    print("This is init()")

@rawhttp
def run(request):
    print("This is run()")
    print("Request: [{0}]".format(request))
    if request.method == 'GET':
        # For this example, just return the URL for GETs.
        respBody = str.encode(request.full_path)
        return AMLResponse(respBody, 200)
    elif request.method == 'POST':
        reqBody = request.get_data(False)
        # For a real-world solution, you would load the data from reqBody
        # and send it to the model. Then return the response.

        # For demonstration purposes, this example
        # adds a header and returns the request body.
        resp = AMLResponse(reqBody, 200)
        resp.headers['Access-Control-Allow-Origin'] = "http://www.example.com"
        return resp
    else:
        return AMLResponse("bad request", 500)
```

> [!IMPORTANT]
> `AMLResponse` 类位于 `azureml.contrib` 命名空间中。 此命名空间中的实体会频繁更改，因为我们正在改进服务。 此命名空间中的任何内容都应被视为预览版，Microsoft 并不完全支持这些内容。
>
> 如果需要在本地开发环境中对此进行测试，可以使用以下命令安装这些组件：
>
> ```shell
> pip install azureml-contrib-services
> ```


> [!WARNING]
> Azure 机器学习仅将 POST 和 GET 请求路由到运行评分服务的容器。 这可能导致错误，因为浏览器使用 OPTIONS 请求预检 CORS 请求。
> 


## <a name="load-registered-models"></a>加载已注册的模型

可以通过两种方法在入口脚本中查找模型：
* `AZUREML_MODEL_DIR`：一个包含模型位置路径的环境变量。
* `Model.get_model_path`：一个 API，使用注册的模型名称返回指向模型文件的路径。

#### <a name="azureml_model_dir"></a>AZUREML_MODEL_DIR

AZUREML_MODEL_DIR 是在服务部署过程中创建的环境变量。 可以使用此环境变量来查找部署的模型的位置。

下表描述了 AZUREML_MODEL_DIR 的值，它的值取决于部署的模型数：

| 部署 | 环境变量值 |
| ----- | ----- |
| 单个模型 | 包含模型的文件夹的路径。 |
| 多个模型 | 包含所有模型的文件夹的路径。 各个模型按名称和版本放置在此文件夹中 (`$MODEL_NAME/$VERSION`) |

在模型注册和部署过程中，会将模型放置在 AZUREML_MODEL_DIR 路径中，并保留它们的原始文件名。

若要在入口脚本中获取某个模型文件的路径，请将此环境变量与要查找的文件路径组合在一起。

**单个模型示例**
```python
# Example when the model is a file
model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_regression_model.pkl')

# Example when the model is a folder containing a file
file_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'my_model_folder', 'sklearn_regression_model.pkl')
```

**多个模型示例**

在此方案中，向工作区注册两个模型：

* `my_first_model`：包含一个文件 (`my_first_model.pkl`)，并且只有一个版本 (`1`)。
* `my_second_model`：包含一个文件 (`my_second_model.pkl`)，有两个版本；`1` 和 `2`。

部署服务后，部署操作中将同时提供两种模型：

```python
first_model = Model(ws, name="my_first_model", version=1)
second_model = Model(ws, name="my_second_model", version=2)
service = Model.deploy(ws, "myservice", [first_model, second_model], inference_config, deployment_config)
```

在托管服务的 Docker 映像中，`AZUREML_MODEL_DIR` 环境变量包含模型所在的目录。
在此目录中，每个模型都位于 `MODEL_NAME/VERSION` 的目录路径中。 其中 `MODEL_NAME` 是已注册的模型的名称，`VERSION` 是模型的版本。 构成已注册的模型的文件存储在这些目录中。

在此示例中，路径将是 `$AZUREML_MODEL_DIR/my_first_model/1/my_first_model.pkl` 和 `$AZUREML_MODEL_DIR/my_second_model/2/my_second_model.pkl`。


```python
# Example when the model is a file, and the deployment contains multiple models
first_model_name = 'my_first_model'
first_model_version = '1'
first_model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), first_model_name, first_model_version, 'my_first_model.pkl')
second_model_name = 'my_second_model'
second_model_version = '2'
second_model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), second_model_name, second_model_version, 'my_second_model.pkl')
```

### <a name="get_model_path"></a>get_model_path

注册模型时，请提供用于在注册表中管理该模型的模型名称。 将此名称与 [Model.get_model_path()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#get-model-path-model-name--version-none---workspace-none-) 方法结合使用，以检索本地文件系统上一个或多个模型文件的路径。 如果注册文件夹或文件集合，此 API 会返回包含这些文件的目录的路径。

注册模型时，请为其指定一个名称。 该名称对应于模型的放置位置（本地位置或在服务部署过程中指定的位置）。

## <a name="next-steps"></a>后续步骤

* [排查失败的部署问题](how-to-troubleshoot-deployment.md)
* [部署到 Azure Kubernetes 服务](how-to-deploy-azure-kubernetes-service.md)
* [创建使用 web 服务的客户端应用程序](how-to-consume-web-service.md)
* [更新 web 服务](how-to-deploy-update-web-service.md)
* [如何使用自定义 Docker 映像部署模型](how-to-deploy-custom-docker-image.md)
* [使用 TLS 通过 Azure 机器学习保护 Web 服务](how-to-secure-web-service.md)
* [使用 Application Insights 监视 Azure 机器学习模型](how-to-enable-app-insights.md)
* [为生产环境中的模型收集数据](how-to-enable-data-collection.md)
* [为模型部署创建事件警报和触发器](how-to-use-event-grid.md)
