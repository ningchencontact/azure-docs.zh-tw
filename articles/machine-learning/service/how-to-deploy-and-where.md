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
ms.date: 07/08/2019
ms.custom: seoapril2019
ms.openlocfilehash: 796118999041b2bef2d51657901e9e399578e97c
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/18/2019
ms.locfileid: "68327042"
---
# <a name="deploy-models-with-the-azure-machine-learning-service"></a>使用 Azure Machine Learning 服務部署模型

瞭解如何將您的機器學習模型部署為 Azure 雲端中的 web 服務, 或 IoT Edge 裝置。 

無論[您部署](#target)模型的位置為何, 工作流程都很類似:

1. 註冊模型。
1. 準備部署 (指定資產、使用量、計算目標)
1. 將模型部署到計算目標。
1. 測試已部署的模型, 也稱為 web 服務。

如需部署工作流程中相關概念的詳細資訊，請參閱[使用 Azure Machine Learning 服務來管理、部署及監視模型](concept-model-management-and-deployment.md)。

## <a name="prerequisites"></a>必要條件

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
  az ml model register -n sklearn_mnist  --asset-path outputs/sklearn_mnist_model.pkl  --experiment-name myexperiment
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

#### <a name="what-is-getmodelpath"></a>什麼是 get_model_path？

當您註冊模型時, 您會提供用來在登錄中管理模型的模型名稱。 您會將此名稱與[模型搭配使用。 get _model_path ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#get-model-path-model-name--version-none---workspace-none-)可在本機檔案系統上取出模型檔案的路徑。 如果您註冊資料夾或檔案集合, 此 API 會傳回包含這些檔案之目錄的路徑。

當您註冊模型時, 您可以為它指定一個名稱, 它會對應至模型的放置位置, 不論是在本機或在服務部署期間。

下列範例會傳回名`sklearn_mnist_model.pkl`為之單一檔案的路徑 (已使用名稱`sklearn_mnist`註冊):

```python
model_path = Model.get_model_path('sklearn_mnist')
``` 

#### <a name="optional-automatic-swagger-schema-generation"></a>選擇性自動產生 Swagger 架構

若要自動產生 web 服務的架構, 請在其中一個已定義型別物件的函式中提供輸入和/或輸出的範例, 並使用型別和範例來自動建立架構。 Azure Machine Learning 服務接著會在部署期間建立 web 服務的[OpenAPI](https://swagger.io/docs/specification/about/) (Swagger) 規格。

目前支援下列類型:

* `pandas`
* `numpy`
* `pyspark`
* 標準 Python 物件

若要使用架構產生, 請`inference-schema`在您的 conda 環境檔案中包含封裝。 下列範例會使用`[numpy-support]` , 因為輸入腳本會使用 numpy 參數類型: 

#### <a name="example-dependencies-file"></a>範例相依性檔案
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

> [!TIP]
> 部署服務之後, 請使用`swagger_uri`屬性來取出架構 JSON 檔。

#### <a name="example-entry-script"></a>範例專案腳本

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

input_sample = np.array([[10,9,8,7,6,5,4,3,2,1]])
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

#### <a name="example-script-with-dictionary-input-support-consumption-from-power-bi"></a>具有字典輸入的範例腳本 (支援從 Power BI 耗用量)

下列範例示範如何使用資料框架, 將輸入資料定義為 < 索引鍵: 值 > 字典。 此方法支援從 Power BI 取用已部署的 web 服務 ([深入瞭解如何從 Power BI 使用 web 服務](https://docs.microsoft.com/power-bi/service-machine-learning-integration)):

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
    model_path = Model.get_model_path('model_name')   # replace model_name with your actual model name, if needed
    # deserialize the model file back into a sklearn model
    model = joblib.load(model_path)

input_sample = pd.DataFrame(data=[{
              "input_name_1": 5.1,         # This is a decimal type sample. Use the data type that reflects this column in your data
              "input_name_2": "value2",    # This is a string type sample. Use the data type that reflects this column in your data
              "input_name_3": 3            # This is a integer type sample. Use the data type that reflects this column in your data
            }])

output_sample = np.array([0])              # This is a integer type sample. Use the data type that reflects the expected result

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
inference_config = InferenceConfig(runtime= "python",
                                   entry_script="x/y/score.py",
                                   conda_file="env/myenv.yml")
```

如需詳細資訊, 請參閱[InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py)類別參考。

如需有關使用自訂 Docker 映射搭配推斷設定的詳細資訊, 請參閱[如何使用自訂 docker 映射部署模型](how-to-deploy-custom-docker-image.md)。

### <a name="cli-example-of-inferenceconfig"></a>InferenceConfig 的 CLI 範例

下列 JSON 檔是用於機器學習 CLI 的範例推斷設定:

```JSON
{
   "entryScript": "x/y/score.py",
   "runtime": "python",
   "condaFile": "env/myenv.yml",
   "sourceDirectory":"C:/abc",
}
```

下列實體在此檔案中是有效的:

* __entryScript__:本機檔案的路徑, 其中包含要為映射執行的程式碼。
* __運行__時間:要用於影像的執行時間。 目前支援的執行時間為 ' spark-.py ' 和 ' python '。
* __condaFile__(選擇性):本機檔案的路徑, 其中包含要用於影像的 conda 環境定義。
* __extraDockerFileSteps__(選擇性):本機檔案的路徑, 其中包含設定映射時要執行的其他 Docker 步驟。
* __sourceDirectory__(選擇性):資料夾的路徑, 其中包含要建立映射的所有檔案。
* __enableGpu__(選擇性):是否要在映射中啟用 GPU 支援。 GPU 映射必須用於 Microsoft Azure 服務, 例如 Azure 容器實例、Azure Machine Learning 計算、Azure 虛擬機器和 Azure Kubernetes Service。 預設值為 False。
* __baseImage__(選擇性):要當做基底映射使用的自訂映射。 如果未指定基底映射, 則會根據指定的執行時間參數來使用基底映射。
* __baseImageRegistry__(選擇性):包含基底映射的映射登錄。
* __cudaVersion__(選擇性):要為需要 GPU 支援的映射安裝的 CUDA 版本。 GPU 映射必須用於 Microsoft Azure 服務, 例如 Azure 容器實例、Azure Machine Learning 計算、Azure 虛擬機器和 Azure Kubernetes Service。 支援的版本為9.0、9.1 和10.0。 如果設定了 ' enable_gpu ', 則預設為 ' 9.1 '。

這些實體會對應至[InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py)類別的參數。

三個下列命令示範如何使用 CLI 部署模型:

```azurecli-interactive
az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json
```

在此範例中, 設定包含下列專案:

* 包含推斷所需資產的目錄
* 此模型需要 Python
* [輸入腳本](#script), 用來處理傳送至已部署服務的 web 要求
* 描述推斷所需之 Python 套件的 conda 檔案

如需有關使用自訂 Docker 映射搭配推斷設定的詳細資訊, 請參閱[如何使用自訂 docker 映射部署模型](how-to-deploy-custom-docker-image.md)。

### <a name="3-define-your-deployment-configuration"></a>3.定義您的部署設定

部署之前, 您必須先定義部署設定。 部署設定適用于將裝載 web 服務的計算目標。 例如, 在本機部署時, 您必須指定服務接受要求的埠。

您可能也需要建立計算資源。 例如, 如果您還沒有與工作區相關聯的 Azure Kubernetes Service。

下表提供建立每個計算目標之部署設定的範例:

| 計算目標 | 部署設定範例 |
| ----- | ----- |
| 本機 | `deployment_config = LocalWebservice.deploy_configuration(port=8890)` |
| Azure 容器執行個體 | `deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |
| Azure Kubernetes Service | `deployment_config = AksWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |

下列各節示範如何建立部署設定, 然後使用它來部署 web 服務。

### <a name="optional-profile-your-model"></a>選用：分析模型

在將您的模型部署為服務之前, 您可以使用 SDK 或 CLI 進行分析, 以判斷最佳的 CPU 和記憶體需求。  模型分析結果會以物件的`Run`形式發出。 [模型設定檔架構的完整詳細資料可以在 API 檔中找到](https://docs.microsoft.com/python/api/azureml-core/azureml.core.profile.modelprofile?view=azure-ml-py)。

深入瞭解[如何使用 SDK 來分析您的模型](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#profile-workspace--profile-name--models--inference-config--input-data-)。

若要使用 CLI 來分析您的模型, 請使用[az ml model profile](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-profile)。

## <a name="deploy-to-target"></a>部署至目標

### <a id="local"></a>本機部署

若要在本機部署, 您必須在本機電腦上**安裝 Docker** 。

+ **使用 SDK**

  ```python
  deployment_config = LocalWebservice.deploy_configuration(port=8890)
  service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config)
  service.wait_for_deployment(show_output = True)
  print(service.state)
  ```

+ **使用 CLI**

    若要使用 CLI 進行部署, 請使用下列命令。 以`mymodel:1`已註冊模型的名稱和版本取代:

  ```azurecli-interactive
  az ml model deploy -m mymodel:1 -ic inferenceconfig.json -dc deploymentconfig.json
  ```

    `deploymentconfig.json`檔中的專案會對應至 LocalWebservice 的參數[。 deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservicedeploymentconfiguration?view=azure-ml-py)。 下表描述 JSON 檔中的實體與方法的參數之間的對應:

    | JSON 實體 | 方法參數 | 描述 |
    | ----- | ----- | ----- |
    | `computeType` | NA | 計算目標。 若為本機, 此值必須`local`是。 |
    | `port` | `port` | 要在其上公開服務之 HTTP 端點的本機埠。 |

    下列 JSON 是與 CLI 搭配使用的範例部署設定:

    ```json
    {
        "computeType": "local",
        "port": 32267
    }
    ```

### <a id="aci"></a>Azure 容器實例 (DEVTEST)

如果符合下列一或多個條件，請使用 Azure 容器執行個體來將您的模型部署為 Web 服務：
- 您需要快速部署及驗證模型。
- 您正在測試處於開發狀態的模型。 

若要查看 ACI 的配額和區域可用性, 請參閱[Azure 容器實例的配額和區域可用性](https://docs.microsoft.com/azure/container-instances/container-instances-quotas)一文。

+ **使用 SDK**

  ```python
  deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)
  service = Model.deploy(ws, "aciservice", [model], inference_config, deployment_config)
  service.wait_for_deployment(show_output = True)
  print(service.state)
  ```

+ **使用 CLI**

    若要使用 CLI 進行部署, 請使用下列命令。 將`mymodel:1`取代為已註冊模型的名稱和版本。 以`myservice`要提供此服務的名稱取代:

    ```azurecli-interactive
    az ml model deploy -m mymodel:1 -n myservice -ic inferenceconfig.json -dc deploymentconfig.json
    ```

    `deploymentconfig.json`檔中的專案會對應至 AciWebservice 的參數[。 deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aci.aciservicedeploymentconfiguration?view=azure-ml-py)。 下表描述 JSON 檔中的實體與方法的參數之間的對應:

    | JSON 實體 | 方法參數 | 描述 |
    | ----- | ----- | ----- |
    | `computeType` | NA | 計算目標。 若為 ACI, 此值必須`ACI`是。 |
    | `containerResourceRequirements` | NA | 包含配置給容器的 CPU 和記憶體的設定元素。 |
    | &emsp;&emsp;`cpu` | `cpu_cores` | 要為此 web 服務配置的 CPU 核心數目。 設置`0.1` |
    | &emsp;&emsp;`memoryInGB` | `memory_gb` | 要為此 web 服務配置的記憶體數量 (以 GB 為單位)。 預設`0.5` |
    | `location` | `location` | 要部署此 Webservice 的 Azure 區域。 如果未指定, 則會使用工作區位置。 如需可用區域的詳細資訊, 請參閱:[ACI 區域](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=container-instances) |
    | `authEnabled` | `auth_enabled` | 是否要啟用此 Webservice 的驗證。 預設為 False |
    | `sslEnabled` | `ssl_enabled` | 是否要為此 Webservice 啟用 SSL。 預設值為 False。 |
    | `appInsightsEnabled` | `enable_app_insights` | 是否要為此 Webservice 啟用 AppInsights。 預設為 False |
    | `sslCertificate` | `ssl_cert_pem_file` | 若已啟用 SSL, 則需要憑證檔案 |
    | `sslKey` | `ssl_key_pem_file` | 啟用 SSL 時所需的金鑰檔 |
    | `cname` | `ssl_cname` | 如果 SSL 已啟用, 則為的 cname |
    | `dnsNameLabel` | `dns_name_label` | 評分端點的 dns 名稱標籤。 如果未指定, 則會產生評分端點的唯一 dns 名稱標籤。 |

    下列 JSON 是與 CLI 搭配使用的範例部署設定:

    ```json
    {
        "computeType": "aci",
        "containerResourceRequirements":
        {
            "cpu": 0.5,
            "memoryInGB": 1.0
        },
        "authEnabled": true,
        "sslEnabled": false,
        "appInsightsEnabled": false
    }
    ```

+ **使用 VS Code**

  若要[使用 VS Code 部署模型](how-to-vscode-tools.md#deploy-and-manage-models), 您不需要事先建立 aci 容器來進行測試, 因為 ACI 容器是即時建立的。

如需詳細資訊，請參閱 [AciWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py) \(英文\) 和 [Webservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice?view=azure-ml-py) \(英文\) 類別的參考文件。

### <a id="aks"></a>Azure Kubernetes Service (DEVTEST & 生產)

您可以使用現有的 AKS 叢集，或使用 Azure Machine Learning SDK、CLI 或 Azure 入口網站建立新的叢集。

<a id="deploy-aks"></a>

如果您已附加 AKS 叢集, 您可以部署到該叢集。 如果您尚未建立或連接 AKS 叢集, 請依照程式來<a href="#create-attach-aks">建立新的 AKS</a>叢集。

+ **使用 SDK**

  ```python
  aks_target = AksCompute(ws,"myaks")
  # If deploying to a cluster configured for dev/test, ensure that it was created with enough
  # cores and memory to handle this deployment configuration. Note that memory is also used by
  # things such as dependencies and AML components.
  deployment_config = AksWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)
  service = Model.deploy(ws, "aksservice", [model], inference_config, deployment_config, aks_target)
  service.wait_for_deployment(show_output = True)
  print(service.state)
  print(service.get_logs())
  ```

+ **使用 CLI**

    若要使用 CLI 進行部署, 請使用下列命令。 將`myaks`取代為 AKS 計算目標的名稱。 將`mymodel:1`取代為已註冊模型的名稱和版本。 以`myservice`要提供此服務的名稱取代:

  ```azurecli-interactive
  az ml model deploy -ct myaks -m mymodel:1 -n myservice -ic inferenceconfig.json -dc deploymentconfig.json
  ```

    `deploymentconfig.json`檔中的專案會對應至 AksWebservice 的參數[。 deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aks.aksservicedeploymentconfiguration?view=azure-ml-py)。 下表描述 JSON 檔中的實體與方法的參數之間的對應:

    | JSON 實體 | 方法參數 | 描述 |
    | ----- | ----- | ----- |
    | `computeType` | NA | 計算目標。 若為 AKS, 此值必須`aks`是。 |
    | `autoScaler` | NA | 包含自動調整的設定元素。 請參閱自動調整程式資料表。 |
    | &emsp;&emsp;`autoscaleEnabled` | `autoscale_enabled` | 是否要啟用 web 服務的自動調整。 如果`numReplicas`  = 為,`True`則為,`False`否則為。 `0` |
    | &emsp;&emsp;`minReplicas` | `autoscale_min_replicas` | 自動調整此 web 服務時所要使用的容器數目下限。 預設值`1`:。 |
    | &emsp;&emsp;`maxReplicas` | `autoscale_max_replicas` | 自動調整此 web 服務時所要使用的容器數目上限。 預設值`10`:。 |
    | &emsp;&emsp;`refreshPeriodInSeconds` | `autoscale_refresh_seconds` | 自動調整程式嘗試調整此 web 服務的頻率。 預設值`1`:。 |
    | &emsp;&emsp;`targetUtilization` | `autoscale_target_utilization` | 自動調整程式應該嘗試為此 web 服務維護的目標使用率 (以百分比100為單位)。 預設值`70`:。 |
    | `dataCollection` | NA | 包含資料收集的設定元素。 |
    | &emsp;&emsp;`storageEnabled` | `collect_model_data` | 是否要啟用 web 服務的模型資料收集。 預設值`False`:。 |
    | `authEnabled` | `auth_enabled` | 是否要啟用 web 服務的驗證。 預設值`True`:。 |
    | `containerResourceRequirements` | NA | 包含配置給容器的 CPU 和記憶體的設定元素。 |
    | &emsp;&emsp;`cpu` | `cpu_cores` | 要為此 web 服務配置的 CPU 核心數目。 設置`0.1` |
    | &emsp;&emsp;`memoryInGB` | `memory_gb` | 要為此 web 服務配置的記憶體數量 (以 GB 為單位)。 預設`0.5` |
    | `appInsightsEnabled` | `enable_app_insights` | 是否要啟用 web 服務的 Application Insights 記錄。 預設值`False`:。 |
    | `scoringTimeoutMs` | `scoring_timeout_ms` | 對 web 服務評分呼叫強制執行的時間。 預設值`60000`:。 |
    | `maxConcurrentRequestsPerContainer` | `replica_max_concurrent_requests` | 此 web 服務每個節點的並行要求數目上限。 預設值`1`:。 |
    | `maxQueueWaitMs` | `max_request_wait_time` | 在傳回503錯誤之前, 要求會停留在三個佇列中的時間上限 (以毫秒為單位)。 預設值`500`:。 |
    | `numReplicas` | `num_replicas` | 要為此 web 服務配置的容器數目。 沒有預設值。 如果未設定此參數, 則預設會啟用自動調整程式。 |
    | `keys` | NA | 包含索引鍵的設定元素。 |
    | &emsp;&emsp;`primaryKey` | `primary_key` | 要用於此 Webservice 的主要驗證金鑰 |
    | &emsp;&emsp;`secondaryKey` | `secondary_key` | 要用於此 Webservice 的次要驗證金鑰 |
    | `gpuCores` | `gpu_cores` | 要為此 Webservice 配置的 GPU 核心數目。 預設值為 1。 |
    | `livenessProbeRequirements` | NA | 包含活動探查需求的設定元素。 |
    | &emsp;&emsp;`periodSeconds` | `period_seconds` | 執行活動探查的頻率 (以秒為單位)。 預設為10秒。 最小值為1。 |
    | &emsp;&emsp;`initialDelaySeconds` | `initial_delay_seconds` | 在起始活動探查之前, 容器啟動後的秒數。 預設為310 |
    | &emsp;&emsp;`timeoutSeconds` | `timeout_seconds` | 活動探查超時之前的秒數。預設為2秒。 最小值為1 |
    | &emsp;&emsp;`successThreshold` | `success_threshold` | 成功之後, 成功將活動探查視為成功的最小連續成功。 預設值為 1。 最小值為1。 |
    | &emsp;&emsp;`failureThreshold` | `failure_threshold` | 當 Pod 啟動且活動探查失敗時, Kubernetes 會先嘗試 failureThreshold 時間再放棄。 預設值為3。 最小值為1。 |
    | `namespace` | `namespace` | Webservice 部署所在的 Kubernetes 命名空間。 最多63小寫英數位元 (' a'-'z-a'-'z '、' 0 '-' 9 ') 和連字號 ('-') 字元。 第一個和最後一個字元不可以是連字號。 |

    下列 JSON 是與 CLI 搭配使用的範例部署設定:

    ```json
    {
        "computeType": "aks",
        "autoScaler":
        {
            "autoscaleEnabled": true,
            "minReplicas": 1,
            "maxReplicas": 3,
            "refreshPeriodInSeconds": 1,
            "targetUtilization": 70
        },
        "dataCollection":
        {
            "storageEnabled": true
        },
        "authEnabled": true,
        "containerResourceRequirements":
        {
            "cpu": 0.5,
            "memoryInGB": 1.0
        }
    }
    ```

+ **使用 VS Code**

  您也可以透過[VS Code 擴充功能部署至 AKS](how-to-vscode-tools.md#deploy-and-manage-models), 但您必須事先設定 AKS 叢集。

若要深入瞭解 AKS 部署和自動調整, 請參閱[AksWebservice. deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.akswebservice)參考。

#### 建立新的 AKS 叢集<a id="create-attach-aks"></a>
**估計時間**：約 20 分鐘。

建立或附加 AKS 叢集是工作區的一次性程式。 您可以重複使用此叢集進行多個部署。 如果您刪除叢集或包含該叢集的資源群組, 則必須在下次需要部署時建立新的叢集。 您可以將多個 AKS 叢集附加至您的工作區。

如果您想要建立用於開發、驗證和測試的 AKS 叢集, 請在使用`cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST` [`provisioning_configuration()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py)時設定。 使用此設定建立的叢集只會有一個節點。

> [!IMPORTANT]
> 設定`cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST`會建立不適合用來處理生產流量的 AKS 叢集。 推斷時間可能會比針對生產環境建立的叢集還長。 開發/測試叢集也不保證容錯功能。
>
> 我們建議針對開發/測試建立的叢集至少使用兩個虛擬 Cpu。

下列範例示範如何建立新的 Azure Kubernetes Service 叢集:

```python
from azureml.core.compute import AksCompute, ComputeTarget

# Use the default configuration (you can also provide parameters to customize this).
# For example, to create a dev/test cluster, use:
# prov_config = AksCompute.provisioning_configuration(cluster_purpose = AksComputee.ClusterPurpose.DEV_TEST)
prov_config = AksCompute.provisioning_configuration()

aks_name = 'myaks'
# Create the cluster
aks_target = ComputeTarget.create(workspace = ws,
                                    name = aks_name,
                                    provisioning_configuration = prov_config)

# Wait for the create process to complete
aks_target.wait_for_completion(show_output = True)
```

如需有關在 Azure Machine Learning SDK 之外建立 AKS 叢集的詳細資訊, 請參閱下列文章:
* [建立 AKS 叢集](https://docs.microsoft.com/cli/azure/aks?toc=%2Fazure%2Faks%2FTOC.json&bc=%2Fazure%2Fbread%2Ftoc.json&view=azure-cli-latest#az-aks-create)
* [建立 AKS 叢集 (入口網站)](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough-portal?view=azure-cli-latest)

如需參數的`cluster_purpose`詳細資訊, 請參閱[AksCompute. ClusterPurpose](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.akscompute.clusterpurpose?view=azure-ml-py)參考。

> [!IMPORTANT]
> 針對 [`provisioning_configuration()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py)，如果您要為 agent_count 和 vm_size 挑選自訂值，則必須先確定 agent_count 乘以 vm_size 會大於或等於 12 個虛擬 CPU。 例如，如果您使用的 vm_size 為 "Standard_D3_v2" (其具有 4 個虛擬 CPU)，則您應該挑選等於或大於 3 的 agent_count。
>
> Azure Machine Learning SDK 不提供調整 AKS 叢集的支援。 若要調整叢集中的節點, 請在 Azure 入口網站中使用適用于 AKS 叢集的 UI。 您只能變更節點計數, 而不是叢集的 VM 大小。

#### <a name="attach-an-existing-aks-cluster"></a>附加現有的 AKS 叢集
**估計時間:** 大約5分鐘。

如果您的 Azure 訂用帳戶中已經有 AKS 叢集, 而且它是1.12 版。 # #, 您可以使用它來部署您的映射。

> [!WARNING]
> 將 AKS 叢集附加至工作區時, 您可以藉由設定`cluster_purpose`參數來定義使用叢集的方式。
>
> 如果您未設定`cluster_purpose`參數或設定`cluster_purpose = AksCompute.ClusterPurpose.FAST_PROD`, 則叢集必須至少有12個可用的虛擬 cpu。
>
> 如果您設定`cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST`了, 叢集就不需要有12個虛擬 cpu。 不過, 針對「開發/測試」設定的叢集將不適合用于生產層級的流量, 而且可能會增加推斷時間。

下列程式碼示範如何將現有的 AKS 1.12. # # 叢集附加至您的工作區:

```python
from azureml.core.compute import AksCompute, ComputeTarget
# Set the resource group that contains the AKS cluster and the cluster name
resource_group = 'myresourcegroup'
cluster_name = 'mycluster'

# Attach the cluster to your workgroup. If the cluster has less than 12 virtual CPUs, use the following instead:
# attach_config = AksCompute.attach_configuration(resource_group = resource_group,
#                                         cluster_name = cluster_name,
#                                         cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST)
attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                         cluster_name = cluster_name)
aks_target = ComputeTarget.attach(ws, 'mycompute', attach_config)
```

如需有關的`attack_configuration()`詳細資訊, 請參閱[AksCompute. attach_configuration ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)參考。

如需參數的`cluster_purpose`詳細資訊, 請參閱[AksCompute. ClusterPurpose](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.akscompute.clusterpurpose?view=azure-ml-py)參考。

## <a name="consume-web-services"></a>取用 Web 服務

每個已部署的 web 服務都會提供 REST API, 因此您可以使用各種程式設計語言來建立用戶端應用程式。 如果您已啟用服務的驗證, 則必須提供服務金鑰做為要求標頭中的權杖。

### <a name="request-response-consumption"></a>要求-回應耗用量

以下是如何在 Python 中叫用服務的範例:
```python
import requests
import json

headers = {'Content-Type':'application/json'}

if service.auth_enabled:
    headers['Authorization'] = 'Bearer '+service.get_keys()[0]

print(headers)
    
test_sample = json.dumps({'data': [
    [1,2,3,4,5,6,7,8,9,10], 
    [10,9,8,7,6,5,4,3,2,1]
]})

response = requests.post(service.scoring_uri, data=test_sample, headers=headers)
print(response.status_code)
print(response.elapsed)
print(response.json())
```

如需詳細資訊，請參閱[建立用戶端應用程式以使用 Webservice](how-to-consume-web-service.md)。


### <a id="azuremlcompute"></a>批次推斷
Azure Machine Learning 計算目標是由 Azure Machine Learning 服務建立和管理。 它們可以用於 Azure Machine Learning 管線的批次預測。

如需使用 Azure Machine Learning 計算進行批次推斷的逐步解說, 請閱讀[如何執行批次預測](how-to-run-batch-predictions.md)一文。

### <a id="iotedge"></a>IoT Edge 推斷
支援部署至 edge 目前處於預覽階段。 如需詳細資訊, 請參閱將[Azure Machine Learning 部署為 IoT Edge 模組一](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-machine-learning)文。


## <a id="update"></a>更新 web 服務

當您建立新的模型時, 您必須手動更新您想要使用新模型的每個服務。 若要更新 Web 服務，請使用 `update` 方法。 下列程式碼示範如何將 web 服務更新為使用新的模型:

```python
from azureml.core.webservice import Webservice
from azureml.core.model import Model

# register new model
new_model = Model.register(model_path = "outputs/sklearn_mnist_model.pkl",
                       model_name = "sklearn_mnist",
                       tags = {"key": "0.1"},
                       description = "test",
                       workspace = ws)

service_name = 'myservice'
# Retrieve existing service
service = Webservice(name = service_name, workspace = ws)

# Update to new model(s)
service.update(models = [new_model])
print(service.state)
print(service.get_logs())
```

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

