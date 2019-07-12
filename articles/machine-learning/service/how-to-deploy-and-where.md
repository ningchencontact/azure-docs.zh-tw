---
title: 如何及在何處部署模型
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
ms.openlocfilehash: fb23e61142a639420d74c08e5a9a41324acab18b
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2019
ms.locfileid: "67706290"
---
# <a name="deploy-models-with-the-azure-machine-learning-service"></a>使用 Azure Machine Learning 服務部署模型

了解如何部署您的機器學習模型作為 web 服務在 Azure 雲端中，或到 IoT Edge 裝置。 

工作流程很類似，不論[您在其中部署](#target)模型：

1. 註冊模型。
1. 準備部署 （指定資產、 使用方式，計算目標）
1. 將模型部署到計算目標。
1. 測試已部署的模型，也稱為 web 服務。

如需部署工作流程中相關概念的詳細資訊，請參閱[使用 Azure Machine Learning 服務來管理、部署及監視模型](concept-model-management-and-deployment.md)。

## <a name="prerequisites"></a>先決條件

- 模型。 如果您不需要定型的模型，您可以使用模型 & 相依性檔案中提供[本教學課程](https://aka.ms/azml-deploy-cloud)。

- [機器學習服務的 Azure CLI 擴充功能](reference-azure-machine-learning-cli.md)， [Azure Machine Learning Python SDK](https://aka.ms/aml-sdk)，或有[Azure 機器學習 Visual Studio Code 擴充功能](how-to-vscode-tools.md)。

## <a id="registermodel"></a> 註冊您的模型

組成模型的一或多個檔案已註冊的模型邏輯容器。 比方說，如果您有多個檔案中儲存的模型，您可以為工作區中的單一模型加以註冊。 註冊之後，您可以下載或部署已註冊的模型然後接收已註冊的所有檔案。

機器學習服務模型會在您的 Azure Machine Learning 工作區中註冊。 模型可以來自 Azure 機器學習服務，或可以來自其他地方。 下列範例示範如何註冊模型，以從檔案：

### <a name="register-a-model-from-an-experiment-run"></a>註冊模型的實驗執行

+ **使用 SDK 的 Scikit-learn 範例**
  ```python
  model = run.register_model(model_name='sklearn_mnist', model_path='outputs/sklearn_mnist_model.pkl')
  print(model.name, model.id, model.version, sep='\t')
  ```

  > [!TIP]
  > 若要註冊模型中包含多個檔案，設定`model_path`包含檔案的目錄。

+ **使用 CLI**

  ```azurecli-interactive
  az ml model register -n sklearn_mnist  --asset-path outputs/sklearn_mnist_model.pkl  --experiment-name myexperiment
  ```

  > [!TIP]
  > 若要註冊模型中包含多個檔案，設定`--asset-path`包含檔案的目錄。

+ **使用 VS Code**

  註冊模型使用的任何模型檔案或資料夾[VS Code](how-to-vscode-tools.md#deploy-and-manage-models)延伸模組。

### <a name="register-an-externally-created-model"></a>註冊外部建立的模型

[!INCLUDE [trusted models](../../../includes/machine-learning-service-trusted-model.md)]

您可以藉由提供註冊的外部建立的模型**的本機路徑**模型。 您可以提供一個資料夾或單一檔案。

+ **使用 Python SDK 的 ONNX 範例：**
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
  > 若要註冊模型中包含多個檔案，設定`model_path`包含檔案的目錄。

+ **使用 CLI**
  ```azurecli-interactive
  az ml model register -n onnx_mnist -p mnist/model.onnx
  ```

  > [!TIP]
  > 若要註冊模型中包含多個檔案，設定`-p`包含檔案的目錄。

**估計時間**：大約 10 秒。

如需詳細資訊，請參閱 [Model 類別](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py) \(英文\) 的參考文件。

如需有關使用模型的詳細資訊訓練外的 Azure Machine Learning 服務，請參閱[如何將現有的模型部署](how-to-deploy-existing-model.md)。

<a name="target"></a>

## <a name="choose-a-compute-target"></a>選擇計算目標

下列計算目標，或計算資源，可用來裝載您的 web 服務部署。 

[!INCLUDE [aml-compute-target-deploy](../../../includes/aml-compute-target-deploy.md)]

## <a name="prepare-to-deploy"></a>準備部署

若要部署為 web 服務，您必須建立推斷組態 (`InferenceConfig`) 和部署組態。 推斷，或模型計分，是階段已部署的模型用於預測，最常在實際執行資料的位置。 在推斷組態中，您可以指定指令碼和您的模型所需的相依性。 部署組態中，您會指定如何計算目標上提供模型的詳細資料。


### <a id="script"></a> 1.定義您的項目指令碼和相依性

項目指令碼會接收到已部署的 web 服務，送出的資料，並將它傳遞至模型。 然後，它會採用模型傳回的回應，並將該回應傳回至用戶端。 **指令碼是您的模型特有**; 它必須了解模型所預期，並傳回的資料。

指令碼包含兩個函式，載入及執行模型：

* `init()`:此函式通常會將模型載入到全域物件。 只要您的 web 服務的 Docker 容器啟動時，會執行此函式。

* `run(input_data)`:此函式會使用模型，依據輸入資料來預測值。 執行的輸入和輸出通常使用 JSON 進行序列化和還原序列化。 您也可以使用原始的二進位資料。 您可以先轉換資料，再將資料傳送給模型或傳回用戶端。

#### <a name="what-is-getmodelpath"></a>什麼是 get_model_path？

當您註冊模型時，您會提供用來管理在登錄中的模型的模型名稱。 您使用這個名稱與[Model.get_model_path()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#get-model-path-model-name--version-none---workspace-none-)以擷取本機檔案系統上的模型檔案的路徑。 如果您註冊資料夾或檔案的集合，此 API 會傳回路徑來包含這些檔案的目錄。

當您註冊模型時，您指定的名稱，對應至模型放置的位置，在本機或在服務部署期間。

下列範例會以單一檔案呼叫傳回路徑`sklearn_mnist_model.pkl`(其已註冊名稱為`sklearn_mnist`):

```python
model_path = Model.get_model_path('sklearn_mnist')
``` 

#### <a name="optional-automatic-swagger-schema-generation"></a>（選擇性）自動產生 Swagger 結構描述

自動產生的結構描述，您的 web 服務、 提供輸入的範例和/或輸出中定義的型別物件的型別和範例的其中一個建構函式用來自動建立結構描述。 接著會建立 azure Machine Learning 服務[OpenAPI](https://swagger.io/docs/specification/about/) (Swagger) 規格的 web 服務部署期間。

目前支援下列類型：

* `pandas`
* `numpy`
* `pyspark`
* 標準的 Python 物件

若要使用結構描述產生，包括`inference-schema`conda 環境檔案中的封裝。 下列範例會使用`[numpy-support]`由於進入指令碼會使用 numpy 參數類型： 

#### <a name="example-dependencies-file"></a>範例相依性檔案
下列 YAML 是推斷的 Conda 相依性檔案的範例。

```YAML
name: project_environment
dependencies:
  - python=3.6.2
  - pip:
    - azureml-defaults
    - scikit-learn==0.20.0
    - inference-schema[numpy-support]
```

如果您想要使用自動結構描述產生，您的項目指令碼**必須**匯入`inference-schema`封裝。 

定義輸入和輸出中的範例格式`input_sample`和`output_sample`代表 web 服務的要求和回應格式的變數。 使用這些範例中的輸入和輸出函式的裝飾項目上`run()`函式。 Scikit-learn-了解下列範例會使用結構描述產生。

> [!TIP]
> 在部署服務之後, 使用`swagger_uri`屬性，以擷取結構描述 JSON 文件。

#### <a name="example-entry-script"></a>範例項目指令碼

下列範例示範如何接受及傳回的 JSON 資料：

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

#### <a name="example-script-with-dictionary-input-support-consumption-from-power-bi"></a>與字典輸入 （從 Power BI 支援耗用量） 的範例指令碼

下列範例示範如何定義輸入的資料做為 < 索引鍵： 值 > 使用資料框架的字典。 使用 Power BI 中的已部署的 web 服務支援這個方法 ([進一步了解如何使用 Power BI 中的 web 服務](https://docs.microsoft.com/power-bi/service-machine-learning-integration)):

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
如需詳細的範例指令碼，請參閱下列範例：

* Pytorch: [https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-pytorch](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-pytorch)
* TensorFlow: [https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-tensorflow](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-tensorflow)
* Keras: [https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras)
* ONNX: [https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/onnx/](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/onnx/)
* 計分二進位資料：[如何使用 web 服務](how-to-consume-web-service.md)

### <a name="2-define-your-inferenceconfig"></a>2.定義您 InferenceConfig

推斷組態會描述如何設定模型進行預測。 下列範例示範如何建立推斷設定。 這個組態會指定執行階段、 項目指令碼，以及 （選擇性） conda 環境檔案：

```python
inference_config = InferenceConfig(runtime= "python",
                                   entry_script="x/y/score.py",
                                   conda_file="env/myenv.yml")
```

如需詳細資訊，請參閱 < [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py)類別參考。

如需推斷組態搭配使用自訂的 Docker 映像的資訊，請參閱[如何部署模型使用自訂的 Docker 映像](how-to-deploy-custom-docker-image.md)。

### <a name="cli-example-of-inferenceconfig"></a>InferenceConfig 的 CLI 範例

下列 JSON 文件是用於機器學習 CLI 的範例推斷組態：

```JSON
{
   "entryScript": "x/y/score.py",
   "runtime": "python",
   "condaFile": "env/myenv.yml",
   "sourceDirectory":"C:/abc",
}
```

在這個檔案是有效的下列實體：

* __entryScript__:包含執行映像的程式碼的本機檔案路徑。
* __執行階段__:要用於映像的執行階段。 目前支援的執行階段是 ' spark py' 與 'python'。
* __condaFile__ （選擇性）：包含要用於映像的 conda 環境定義的本機檔案路徑。
* __extraDockerFileSteps__ （選擇性）：包含其他的 Docker 步驟，以設定 映像時所執行的本機檔案路徑。
* __sourceDirectory__ （選擇性）：包含建立映像的所有檔案的資料夾路徑。
* __enableGpu__ （選擇性）：指出要啟用 GPU 支援映像中。 GPU 映像必須使用 Microsoft Azure 服務，例如 Azure Container Instances、 Azure Machine Learning 計算、 Azure 虛擬機器和 Azure Kubernetes 服務。 預設為 False。
* __baseImage__ （選擇性）：要做為基底映像的自訂映像。 如果不指定了任何基底映像，則基底映像會使用基礎的指定執行階段參數。
* __baseImageRegistry__ （選擇性）：包含基底映像的映像登錄。
* __cudaVersion__ （選擇性）：CUDA 安裝需要 GPU 支援的映像的版本。 GPU 映像必須使用 Microsoft Azure 服務，例如 Azure Container Instances、 Azure Machine Learning 計算、 Azure 虛擬機器和 Azure Kubernetes 服務。 支援的版本為 9.0、 9.1 和 10.0。 如果設定 'enable_gpu'，則預設為 '9.1'。

這些實體對應至參數[InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py)類別。

下列命令示範如何部署模型，使用 CLI:

```azurecli-interactive
az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json
```

在此範例中，組態會包含下列項目：

* 包含推斷所需的資產目錄
* 此模型需要使用 Python
* [項目指令碼](#script)，用以處理 web 要求傳送至已部署的服務
* 描述推斷所需的 Python 套件的 conda 檔案

如需推斷組態搭配使用自訂的 Docker 映像的資訊，請參閱[如何部署模型使用自訂的 Docker 映像](how-to-deploy-custom-docker-image.md)。

### <a name="3-define-your-deployment-configuration"></a>3.定義您的部署組態

在部署之前，您必須定義部署組態。 部署組態是針對將裝載 web 服務的計算目標。 例如，在本機部署時，您必須指定服務可以接受要求的連接埠。

此外，您可能也需要建立計算資源。 比方說，如果您還沒這樣做有 Azure Kubernetes 服務相關聯工作區。

下表提供建立每個計算目標的部署組態的範例：

| 計算目標 | 部署組態範例 |
| ----- | ----- |
| 本機 | `deployment_config = LocalWebservice.deploy_configuration(port=8890)` |
| Azure 容器執行個體 | `deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |
| Azure Kubernetes Service | `deployment_config = AksWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |

下列各節示範如何建立部署組態，並接著使用它來部署 web 服務。

### <a name="optional-profile-your-model"></a>選用：分析您的模型
在部署之前即服務模型，您可以分析以判斷最佳的 CPU 和記憶體需求，使用 SDK 或 CLI。  模型的分析結果的形式發出`Run`物件。 完整詳細資料[模型設定檔結構描述可在 API 文件](https://docs.microsoft.com/python/api/azureml-core/azureml.core.profile.modelprofile?view=azure-ml-py)

深入了解[如何分析您的模型使用 SDK](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#profile-workspace--profile-name--models--inference-config--input-data-)

## <a name="deploy-to-target"></a>部署到目標

### <a id="local"></a> 本機部署

若要在本機部署，您需要有**安裝 Docker**本機電腦上。

+ **使用 SDK**

  ```python
  deployment_config = LocalWebservice.deploy_configuration(port=8890)
  service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config)
  service.wait_for_deployment(show_output = True)
  print(service.state)
  ```

+ **使用 CLI**

    若要使用 CLI 進行部署，使用下列命令。 取代`mymodel:1`具有名稱和版本的已註冊的模型：

  ```azurecli-interactive
  az ml model deploy -m mymodel:1 -ic inferenceconfig.json -dc deploymentconfig.json
  ```

    中的項目`deploymentconfig.json`文件引導模式的參數[LocalWebservice.deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservicedeploymentconfiguration?view=azure-ml-py)。 下表描述的 JSON 文件中的實體和方法的參數之間的對應：

    | JSON 實體 | 方法參數 | 描述 |
    | ----- | ----- | ----- |
    | `computeType` | NA | 計算目標。 值必須是本機， `local`。 |
    | `port` | `port` | 本機連接埠上公開服務的 HTTP 端點。 |

    下列 JSON 是使用 CLI 的使用範例部署組態：

    ```json
    {
        "computeType": "local",
        "port": 32267
    }
    ```

### <a id="aci"></a> Azure 容器執行個體 （研發/測試）

如果符合下列一或多個條件，請使用 Azure 容器執行個體來將您的模型部署為 Web 服務：
- 您需要快速部署及驗證模型。
- 您正在測試處於開發狀態的模型。 

若要查看 ACI 配額和區域可用性，請參閱[配額和區域可用性，Azure Container Instances 的](https://docs.microsoft.com/azure/container-instances/container-instances-quotas)文章。

+ **使用 SDK**

  ```python
  deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)
  service = Model.deploy(ws, "aciservice", [model], inference_config, deployment_config)
  service.wait_for_deployment(show_output = True)
  print(service.state)
  ```

+ **使用 CLI**

    若要使用 CLI 進行部署，使用下列命令。 取代`mymodel:1`具有名稱和版本的已註冊的模型。 取代`myservice`與提供給此服務名稱：

    ```azurecli-interactive
    az ml model deploy -m mymodel:1 -n myservice -ic inferenceconfig.json -dc deploymentconfig.json
    ```

    中的項目`deploymentconfig.json`文件引導模式的參數[AciWebservice.deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aci.aciservicedeploymentconfiguration?view=azure-ml-py)。 下表描述的 JSON 文件中的實體和方法的參數之間的對應：

    | JSON 實體 | 方法參數 | 描述 |
    | ----- | ----- | ----- |
    | `computeType` | NA | 計算目標。 ACI，值必須是`ACI`。 |
    | `containerResourceRequirements` | NA | 包含 CPU 和記憶體配置容器的組態項目。 |
    | &emsp;&emsp;`cpu` | `cpu_cores` | 為此 web 服務所配置的 CPU 核心數目。 預設值， `0.1` |
    | &emsp;&emsp;`memoryInGB` | `memory_gb` | （以 gb 為單位） 的記憶體數量配置給此 web 服務。 預設值， `0.5` |
    | `location` | `location` | 若要部署到此 web 服務的 Azure 區域。 如果未指定將使用位置的工作區。 在 可用的區域上的更多詳細資料可以在這裡找到：[ACI 區域](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=container-instances) |
    | `authEnabled` | `auth_enabled` | 是否要啟用此 web 服務驗證。 預設為 False |
    | `sslEnabled` | `ssl_enabled` | 是否要啟用 SSL，此 web 服務。 預設為 False。 |
    | `appInsightsEnabled` | `enable_app_insights` | 是否要啟用 AppInsights 針對此 web 服務。 預設為 False |
    | `sslCertificate` | `ssl_cert_pem_file` | 如果 SSL 已啟用所需的憑證檔案 |
    | `sslKey` | `ssl_key_pem_file` | 如果 SSL 已啟用所需的金鑰檔 |
    | `cname` | `ssl_cname` | Cname 如果啟用 SSL |
    | `dnsNameLabel` | `dns_name_label` | 評分端點 dns 名稱標籤。 如果未指定唯一的 dns 名稱標籤將會產生評分端點。 |

    下列 JSON 是使用 CLI 的使用範例部署組態：

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

  若要[使用 VS Code 將模型部署](how-to-vscode-tools.md#deploy-and-manage-models)您不需要建立 ACI 容器事先測試，因為即時建立 ACI 容器。

如需詳細資訊，請參閱 [AciWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py) \(英文\) 和 [Webservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice?view=azure-ml-py) \(英文\) 類別的參考文件。

### <a id="aks"></a>Azure Kubernetes Service （研發/測試與生產環境）

您可以使用現有的 AKS 叢集，或使用 Azure Machine Learning SDK、CLI 或 Azure 入口網站建立新的叢集。

<a id="deploy-aks"></a>

如果您已經有附加的 AKS 叢集，您可以將它部署到它。 如果您還沒有建立或附加的 AKS 叢集，請依照下列程序<a href="#create-attach-aks">建立新的 AKS 叢集</a>。

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

    若要使用 CLI 進行部署，使用下列命令。 取代`myaks`AKS 同名的計算目標。 取代`mymodel:1`具有名稱和版本的已註冊的模型。 取代`myservice`與提供給此服務名稱：

  ```azurecli-interactive
  az ml model deploy -ct myaks -m mymodel:1 -n myservice -ic inferenceconfig.json -dc deploymentconfig.json
  ```

    中的項目`deploymentconfig.json`文件引導模式的參數[AksWebservice.deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aks.aksservicedeploymentconfiguration?view=azure-ml-py)。 下表描述的 JSON 文件中的實體和方法的參數之間的對應：

    | JSON 實體 | 方法參數 | 描述 |
    | ----- | ----- | ----- |
    | `computeType` | NA | 計算目標。 AKS，值必須是`aks`。 |
    | `autoScaler` | NA | 包含自動調整規模的組態項目。 請參閱 autoscaler 表格。 |
    | &emsp;&emsp;`autoscaleEnabled` | `autoscale_enabled` | 是否要啟用 web 服務的自動調整。 如果`numReplicas`  =  `0`， `True`; 否則`False`。 |
    | &emsp;&emsp;`minReplicas` | `autoscale_min_replicas` | 最小數目的容器時要使用自動調整這個 web 服務。 預設值， `1`。 |
    | &emsp;&emsp;`maxReplicas` | `autoscale_max_replicas` | 容器時要使用的最大數目自動調整這個 web 服務。 預設值， `10`。 |
    | &emsp;&emsp;`refreshPeriodInSeconds` | `autoscale_refresh_seconds` | 自動調整程式嘗試的頻率來調整此 web 服務。 預設值， `1`。 |
    | &emsp;&emsp;`targetUtilization` | `autoscale_target_utilization` | 目標使用率 （以滿分為 100 的百分比表示），自動調整程式應該嘗試維護這個 web 服務。 預設值， `70`。 |
    | `dataCollection` | NA | 包含資料集合的組態項目。 |
    | &emsp;&emsp;`storageEnabled` | `collect_model_data` | 是否要啟用 web 服務的模型資料收集。 預設值， `False`。 |
    | `authEnabled` | `auth_enabled` | 是否要啟用 web 服務的驗證。 預設值， `True`。 |
    | `containerResourceRequirements` | NA | 包含 CPU 和記憶體配置容器的組態項目。 |
    | &emsp;&emsp;`cpu` | `cpu_cores` | 為此 web 服務所配置的 CPU 核心數目。 預設值， `0.1` |
    | &emsp;&emsp;`memoryInGB` | `memory_gb` | （以 gb 為單位） 的記憶體數量配置給此 web 服務。 預設值， `0.5` |
    | `appInsightsEnabled` | `enable_app_insights` | 是否要啟用 web 服務的 Application Insights 記錄。 預設值， `False`。 |
    | `scoringTimeoutMs` | `scoring_timeout_ms` | 若要強制執行評分 web 服務呼叫的逾時。 預設值， `60000`。 |
    | `maxConcurrentRequestsPerContainer` | `replica_max_concurrent_requests` | 此 web 服務針對每個節點最大的並行要求。 預設值， `1`。 |
    | `maxQueueWaitMs` | `max_request_wait_time` | 要求都會保留在三個 503 之前的佇列 （以毫秒為單位） 的最大時間就會傳回錯誤。 預設值， `500`。 |
    | `numReplicas` | `num_replicas` | 配置為此 web 服務的容器數目。 沒有預設值。 如果未設定此參數，預設會啟用自動調整程式。 |
    | `keys` | NA | 包含索引鍵的組態項目。 |
    | &emsp;&emsp;`primaryKey` | `primary_key` | 若要使用此 web 服務的主要驗證金鑰 |
    | &emsp;&emsp;`secondaryKey` | `secondary_key` | 若要使用此 web 服務的次要驗證金鑰 |
    | `gpuCores` | `gpu_cores` | 配置給此 Webservice 的 GPU 核心數目。 預設值為 1。 |
    | `livenessProbeRequirements` | NA | 包含作用探查需求的組態項目。 |
    | &emsp;&emsp;`periodSeconds` | `period_seconds` | 頻率 （以秒為單位） 進行的探測。 預設為 10 秒。 最小值為 1。 |
    | &emsp;&emsp;`initialDelaySeconds` | `initial_delay_seconds` | 容器啟動之前起始作用探查之後的秒數。 預設值為 310 |
    | &emsp;&emsp;`timeoutSeconds` | `timeout_seconds` | 探測逾時之前的秒數。預設值為 2 秒。 最小值為 1 |
    | &emsp;&emsp;`successThreshold` | `success_threshold` | 探測才能算是成功之後有失敗的最小連續成功。 預設值為 1。 最小值為 1。 |
    | &emsp;&emsp;`failureThreshold` | `failure_threshold` | 當啟動 Pod，而且探測失敗時，Kubernetes 會嘗試放棄之前 failureThreshold 時間。 預設值是 3。 最小值為 1。 |
    | `namespace` | `namespace` | Web 服務部署到 Kubernetes 命名空間。 最多 63 個小寫英數字元 ('a'-'z'、 '0'-'9') 和連字號 ('-') 字元。 第一個和最後一個字元不能連字號。 |

    下列 JSON 是使用 CLI 的使用範例部署組態：

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

  您也可以[VS Code 延伸模組部署到 AKS](how-to-vscode-tools.md#deploy-and-manage-models)，但您必須事先設定 AKS 叢集。

進一步了解 AKS 部署和自動調整[AksWebservice.deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.akswebservice)參考。

#### 建立新的 AKS 叢集<a id="create-attach-aks"></a>
**估計時間**：約 20 分鐘。

建立或附加的 AKS 叢集一次處理您的工作區。 您可以重複使用此叢集進行多個部署。 如果您刪除叢集或包含它的資源群組，您必須在的下次您需要部署時建立新的叢集。 您可以有多個附加至您的工作區的 AKS 叢集。

如果您想要建立 AKS 叢集的開發、 驗證和測試，您設定`cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST`使用時[ `provisioning_configuration()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py)。 建立此設定的叢集中只有一個節點。

> [!IMPORTANT]
> 設定`cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST`建立的 AKS 叢集，不適合用於處理生產流量。 推斷的時間可能超過用於生產環境中建立的叢集。 開發/測試叢集也不保證容錯功能。
>
> 我們建議用於開發/測試建立的叢集使用至少兩個虛擬 Cpu。

下列範例示範如何建立新的 Azure Kubernetes 服務叢集：

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

如需有關如何建立 AKS 叢集外部 Azure 機器學習服務 SDK 的詳細資訊，請參閱下列文章：
* [建立 AKS 叢集](https://docs.microsoft.com/cli/azure/aks?toc=%2Fazure%2Faks%2FTOC.json&bc=%2Fazure%2Fbread%2Ftoc.json&view=azure-cli-latest#az-aks-create)
* [建立 AKS 叢集 （入口網站）](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough-portal?view=azure-cli-latest)

如需詳細資訊`cluster_purpose`參數，請參閱 < [AksCompute.ClusterPurpose](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.akscompute.clusterpurpose?view=azure-ml-py)參考。

> [!IMPORTANT]
> 針對 [`provisioning_configuration()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py)，如果您要為 agent_count 和 vm_size 挑選自訂值，則必須先確定 agent_count 乘以 vm_size 會大於或等於 12 個虛擬 CPU。 例如，如果您使用的 vm_size 為 "Standard_D3_v2" (其具有 4 個虛擬 CPU)，則您應該挑選等於或大於 3 的 agent_count。
>
> Azure 機器學習服務 SDK 不提供調整 AKS 叢集的支援。 若要調整叢集中的節點，用於您的 AKS 叢集，在 Azure 入口網站中的 UI。 您只能變更節點計數，而不是叢集中的 VM 大小。

#### <a name="attach-an-existing-aks-cluster"></a>附加現有的 AKS 叢集
**估計時間：** 大約需要 5 分鐘。

如果您已經有 Azure 訂用帳戶，AKS 叢集，而且它是 1.12 版。 # #，您可以使用它來部署您的映像。

> [!WARNING]
> 當附加至工作區的 AKS 叢集，您可以定義您藉由設定使用叢集的方式`cluster_purpose`參數。
>
> 如果您未設定`cluster_purpose`參數或設定`cluster_purpose = AksCompute.ClusterPurpose.FAST_PROD`，則叢集必須至少 12 個虛擬 Cpu 可用。
>
> 如果您將設定`cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST`，則不需要叢集，這是具有 12 個虛擬 Cpu。 不過已針對開發/測試的叢集將不會適用於生產環境層級的流量，而且可能增加推斷時間。

下列程式碼示範如何附加現有的 AKS 1.12。 # # 您的工作區的叢集：

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

如需詳細資訊`attack_configuration()`，請參閱 < [AksCompute.attach_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)參考。

如需詳細資訊`cluster_purpose`參數，請參閱 < [AksCompute.ClusterPurpose](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.akscompute.clusterpurpose?view=azure-ml-py)參考。

## <a name="consume-web-services"></a>取用 Web 服務

每個已部署的 web 服務會提供 REST API，讓您可以在各種程式設計語言中的用戶端應用程式。 如果您已啟用您的服務驗證，您需要提供服務的金鑰做為您的要求標頭中的權杖。

### <a name="request-response-consumption"></a>要求-回應耗用量

以下是如何叫用您的服務，在 Python 中的範例：
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


### <a id="azuremlcompute"></a> 批次推斷
建立並由 Azure Machine Learning 服務來管理 azure Machine Learning 計算目標。 它們可用於從 Azure 機器學習服務管線的批次預測。

使用 Azure Machine Learning 計算的批次推斷的逐步解說，請閱讀[如何執行批次預測](how-to-run-batch-predictions.md)文章。

### <a id="iotedge"></a> IoT Edge 推斷
部署到 edge 支援處於預覽狀態。 如需詳細資訊，請參閱 <<c0> [ 為 IoT Edge 模組部署 Azure Machine Learning](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-machine-learning)文章。


## <a id="update"></a> 更新 web 服務

當您建立新的模型時，您必須手動更新每項服務，您想要使用新的模型。 若要更新 Web 服務，請使用 `update` 方法。 下列程式碼示範如何更新 web 服務，以使用新的模型：

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

## <a name="continuous-model-deployment"></a>Continuous 模型部署 

您可以連續部署模型使用的機器學習服務延伸模組[Azure DevOps](https://azure.microsoft.com/services/devops/)。 使用 Azure DevOps 的機器學習服務的擴充功能，您可以在 Azure 機器學習服務工作區中註冊新的機器學習模型時觸發的部署管線。 

1. 報名[Azure 管線](https://docs.microsoft.com/azure/devops/pipelines/get-started/pipelines-sign-up?view=azure-devops)，這可讓產生持續整合與傳遞到任何平台/任何您應用程式的雲端。 Azure 的管線[不同於 ML 管線](concept-ml-pipelines.md#compare)。 

1. [建立 Azure DevOps 專案。](https://docs.microsoft.com/azure/devops/organizations/projects/create-project?view=azure-devops)

1. 安裝[Azure 管線的機器學習服務延伸模組](https://marketplace.visualstudio.com/items?itemName=ms-air-aiagility.vss-services-azureml&targetId=6756afbe-7032-4a36-9cb6-2771710cadc2&utm_source=vstsproduct&utm_medium=ExtHubManageList) 

1. 使用__服務連線__設定服務主體連線到您的 Azure 機器學習服務工作區，來存取您的所有構件。 移至 專案設定，按一下 服務連線，並選取 Azure Resource Manager。

    ![view-service-connection](media/how-to-deploy-and-where/view-service-connection.png) 

1. 定義為 AzureMLWorkspace__範圍層級__並填入後續的參數。

    ![view-azure-resource-manager](media/how-to-deploy-and-where/resource-manager-connection.png)

1. 接下來，若要持續部署您使用 Azure 管線的機器學習服務模型，在管線選取__釋放__。 加入新的成品，請選取 AzureML 模型的成品與先前步驟中建立的服務連線。 選取模型和版本，以觸發部署。 

    ![select-AzureMLmodel-artifact](media/how-to-deploy-and-where/enable-modeltrigger-artifact.png)

1. 可讓您的模型成品模型觸發程序。 藉由開啟觸發程序，每次指定的版本 （亦即 最新的版本） 該模型會在您的工作區中的暫存器，則會觸發 Azure DevOps 發行管線。 

    ![enable-model-trigger](media/how-to-deploy-and-where/set-modeltrigger.png)

如需範例專案和範例，請參閱[MLOps 存放庫](https://github.com/Microsoft/MLOps)

## <a name="clean-up-resources"></a>清除資源
若要刪除已部署的 Web 服務，請使用 `service.delete()`。
若要刪除已註冊的模型，請使用 `model.delete()`。

如需詳細資訊，請參閱的參考文件[WebService.delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#delete--)，並[Model.delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#delete--)。

## <a name="next-steps"></a>後續步驟
* [如何部署模型使用自訂的 Docker 映像](how-to-deploy-custom-docker-image.md)
* [部署疑難排解](how-to-troubleshoot-deployment.md)
* [使用 SSL 保護 Azure Machine Learning Web 服務](how-to-secure-web-service.md)
* [取用部署為 Web 服務的 ML 模型](how-to-consume-web-service.md)
* [使用 Application Insights 監視您的 Azure Machine Learning 模型](how-to-enable-app-insights.md)
* [在生產環境中收集模型資料](how-to-enable-data-collection.md)

