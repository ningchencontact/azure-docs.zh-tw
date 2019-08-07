---
title: 部署模型的方式和位置
titleSuffix: Azure Machine Learning service
description: 了解部署 Azure Machine Learning 服務模型的方式和位置，這類模型包括：Azure 容器執行個體、Azure Kubernetes Service、Azure IoT Edge 或可現場程式化閘道陣列。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 08/06/2019
ms.custom: seoapril2019
ms.openlocfilehash: 7e88b99cf0ecede64d75b36eafdcc88798e2e4a4
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/06/2019
ms.locfileid: "68840455"
---
# <a name="deploy-models-with-the-azure-machine-learning-service"></a>使用 Azure Machine Learning 服務部署模型

瞭解如何將您的機器學習模型部署為 Azure 雲端中的 web 服務, 或 IoT Edge 裝置。 

無論[您部署](#target)模型的位置為何, 工作流程都很類似:

1. 註冊模型。
1. 準備部署 (指定資產、使用量、計算目標)
1. 將模型部署到計算目標。
1. 測試已部署的模型, 也稱為 web 服務。

如需部署工作流程中相關概念的詳細資訊，請參閱[使用 Azure Machine Learning 服務來管理、部署及監視模型](concept-model-management-and-deployment.md)。

## <a name="prerequisites"></a>先決條件

- 模型。 如果您沒有定型的模型, 您可以使用[本教學](https://aka.ms/azml-deploy-cloud)課程中提供的模型 & 相依性檔案。

- [Machine Learning 服務的 Azure CLI 延伸](reference-azure-machine-learning-cli.md)模組, [Azure Machine Learning Python SDK](https://aka.ms/aml-sdk), 或[Azure Machine Learning Visual Studio Code 延伸](how-to-vscode-tools.md)模組。

## <a id="registermodel"></a>註冊您的模型

組成模型的一或多個檔案之已註冊的模型邏輯容器。 例如, 如果您的模型儲存在多個檔案中, 您可以在工作區中將其註冊為單一模型。 註冊之後, 您就可以下載或部署已註冊的模型, 並接收所有已註冊的檔案。

機器學習模型會在您的 Azure Machine Learning 工作區中註冊。 模型可能來自 Azure Machine Learning, 也可能來自其他地方。 下列範例示範如何從檔案註冊模型:

### <a name="register-a-model-from-an-experiment-run"></a>從實驗執行註冊模型

+ **Scikit-learn-瞭解使用 SDK 的範例**
  ```python
  model = run.register_model(model_name='sklearn_mnist', model_path='outputs/sklearn_mnist_model.pkl')
  print(model.name, model.id, model.version, sep='\t')
  ```

  > [!TIP]
  > 若要在模型註冊中包含多個檔案`model_path` , 請將設定為包含檔案的目錄。

+ **使用 CLI**

  ```azurecli-interactive
  az ml model register -n sklearn_mnist  --asset-path outputs/sklearn_mnist_model.pkl  --experiment-name myexperiment --run-id myrunid
  ```

  > [!TIP]
  > 若要在模型註冊中包含多個檔案`--asset-path` , 請將設定為包含檔案的目錄。

+ **使用 VS Code**

  使用任何模型檔案或具有[VS Code](how-to-vscode-tools.md#deploy-and-manage-models)副檔名的資料夾來註冊模型。

### <a name="register-an-externally-created-model"></a>註冊外部建立的模型

[!INCLUDE [trusted models](../../../includes/machine-learning-service-trusted-model.md)]

您可以藉由提供模型的**本機路徑**來註冊外部建立的模型。 您可以提供資料夾或單一檔案。

+ **使用 Python SDK 的 ONNX 範例:**
  ```python
  onnx_model_url = "https://www.cntk.ai/OnnxModels/mnist/opset_7/mnist.tar.gz"
  urllib.request.urlretrieve(onnx_model_url, filename="mnist.tar.gz")
  !tar xvzf mnist.tar.gz
  
  model = Model.register(workspace = ws,
                         model_path ="mnist/model.onnx",
                         model_name = "onnx_mnist",
                         tags = {"onnx": "demo"},
                         description = "MNIST image classification CNN from ONNX Model Zoo",)
  ```

  > [!TIP]
  > 若要在模型註冊中包含多個檔案`model_path` , 請將設定為包含檔案的目錄。

+ **使用 CLI**
  ```azurecli-interactive
  az ml model register -n onnx_mnist -p mnist/model.onnx
  ```

  > [!TIP]
  > 若要在模型註冊中包含多個檔案`-p` , 請將設定為包含檔案的目錄。

**估計時間**：大約 10 秒。

如需詳細資訊，請參閱 [Model 類別](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py) \(英文\) 的參考文件。

如需使用 Azure Machine Learning 服務外部定型模型的詳細資訊, 請參閱[如何部署現有的模型](how-to-deploy-existing-model.md)。

<a name="target"></a>

## <a name="choose-a-compute-target"></a>選擇計算目標

下列計算目標或計算資源可以用來裝載您的 web 服務部署。 

[!INCLUDE [aml-compute-target-deploy](../../../includes/aml-compute-target-deploy.md)]

## <a name="prepare-to-deploy"></a>準備部署

若要部署為 web 服務, 您必須建立推斷設定 (`InferenceConfig`) 和部署設定。 推斷 (或模型計分) 是已部署的模型用於預測的階段, 最常見的是生產資料。 在推斷設定中, 您可以指定服務模型所需的腳本和相依性。 在 [部署設定] 中, 您可以指定如何在計算目標上提供模型的詳細資料。

> [!IMPORTANT]
> Azure Machine Learning SDK 不會提供 web 服務或 IoT Edge 部署的方式來存取資料存放區或資料集。 如果您需要部署的模型來存取儲存在部署外部的資料, 例如在 Azure 儲存體帳戶中, 您必須使用相關的 SDK 開發自訂程式碼解決方案。 例如,[適用于 Python 的 AZURE 儲存體 SDK](https://github.com/Azure/azure-storage-python)。
>
> 另一個適用于您案例的替代方法是[批次預測](how-to-run-batch-predictions.md), 這可在計分時提供對資料存放區的存取。

### <a id="script"></a> 1.定義您的輸入腳本 & 相依性

輸入腳本會接收提交至已部署 web 服務的資料, 並將它傳遞至模型。 然後，它會採用模型傳回的回應，並將該回應傳回至用戶端。 **腳本是您的模型特有的**;它必須瞭解模型預期和傳回的資料。

此腳本包含兩個載入和執行模型的函式:

* `init()`:此函式通常會將模型載入到全域物件。 此函式只會在 web 服務的 Docker 容器啟動時執行一次。

* `run(input_data)`:此函式會使用模型，依據輸入資料來預測值。 執行的輸入和輸出通常使用 JSON 進行序列化和還原序列化。 您也可以使用原始的二進位資料。 您可以先轉換資料，再將資料傳送給模型或傳回用戶端。

#### <a name="what-is-get_model_path"></a>什麼是 get_model_path？

當您註冊模型時, 您會提供用來在登錄中管理模型的模型名稱。 您會將此名稱與[模型搭配使用。 get _model_path ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#get-model-path-model-name--version-none---workspace-none-)可在本機檔案系統上取出模型檔案的路徑。 如果您註冊資料夾或檔案集合, 此 API 會傳回包含這些檔案之目錄的路徑。

當您註冊模型時, 您可以為它指定一個名稱, 它會對應至模型的放置位置, 不論是在本機或在服務部署期間。

下列範例會傳回名`sklearn_mnist_model.pkl`為之單一檔案的路徑 (已使用名稱`sklearn_mnist`註冊):

```python
model_path = Model.get_model_path('sklearn_mnist')
```

#### <a name="optional-automatic-schema-generation"></a>選擇性自動產生架構

若要自動產生 web 服務的架構, 請在其中一個已定義型別物件的函式中提供輸入和/或輸出的範例, 並使用型別和範例來自動建立架構。 Azure Machine Learning 服務接著會在部署期間建立 web 服務的[OpenAPI](https://swagger.io/docs/specification/about/) (Swagger) 規格。

目前支援下列類型:

* `pandas`
* `numpy`
* `pyspark`
* 標準 Python 物件

若要使用架構產生, 請`inference-schema`在您的 conda 環境檔案中包含封裝。

##### <a name="example-dependencies-file"></a>範例相依性檔案

下列 YAML 是用於推斷的 Conda 相依性檔案範例。

```YAML
name: project_environment
dependencies:
  - python=3.6.2
  - pip:
    - azureml-defaults
    - scikit-learn==0.20.0
    - inference-schema[numpy-support]
```

如果您想要使用自動產生架構, 您的輸入腳本**必須**匯`inference-schema`入封裝。

在`input_sample` 和`output_sample`變數中定義輸入和輸出範例格式, 其代表 web 服務的要求和回應格式。 在函式的 input 和 output 函數裝飾專案`run()`中使用這些範例。 以下的 scikit-learn-瞭解範例會使用架構產生。

##### <a name="example-entry-script"></a>範例專案腳本

下列範例示範如何接受並傳回 JSON 資料:

```python
#example: scikit-learn and Swagger
import json
import numpy as np
from sklearn.externals import joblib
from sklearn.linear_model import Ridge
from azureml.core.model import Model

from inference_schema.schema_decorators import input_schema, output_schema
from inference_schema.parameter_types.numpy_parameter_type import NumpyParameterType


def init():
    global model
    # note here "sklearn_regression_model.pkl" is the name of the model registered under
    # this is a different behavior than before when the code is run locally, even though the code is the same.
    model_path = Model.get_model_path('sklearn_regression_model.pkl')
    # deserialize the model file back into a sklearn model
    model = joblib.load(model_path)


input_sample = np.array([[10, 9, 8, 7, 6, 5, 4, 3, 2, 1]])
output_sample = np.array([3726.995])


@input_schema('data', NumpyParameterType(input_sample))
@output_schema(NumpyParameterType(output_sample))
def run(data):
    try:
        result = model.predict(data)
        # you can return any datatype as long as it is JSON-serializable
        return result.tolist()
    except Exception as e:
        error = str(e)
        return error
```

下列範例示範如何使用資料框架, 將輸入資料定義為`<key: value>`字典。 此方法支援從 Power BI 取用已部署的 web 服務 ([深入瞭解如何從 Power BI 使用 web 服務](https://docs.microsoft.com/power-bi/service-machine-learning-integration)):

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
    # replace model_name with your actual model name, if needed
    model_path = Model.get_model_path('model_name')
    # deserialize the model file back into a sklearn model
    model = joblib.load(model_path)


input_sample = pd.DataFrame(data=[{
    # This is a decimal type sample. Use the data type that reflects this column in your data
    "input_name_1": 5.1,
    # This is a string type sample. Use the data type that reflects this column in your data
    "input_name_2": "value2",
    # This is a integer type sample. Use the data type that reflects this column in your data
    "input_name_3": 3
}])

# This is a integer type sample. Use the data type that reflects the expected result
output_sample = np.array([0])


@input_schema('data', PandasParameterType(input_sample))
@output_schema(NumpyParameterType(output_sample))
def run(data):
    try:
        result = model.predict(data)
        # you can return any datatype as long as it is JSON-serializable
        return result.tolist()
    except Exception as e:
        error = str(e)
        return error
```

如需更多範例腳本, 請參閱下列範例:

* Pytorch[https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-pytorch](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-pytorch)
* TensorFlow[https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-tensorflow](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-tensorflow)
* Keras[https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras)
* ONNX[https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/onnx/](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/onnx/)
* 針對二進位資料進行計分:[如何使用 web 服務](how-to-consume-web-service.md)

### <a name="2-define-your-inferenceconfig"></a>2.定義您的 InferenceConfig

推斷設定會描述如何設定模型來進行預測。 下列範例示範如何建立推斷設定。 此設定會指定執行時間、專案腳本, 以及 (選擇性) conda 環境檔案:

```python
inference_config = InferenceConfig(runtime="python",
                                   entry_script="x/y/score.py",
                                   conda_file="env/myenv.yml")
```

如需詳細資訊, 請參閱[InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py)類別參考。

如需有關使用自訂 Docker 映射搭配推斷設定的詳細資訊, 請參閱[如何使用自訂 docker 映射部署模型](how-to-deploy-custom-docker-image.md)。

### <a name="cli-example-of-inferenceconfig"></a>InferenceConfig 的 CLI 範例

[!INCLUDE [inference config](../../../includes/machine-learning-service-inference-config.md)]

下列命令示範如何使用 CLI 部署模型:

```azurecli-interactive
az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json
```

在此範例中, 設定包含下列專案:

* 此模型需要 Python
* [輸入腳本](#script), 用來處理傳送至已部署服務的 web 要求
* 描述推斷所需之 Python 套件的 conda 檔案

如需有關使用自訂 Docker 映射搭配推斷設定的詳細資訊, 請參閱[如何使用自訂 docker 映射部署模型](how-to-deploy-custom-docker-image.md)。

### <a name="3-define-your-deployment-configuration"></a>3.定義您的部署設定

部署之前, 您必須先定義部署設定。 __部署設定適用于將裝載 web 服務的計算目標__。 例如, 在本機部署時, 您必須指定服務接受要求的埠。

您可能也需要建立計算資源。 例如, 如果您還沒有與工作區相關聯的 Azure Kubernetes Service。

下表提供建立每個計算目標之部署設定的範例:

| 計算目標 | 部署設定範例 |
| ----- | ----- |
| 本機 | `deployment_config = LocalWebservice.deploy_configuration(port=8890)` |
| Azure 容器執行個體 | `deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |
| Azure Kubernetes Service | `deployment_config = AksWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |

> [!TIP]
> 在將您的模型部署為服務之前, 您可能會想要進行分析, 以判斷最佳的 CPU 和記憶體需求。 您可以使用 SDK 或 CLI 來分析您的模型。 如需詳細資訊, 請參閱[設定檔 ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#profile-workspace--profile-name--models--inference-config--input-data-)和[az ml 模型設定檔](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-profile)參考。
>
> 模型分析結果會以物件的`Run`形式發出。 如需詳細資訊, 請參閱[ModelProfile](https://docs.microsoft.com/python/api/azureml-core/azureml.core.profile.modelprofile?view=azure-ml-py)類別參考。

## <a name="deploy-to-target"></a>部署至目標

部署會使用推斷設定部署設定來部署模型。 無論計算目標為何, 部署程式都很類似。 部署至 AKS 稍有不同, 因為您必須提供 AKS 叢集的參考。

### <a id="local"></a>本機部署

若要在本機部署, 您必須在本機電腦上**安裝 Docker** 。

#### <a name="using-the-sdk"></a>使用 SDK

```python
deployment_config = LocalWebservice.deploy_configuration(port=8890)
service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config)
service.wait_for_deployment(show_output = True)
print(service.state)
```

如需詳細資訊, 請參閱[LocalWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py)、 [Model. deploy ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config--deployment-config-none--deployment-target-none-)和[Webservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice?view=azure-ml-py)的參考檔。

#### <a name="using-the-cli"></a>使用 CLI

若要使用 CLI 進行部署, 請使用下列命令。 以`mymodel:1`已註冊模型的名稱和版本取代:

```azurecli-interactive
az ml model deploy -m mymodel:1 -ic inferenceconfig.json -dc deploymentconfig.json
```

[!INCLUDE [aml-local-deploy-config](../../../includes/machine-learning-service-local-deploy-config.md)]

如需詳細資訊, 請參閱[az ml model deploy](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy) reference。

### <a id="aci"></a>Azure 容器實例 (DEVTEST)

請參閱[部署至 Azure 容器實例](how-to-deploy-azure-container-instance.md)。

### <a id="aks"></a>Azure Kubernetes Service (DEVTEST & 生產)

請參閱[部署至 Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md)。

## <a name="consume-web-services"></a>取用 Web 服務

每個已部署的 web 服務都會提供 REST API, 因此您可以使用各種程式設計語言來建立用戶端應用程式。 如果您已啟用服務的金鑰驗證, 則需要提供服務金鑰做為要求標頭中的權杖。
如果您已啟用服務的權杖驗證, 則需要提供 Azure Machine Learning JWT 權杖做為要求標頭中的持有人權杖。

> [!TIP]
> 您可以在部署服務之後, 取得架構 JSON 檔。 從已部署的 web 服務 (例如`service.swagger_uri`) 使用[swagger_uri 屬性](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py#swagger-uri), 以取得本機 web 服務之 swagger 檔案的 uri。

### <a name="request-response-consumption"></a>要求-回應耗用量

以下是如何在 Python 中叫用服務的範例:
```python
import requests
import json

headers = {'Content-Type': 'application/json'}

if service.auth_enabled:
    headers['Authorization'] = 'Bearer '+service.get_keys()[0]
elif service.token_auth_enabled:
    headers['Authorization'] = 'Bearer '+service.get_token()[0]

print(headers)

test_sample = json.dumps({'data': [
    [1, 2, 3, 4, 5, 6, 7, 8, 9, 10],
    [10, 9, 8, 7, 6, 5, 4, 3, 2, 1]
]})

response = requests.post(
    service.scoring_uri, data=test_sample, headers=headers)
print(response.status_code)
print(response.elapsed)
print(response.json())
```

如需詳細資訊，請參閱[建立用戶端應用程式以使用 Webservice](how-to-consume-web-service.md)。

### <a name="web-service-schema-openapi-specification"></a>Web 服務架構 (OpenAPI 規格)

如果您在部署中使用自動產生架構, 您可以使用[swagger_uri 屬性](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py#swagger-uri)取得服務的 OpenAPI 規格位址。 例如： `print(service.swagger_uri)` 。 使用 GET 要求 (或在瀏覽器中開啟 URI) 來取出規格。

下列 JSON 檔是針對部署所產生的架構 (OpenAPI 規格) 範例:

```json
{
    "swagger": "2.0",
    "info": {
        "title": "myservice",
        "description": "API specification for the Azure Machine Learning service myservice",
        "version": "1.0"
    },
    "schemes": [
        "https"
    ],
    "consumes": [
        "application/json"
    ],
    "produces": [
        "application/json"
    ],
    "securityDefinitions": {
        "Bearer": {
            "type": "apiKey",
            "name": "Authorization",
            "in": "header",
            "description": "For example: Bearer abc123"
        }
    },
    "paths": {
        "/": {
            "get": {
                "operationId": "ServiceHealthCheck",
                "description": "Simple health check endpoint to ensure the service is up at any given point.",
                "responses": {
                    "200": {
                        "description": "If service is up and running, this response will be returned with the content 'Healthy'",
                        "schema": {
                            "type": "string"
                        },
                        "examples": {
                            "application/json": "Healthy"
                        }
                    },
                    "default": {
                        "description": "The service failed to execute due to an error.",
                        "schema": {
                            "$ref": "#/definitions/ErrorResponse"
                        }
                    }
                }
            }
        },
        "/score": {
            "post": {
                "operationId": "RunMLService",
                "description": "Run web service's model and get the prediction output",
                "security": [
                    {
                        "Bearer": []
                    }
                ],
                "parameters": [
                    {
                        "name": "serviceInputPayload",
                        "in": "body",
                        "description": "The input payload for executing the real-time machine learning service.",
                        "schema": {
                            "$ref": "#/definitions/ServiceInput"
                        }
                    }
                ],
                "responses": {
                    "200": {
                        "description": "The service processed the input correctly and provided a result prediction, if applicable.",
                        "schema": {
                            "$ref": "#/definitions/ServiceOutput"
                        }
                    },
                    "default": {
                        "description": "The service failed to execute due to an error.",
                        "schema": {
                            "$ref": "#/definitions/ErrorResponse"
                        }
                    }
                }
            }
        }
    },
    "definitions": {
        "ServiceInput": {
            "type": "object",
            "properties": {
                "data": {
                    "type": "array",
                    "items": {
                        "type": "array",
                        "items": {
                            "type": "integer",
                            "format": "int64"
                        }
                    }
                }
            },
            "example": {
                "data": [
                    [ 10, 9, 8, 7, 6, 5, 4, 3, 2, 1 ]
                ]
            }
        },
        "ServiceOutput": {
            "type": "array",
            "items": {
                "type": "number",
                "format": "double"
            },
            "example": [
                3726.995
            ]
        },
        "ErrorResponse": {
            "type": "object",
            "properties": {
                "status_code": {
                    "type": "integer",
                    "format": "int32"
                },
                "message": {
                    "type": "string"
                }
            }
        }
    }
}
```

如需規格的詳細資訊, 請參閱[OPEN API 規格](https://swagger.io/specification/)。

如需可從規格建立用戶端程式庫的公用程式, 請參閱[swagger-codegen](https://github.com/swagger-api/swagger-codegen)。

### <a id="azuremlcompute"></a>批次推斷
Azure Machine Learning 計算目標是由 Azure Machine Learning 服務建立和管理。 它們可以用於 Azure Machine Learning 管線的批次預測。

如需使用 Azure Machine Learning 計算進行批次推斷的逐步解說, 請閱讀[如何執行批次預測](how-to-run-batch-predictions.md)一文。

### <a id="iotedge"></a>IoT Edge 推斷
支援部署至 edge 目前處於預覽階段。 如需詳細資訊, 請參閱將[Azure Machine Learning 部署為 IoT Edge 模組一](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-machine-learning)文。


## <a id="update"></a>更新 web 服務

[!INCLUDE [aml-update-web-service](../../../includes/machine-learning-update-web-service.md)]

## <a name="continuous-model-deployment"></a>連續模型部署 

您可以使用[Azure DevOps](https://azure.microsoft.com/services/devops/)的 Machine Learning 延伸模組, 持續部署模型。 藉由使用 Azure DevOps 的 Machine Learning 擴充功能, 您可以在 Azure Machine Learning 服務工作區中註冊新的機器學習模型時, 觸發部署管線。 

1. 註冊[Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/get-started/pipelines-sign-up?view=azure-devops), 讓您的應用程式持續整合並傳遞至任何平臺/任何雲端。 Azure Pipelines[與 ML 管線不同](concept-ml-pipelines.md#compare)。 

1. [建立 Azure DevOps 專案。](https://docs.microsoft.com/azure/devops/organizations/projects/create-project?view=azure-devops)

1. 安裝[適用于 Azure Pipelines 的 Machine Learning 延伸](https://marketplace.visualstudio.com/items?itemName=ms-air-aiagility.vss-services-azureml&targetId=6756afbe-7032-4a36-9cb6-2771710cadc2&utm_source=vstsproduct&utm_medium=ExtHubManageList)模組 

1. 使用__服務__連線來設定與您的 Azure Machine Learning 服務工作區的服務主體連線, 以存取您的所有成品。 移至 [專案設定], 按一下 [服務連接], 然後選取 [Azure Resource Manager]。

    ![view-service-connection](media/how-to-deploy-and-where/view-service-connection.png) 

1. 將 AzureMLWorkspace 定義為__範圍層級__, 並填入後續的參數。

    ![view-azure-resource-manager](media/how-to-deploy-and-where/resource-manager-connection.png)

1. 接下來, 若要使用 Azure Pipelines 持續部署機器學習模型, 請選取 [管線] 底下的 [__發行__]。 新增 [成品], 選取 [AzureML 模型成品] 和在先前步驟中建立的服務連接。 選取要觸發部署的模型和版本。 

    ![select-AzureMLmodel-artifact](media/how-to-deploy-and-where/enable-modeltrigger-artifact.png)

1. 在您的模型成品上啟用模型觸發程式。 藉由開啟觸發程式, 每次指定的版本 (亦即, 該模型的最新版本) 是在您的工作區中註冊, 會觸發 Azure DevOps 發行管線。 

    ![enable-model-trigger](media/how-to-deploy-and-where/set-modeltrigger.png)

如需範例專案和範例, 請參閱[MLOps 存放庫](https://github.com/Microsoft/MLOps)

## <a name="clean-up-resources"></a>清除資源
若要刪除已部署的 Web 服務，請使用 `service.delete()`。
若要刪除已註冊的模型，請使用 `model.delete()`。

如需詳細資訊, 請參閱[WebService. delete ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#delete--)和[Model. delete ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#delete--)的參考檔。

## <a name="next-steps"></a>後續步驟
* [如何使用自訂 Docker 映射部署模型](how-to-deploy-custom-docker-image.md)
* [部署疑難排解](how-to-troubleshoot-deployment.md)
* [使用 SSL 保護 Azure Machine Learning Web 服務](how-to-secure-web-service.md)
* [取用部署為 Web 服務的 ML 模型](how-to-consume-web-service.md)
* [使用 Application Insights 監視您的 Azure Machine Learning 模型](how-to-enable-app-insights.md)
* [在生產環境中收集模型資料](how-to-enable-data-collection.md)

