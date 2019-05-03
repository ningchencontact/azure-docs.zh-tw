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
ms.date: 05/02/2019
ms.custom: seoapril2019
ms.openlocfilehash: 1da232c2a81c9989cc78eccf1be97b5d75a48666
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/02/2019
ms.locfileid: "65024477"
---
# <a name="deploy-models-with-the-azure-machine-learning-service"></a>使用 Azure Machine Learning 服務部署模型

了解如何部署您的機器學習模型作為 web 服務在 Azure 雲端中，或到 IoT Edge 裝置。 這份文件中的資訊將教導您如何部署至下列的計算目標：

| 計算目標 | 部署類型 | 描述 |
| ----- | ----- | ----- |
| [本機 web 服務](#local) | 測試/偵錯 | 適用於有限的測試和疑難排解。
| [Azure Kubernetes Service (AKS)](#aks) | 即時推斷 | 適用於大規模生產環境部署。 提供自動調整和快速的回應時間。 |
| [Azure 容器執行個體 (ACI)](#aci) | 測試 | 適用於低規模，以 CPU 為基礎的工作負載。 |
| [Azure Machine Learning Compute](how-to-run-batch-predictions.md) | （預覽）批次推斷 | 執行批次評分上無伺服器計算。 支援一般 vm 和低優先順序 Vm。 |
| [Azure IoT Edge](#iotedge) | （預覽）IoT 模組 | 部署與提供在 IoT 裝置上的 ML 模型。 |

## <a name="deployment-workflow"></a>部署工作流程

部署模型的程序與所有計算目標類似：

1. 註冊模型。
1. 部署模型。
1. 測試已部署的模型。

如需部署工作流程中相關概念的詳細資訊，請參閱[使用 Azure Machine Learning 服務來管理、部署及監視模型](concept-model-management-and-deployment.md)。

## <a name="prerequisites-for-deployment"></a>部署必要條件

- 模型。 如果您不需要定型的模型，您可以使用模型 & 相依性檔案中提供[本教學課程](http://aka.ms/azml-deploy-cloud)。

- [機器學習服務的 Azure CLI 擴充功能](reference-azure-machine-learning-cli.md)，或有[Azure Machine Learning Python SDK](https://aka.ms/aml-sdk)。

## <a id="registermodel"></a> 註冊的機器學習服務模型

模型註冊是在 Azure 雲端中儲存和組織已定型模型的方法。 模型是註冊在您的 Azure Machine Learning 服務工作區中。 使用 Azure Machine Learning，或從其他地方定型的模型匯入，則可以訓練模型。 下列範例示範如何註冊模型，以從檔案：

### <a name="register-a-model-from-an-experiment-run"></a>註冊模型的實驗執行

**使用 CLI 的 Scikit-learn 範例**
```azurecli-interactive
az ml model register -n sklearn_mnist  --asset-path outputs/sklearn_mnist_model.pkl  --experiment-name myexperiment
```
**使用 SDK**
```python
model = run.register_model(model_name='sklearn_mnist', model_path='outputs/sklearn_mnist_model.pkl')
print(model.name, model.id, model.version, sep='\t')
```

### <a name="register-an-externally-created-model"></a>註冊外部建立的模型
您可以藉由提供註冊的外部建立的模型**的本機路徑**模型。 您可以提供一個資料夾或單一檔案。

**使用 Python SDK 的 ONNX 範例：**
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

**使用 CLI**
```azurecli-interactive
az ml model register -n onnx_mnist -p mnist/model.onnx
```

**估計時間**：大約 10 秒。

如需詳細資訊，請參閱 [Model 類別](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py) \(英文\) 的參考文件。

## <a name="how-to-deploy"></a>如何部署

若要部署為 web 服務，您必須建立推斷組態 (`InferenceConfig`) 和部署組態。 在推斷組態中，您可以指定指令碼和您的模型所需的相依性。 部署組態中，您會指定如何計算目標上提供模型的詳細資料。


### <a id="script"></a> 1.定義您的項目指令碼和相依性

項目指令碼會接收到已部署的 web 服務，送出的資料，並將它傳遞至模型。 然後，它會採用模型傳回的回應，並將該回應傳回至用戶端。 **指令碼是您的模型特有**; 它必須了解模型所預期，並傳回的資料。

指令碼包含兩個函式，載入及執行模型：

* `init()`:此函式通常會將模型載入到全域物件。 只要您的 web 服務的 Docker 容器啟動時，會執行此函式。

* `run(input_data)`:此函式會使用模型，依據輸入資料來預測值。 執行的輸入和輸出通常使用 JSON 進行序列化和還原序列化。 您也可以使用原始的二進位資料。 您可以先轉換資料，再將資料傳送給模型或傳回用戶端。

#### <a name="optional-automatic-swagger-schema-generation"></a>（選擇性）自動產生 Swagger 結構描述

自動產生的結構描述，您的 web 服務、 提供輸入的範例和/或輸出中定義的型別物件的型別和範例的其中一個建構函式用來自動建立結構描述。 接著會建立 azure Machine Learning 服務[OpenAPI](https://swagger.io/docs/specification/about/) (Swagger) 規格的 web 服務部署期間。

目前支援下列類型：

* `pandas`
* `numpy`
* `pyspark`
* 標準的 Python 物件

若要使用結構描述產生，包括`inference-schema`conda 環境檔案中的封裝。 下列範例會使用`[numpy-support]`由於進入指令碼會使用 numpy 參數類型： 

#### <a name="example-dependencies-file"></a>範例相依性檔案
以下是推斷的 Conda 相依性檔案的範例。
```python
name: project_environment
dependencies:
  - python=3.6.2
  - pip:
    - azureml-defaults
    - scikit-learn
    - inference-schema[numpy-support]
```

如果您想要使用自動結構描述產生，您的項目指令碼**必須**匯入`inference-schema`封裝。 

定義輸入和輸出中的範例格式`input_sample`和`output_sample`代表 web 服務的要求和回應格式的變數。 使用這些範例中的輸入和輸出函式的裝飾項目上`run()`函式。 Scikit-learn-了解下列範例會使用結構描述產生。

> [!TIP]
> 在部署服務之後, 使用`swagger_uri`屬性，以擷取結構描述 JSON 文件。

#### <a name="example-entry-script"></a>範例項目指令碼

下列範例示範如何接受及傳回的 JSON 資料：

**使用 Swagger 產生的 scikit-learn 等最了解範例：**
```python
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

如需詳細的範例指令碼，請參閱下列範例：

* Pytorch: [https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-pytorch](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-pytorch)
* TensorFlow: [https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-tensorflow](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-tensorflow)
* Keras: [https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras)
* ONNX: [https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/onnx/](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/onnx/)
* 計分二進位資料：[如何使用 web 服務](how-to-consume-web-service.md)

### <a name="2-define-your-inferenceconfig"></a>2.定義您 InferenceConfig

推斷組態會描述如何設定模型進行預測。 下列範例示範如何建立推斷組態：

```python
inference_config = InferenceConfig(source_directory="C:/abc",
                                   runtime= "python",
                                   entry_script="x/y/score.py",
                                   conda_file="env/myenv.yml")
```

在此範例中，組態會包含下列項目：

* 包含執行推斷所需的資產目錄
* 此模型需要使用 Python
* [項目指令碼](#script)，用以處理 web 要求傳送至已部署的服務
* 描述執行推斷所需的 Python 套件的 conda 檔案

如需 InferenceConfig 功能資訊，請參閱[進階的組態](#advanced-config)一節。

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

## <a name="where-to-deploy"></a>部署位置

### <a id="local"></a> 在本機部署

本節的範例中使用[deploy_from_image](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#deploy-from-model-workspace--name--models--image-config--deployment-config-none--deployment-target-none-)，而這需要您註冊的模型和映像之前進行部署。 如需有關其他部署方法的詳細資訊，請參閱 <<c0> [ 部署](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#deploy-workspace--name--model-paths--image-config--deployment-config-none--deployment-target-none-)並[deploy_from_model](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#deploy-from-model-workspace--name--models--image-config--deployment-config-none--deployment-target-none-)。

**若要在本機部署，您需要在本機電腦上安裝 docker。**

**使用 SDK**

```python
deployment_config = LocalWebservice.deploy_configuration(port=8890)
service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config)
service.wait_for_deployment(show_output = True)
print(service.state)
```

**使用 CLI**

```azurecli-interactive
az ml model deploy -m sklearn_mnist:1 -ic inferenceconfig.json -dc deploymentconfig.json
```

### <a id="aci"></a> 部署至 Azure Container Instances （研發/測試）

如果符合下列一或多個條件，請使用 Azure 容器執行個體來將您的模型部署為 Web 服務：
- 您需要快速部署及驗證模型。
- 您正在測試處於開發狀態的模型。 

若要查看 ACI 配額和區域可用性，請參閱[配額和區域可用性，Azure Container Instances 的](https://docs.microsoft.com/azure/container-instances/container-instances-quotas)文章。

**使用 SDK**

```python
deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)
service = Model.deploy(ws, "aciservice", [model], inference_config, deployment_config)
service.wait_for_deployment(show_output = True)
print(service.state)
```

**使用 CLI**

```azurecli-interactive
az ml model deploy -m sklearn_mnist:1 -n aciservice -ic inferenceconfig.json -dc deploymentconfig.json
```

如需詳細資訊，請參閱 [AciWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py) \(英文\) 和 [Webservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice?view=azure-ml-py) \(英文\) 類別的參考文件。

### <a id="aks"></a> 部署到 Azure Kubernetes Service （生產）

您可以使用現有的 AKS 叢集，或使用 Azure Machine Learning SDK、CLI 或 Azure 入口網站建立新的叢集。


> [!IMPORTANT]
> 建立 AKS 叢集是工作區的一次性程序。 您可以重複使用此叢集進行多個部署。
> 如果您未建立或附加的 AKS 叢集 go<a href="#create-attach-aks">此處</a>。

#### 部署到 AKS <a id="deploy-aks"></a>

您可以部署到 AKS 使用 Azure ML CLI:
```azurecli-interactive
az ml model deploy -ct myaks -m mymodel:1 -n aksservice -ic inferenceconfig.json -dc deploymentconfig.json
```

您也可以使用 Python SDK:
```python
aks_target = AksCompute(ws,"myaks")
deployment_config = AksWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)
service = Model.deploy(ws, "aksservice", [model], inference_config, deployment_config, aks_target)
service.wait_for_deployment(show_output = True)
print(service.state)
print(service.get_logs())
```

如需有關設定您的 AKS 部署，包括自動調整功能，請參閱[AksWebservice.deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.akswebservice)參考。

**估計時間：** 大約需要 5 分鐘。

#### 建立或附加的 AKS 叢集 <a id="create-attach-aks"></a>
建立或附加的 AKS 叢集**一個階段程序**您工作區。 叢集已與您的工作區相關聯之後，您可以針對多個部署中使用它。 

如果您刪除叢集或包含它的資源群組，您必須在的下次您需要部署時建立新的叢集。

##### <a name="create-a-new-aks-cluster"></a>建立新的 AKS 叢集
如需設定的詳細資訊`autoscale_target_utilization`， `autoscale_max_replicas`，並`autoscale_min_replicas`，請參閱[AksWebservice.deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py#deploy-configuration-autoscale-enabled-none--autoscale-min-replicas-none--autoscale-max-replicas-none--autoscale-refresh-seconds-none--autoscale-target-utilization-none--collect-model-data-none--auth-enabled-none--cpu-cores-none--memory-gb-none--enable-app-insights-none--scoring-timeout-ms-none--replica-max-concurrent-requests-none--max-request-wait-time-none--num-replicas-none--primary-key-none--secondary-key-none--tags-none--properties-none--description-none-)參考。
下列範例示範如何建立新的 Azure Kubernetes 服務叢集：

```python
from azureml.core.compute import AksCompute, ComputeTarget

# Use the default configuration (you can also provide parameters to customize this)
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


> [!IMPORTANT]
> 針對 [`provisioning_configuration()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py)，如果您要為 agent_count 和 vm_size 挑選自訂值，則必須先確定 agent_count 乘以 vm_size 會大於或等於 12 個虛擬 CPU。 例如，如果您使用的 vm_size 為 "Standard_D3_v2" (其具有 4 個虛擬 CPU)，則您應該挑選等於或大於 3 的 agent_count。

**估計時間**：約 20 分鐘。

##### <a name="attach-an-existing-aks-cluster"></a>附加現有的 AKS 叢集

如果您已經有 Azure 訂用帳戶，AKS 叢集，而且它是 1.12 版。 # # 已至少 12 個虛擬 Cpu，您可以使用它來部署您的映像。 下列程式碼示範如何附加現有的 AKS 1.12。 # # 您的工作區的叢集：

```python
from azureml.core.compute import AksCompute, ComputeTarget
# Set the resource group that contains the AKS cluster and the cluster name
resource_group = 'myresourcegroup'
cluster_name = 'mycluster'

# Attach the cluster to your workgroup
attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                         cluster_name = cluster_name)
aks_target = ComputeTarget.attach(ws, 'mycompute', attach_config)
```

## <a name="consume-web-services"></a>取用 Web 服務
每個已部署的 web 服務會提供 REST API，讓您可以在各種程式設計語言中的用戶端應用程式。 如果您已啟用您的服務驗證，您需要提供服務的金鑰做為您的要求標頭中的權杖。

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

## <a id="update"></a> 更新 Web 服務

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

## <a name="clean-up"></a>清除
若要刪除已部署的 Web 服務，請使用 `service.delete()`。
若要刪除已註冊的模型，請使用 `model.delete()`。

如需詳細資訊，請參閱的參考文件[WebService.delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#delete--)，並[Model.delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#delete--)。

## 進階的組態設定 <a id="advanced-config"></a>

### <a id="customimage"></a> 使用自訂的基底映像

就內部而言，InferenceConfig 建立 Docker 映像，其中包含模型和其他服務所需的資產。 如果未指定，則會使用預設的基底映像。

當建立推斷組態搭配使用的映像，映像必須符合下列需求：

* Ubuntu 16.04 或更新版本。
* Conda 4.5。 # 或更新版本。
* Python 3.5。 # 或 3.6。 #。

若要使用自訂映像，`base_image`推斷設定位址的映像的屬性。 下列範例示範如何使用來自這兩個公用和私用 Azure Container Registry 的映像：

```python
# use an image available in public Container Registry without authentication
inference_config.base_image = "mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda"

# or, use an image available in a private Container Registry
inference_config.base_image = "myregistry.azurecr.io/mycustomimage:1.0"
inference_config.base_image_registry.address = "myregistry.azurecr.io"
inference_config.base_image_registry.username = "username"
inference_config.base_image_registry.password = "password"
```

下列映像 Uri 是由 Microsoft 所提供的映像，並可不搭配提供的使用者名稱或密碼值：

* `mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda`
* `mcr.microsoft.com/azureml/onnxruntime:v0.4.0`
* `mcr.microsoft.com/azureml/onnxruntime:v0.4.0-cuda10.0-cudnn7`
* `mcr.microsoft.com/azureml/onnxruntime:v0.4.0-tensorrt19.03`

若要使用這些映像，`base_image`至上述清單中的 URI。 將 `base_image_registry.address` 設定為 `mcr.microsoft.com`。

> [!IMPORTANT]
> 使用 CUDA 或 TensorRT 的 Microsoft 映像必須只使用 Microsoft Azure 服務上。

如需有關如何將自己的映像上傳至 Azure Container Registry 的詳細資訊，請參閱 <<c0> [ 您的第一個映像推送至私人 Docker 容器登錄](https://docs.microsoft.com/azure/container-registry/container-registry-get-started-docker-cli)。

如果您的模型訓練 Azure Machine Learning 計算，使用__1.0.22 版或更新版本__Azure 機器學習服務 sdk，在定型期間建立的映像。 下列範例示範如何使用此映像：

```python
# Use an image built during training with SDK 1.0.22 or greater
image_config.base_image = run.properties["AzureML.DerivedImageName"]
```

## <a name="other-inference-options"></a>其他推斷選項

### <a id="azuremlcompute"></a> 批次推斷
建立並由 Azure Machine Learning 服務來管理 azure Machine Learning 計算目標。 它們可用於從 Azure 機器學習服務管線的批次預測。

使用 Azure Machine Learning 計算的批次推斷的逐步解說，請閱讀[如何執行批次預測](how-to-run-batch-predictions.md)文章。

## <a id="iotedge"></a> IoT Edge 上的推斷
部署到 edge 支援處於預覽狀態。 如需詳細資訊，請參閱 <<c0> [ 為 IoT Edge 模組部署 Azure Machine Learning](https://docs.microsoft.com/en-us/azure/iot-edge/tutorial-deploy-machine-learning)文章。

## <a name="next-steps"></a>後續步驟
* [部署疑難排解](how-to-troubleshoot-deployment.md)
* [使用 SSL 保護 Azure Machine Learning Web 服務](how-to-secure-web-service.md)
* [取用部署為 Web 服務的 ML 模型](how-to-consume-web-service.md)
* [使用 Application Insights 監視您的 Azure Machine Learning 模型](how-to-enable-app-insights.md)
* [在生產環境中收集模型資料](how-to-enable-data-collection.md)
