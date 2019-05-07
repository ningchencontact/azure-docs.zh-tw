---
title: 如何部署推斷配備 GPU 的深度學習模型
titleSuffix: Azure Machine Learning service
description: 了解如何部署為 web 服務用於推斷的 GPU 的深度學習模型。 在本文中，Tensorflow 模型會部署到 Azure Kubernetes 服務叢集。 叢集會使用已啟用 GPU 的 VM 來裝載 web 服務和分數推斷要求。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: vaidyas
author: csteegz
ms.reviewer: larryfr
ms.date: 05/02/2019
ms.openlocfilehash: 5cc0fe0526937245d3ca913afc477f0259e2afd4
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2019
ms.locfileid: "65160646"
---
# <a name="how-to-do-gpu-inferencing"></a>如何執行 GPU 推斷

了解如何使用 GPU 推斷，來進行機器學習模型部署為 web 服務。 在本文中，您將了解如何使用 Azure Machine Learning 服務來部署範例 Tensorflow 深度學習模型。 模型會部署到使用已啟用 GPU 的 VM 來裝載服務的 Azure Kubernetes Service (AKS) 叢集。 當要求傳送至服務時，此模型會使用 GPU 進行推斷。

Gpu 提供的效能優於 Cpu 上可高度平行化的計算。 訓練和推斷深度學習模型 （特別是針對大型批次中的要求） 是針對 Gpu 的絕佳使用案例。  

此範例會示範如何將儲存的 TensorFlow 模型部署到 Azure Machine Learning。 

## <a name="goals-and-prerequisites"></a>目標和必要條件

依照指示執行以：
* 建立已啟用 GPU AKS 叢集
* 部署具有 Tensorflow GPU 的模型

必要條件：
* Azure 機器學習服務工作區
* Python
* Tensorflow SavedModel 註冊。 若要了解如何註冊模型請參閱[部署模型](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-and-where#registermodel)

這篇文章根據[AKS 部署 Tensorflow 模型](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/production-deploy-to-aks-gpu/production-deploy-to-aks-gpu.ipynb)，它會使用 TensorFlow 儲存模型和部署至 AKS 叢集。 不過，透過對評分檔案及環境檔案進行小變更就適用於任何支援的 Gpu 的機器學習架構。  

## <a name="provision-aks-cluster-with-gpus"></a>佈建具有 Gpu 的 AKS 叢集
Azure 有許多不同的 GPU 選項，當然也可用來推斷。 請參閱[清單中的 N 系列](https://azure.microsoft.com/pricing/details/virtual-machines/linux/#n-series)如需完整的功能和成本解析。 

如需有關使用 AKS 使用 Azure Machine Learning 服務的詳細資訊，請參閱[如何部署，且其中發行項。](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-and-where#create-a-new-cluster)

```python
# Provision AKS cluster with GPU machine
prov_config = AksCompute.provisioning_configuration(vm_size="Standard_NC6")

# Create the cluster
aks_target = ComputeTarget.create(
    workspace=ws, name=aks_name, provisioning_configuration=prov_config
)

aks_target.wait_for_deployment()
```

> [!IMPORTANT]
> Azure 會向您收費，只要 AKS 叢集中已佈建。 請務必刪除您的 AKS 叢集，在您完成使用它。


## <a name="write-entry-script"></a>寫入項目指令碼

將下列文字儲存到您的工作目錄為`score.py`。 此檔案將用於傳送至您的服務映像進行評分。 此檔案載入儲存模型中，TensorFlow，然後在每次張貼要求傳遞到 TensorFlow 的工作階段輸入的映像，並傳回產生的分數。
其他推斷架構將會需要不同的評分檔案。

```python
import tensorflow as tf
import numpy as np
import ujson
from azureml.core.model import Model
from azureml.contrib.services.aml_request import AMLRequest, rawhttp
from azureml.contrib.services.aml_response import AMLResponse

def init():
    global session
    global input_name
    global output_name
    
    session = tf.Session()

    model_path = Model.get_model_path('resnet50')
    model = tf.saved_model.loader.load(session, ['serve'], model_path)
    if len(model.signature_def['serving_default'].inputs) > 1:
        raise ValueError("This score.py only supports one input")
    input_name = [tensor.name for tensor in model.signature_def['serving_default'].inputs.values()][0]
    output_name = [tensor.name for tensor in model.signature_def['serving_default'].outputs.values()]
    

@rawhttp
def run(request):
    if request.method == 'POST':
        reqBody = request.get_data(False)
        resp = score(reqBody)
        return AMLResponse(resp, 200)
    if request.method == 'GET':
        respBody = str.encode("GET is not supported")
        return AMLResponse(respBody, 405)
    return AMLResponse("bad request", 500)

def score(data):
    result = session.run(output_name, {input_name: [data]})
    return ujson.dumps(result[1])

if __name__ == "__main__":
    init()
    with open("lynx.jpg", 'rb') as f: #load file for testing locally
        content = f.read()
        print(score(content))

```

## <a name="define-conda-environment"></a>定義 Conda 環境
建立名為的 conda 環境檔案`myenv.yml`到指定的相依性服務。 請務必指定您使用`tensorflow-gpu`達到加速的效能。
```yaml
name: aml-accel-perf
channels:
  - defaults
dependencies:
  - tensorflow-gpu = 1.12
  - numpy
  - ujson
  - pip:
    - azureml-core
    - azureml-contrib-services
```

## <a name="define-gpu-inferenceconfig"></a>定義 GPU InferenceConfig

建立[ `InferenceConfig` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py)指定您要啟用 GPU。 這可確保 CUDA 會隨您的映像。

```python
from azureml.core.model import Model
from azureml.core.model import InferenceConfig

aks_service_name ='gpu-rn'
gpu_aks_config = AksWebservice.deploy_configuration(autoscale_enabled = False, 
                                                    num_replicas = 3, 
                                                    cpu_cores=2, 
                                                    memory_gb=4)
model = Model(ws,"resnet50")

inference_config = InferenceConfig(runtime= "python", 
                                   entry_script="score.py",
                                   conda_file="myenv.yml", 
                                   gpu_enabled=True)
```

如需詳細資訊，請參閱 < [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py)並[AksServiceDeploymentConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aks.aksservicedeploymentconfiguration?view=azure-ml-py)。
## <a name="deploy-the-model"></a>部署模型

將模型部署到您的 AKS 叢集，並等候建立您的服務。

```python
aks_service = Model.deploy(ws,
                           models=[model],
                           inference_config=inference_config, 
                           deployment_config=aks_config,
                           deployment_target=aks_target,
                           name=aks_service_name)

aks_service.wait_for_deployment(show_output = True)
print(aks_service.state)
```

> [!NOTE]
> Azure Machine Learning 服務並不會部署具有模型`InferenceConfig`，而不需要 GPU 叢集需要 GPU。

如需詳細資訊，請參閱 <<c0> [ 模型](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py)。

## <a name="issue-sample-query-to-deployed-model"></a>問題範例查詢，來部署模型

發給您已部署的模型中的範例查詢。 此模型將分數為 post 要求傳送給它的任何 jpeg 影像。 

```python
scoring_url = aks_service.scoring_uri
api_key = aks_service.get_key()(0)
IMAGEURL = "https://upload.wikimedia.org/wikipedia/commons/thumb/6/68/Lynx_lynx_poing.jpg/220px-Lynx_lynx_poing.jpg"

headers = {'Authorization':('Bearer '+ api_key)}
img_data = read_image_from(IMAGEURL).read()
r = requests.post(scoring_url, data = img_data, headers=headers)
```

> [!IMPORTANT]
> 若要將延遲和輸送量最佳化，您的用戶端應該位於與端點相同 Azure 區域中。  目前 API 是在美國東部的 Azure 區域中建立的。

## <a name="cleaning-up-the-resources"></a>清除資源

示範使用完畢之後，請刪除您的資源。

> [!IMPORTANT]
> Azure 會向您的 AKS 叢集部署的時間長度。 請確定它後清除完成它。

```python
aks_service.delete()
aks_target.delete()
```

## <a name="next-steps"></a>後續步驟

* [部署模型上 FPGA](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-fpga-web-service)
* [部署與 ONNX 模型](https://docs.microsoft.com/azure/machine-learning/service/how-to-build-deploy-onnx#deploy)
* [訓練的 DNN Tensorflow 模型](https://docs.microsoft.com/azure/machine-learning/service/how-to-train-tensorflow)
