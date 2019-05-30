---
title: 部署具有 GPU 的推斷的模型
titleSuffix: Azure Machine Learning service
description: 了解如何將深度學習模型部署為 web 服務使用 GPU 的推斷。 在本文中，Tensorflow 模型會部署到 Azure Kubernetes 服務叢集。 叢集會使用已啟用 GPU 的 VM 來裝載 web 服務和分數推斷要求。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: vaidyas
author: csteegz
ms.reviewer: larryfr
ms.date: 05/02/2019
ms.openlocfilehash: ec71165553a1d65ff133d605bf94255100f74e6e
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/30/2019
ms.locfileid: "66388930"
---
# <a name="deploy-a-deep-learning-model-for-inference-with-gpu"></a>部署推斷配備 GPU 的深度學習模型

了解如何使用 GPU 推斷，來進行機器學習模型部署為 web 服務。 推斷，或模型計分，是階段已部署的模型用於預測，最常在實際執行資料的位置。

這篇文章會教導您如何使用 Azure Machine Learning 服務來部署範例 Tensorflow 深度學習模型，以在已啟用 GPU 的虛擬機器 (VM) 上的 Azure Kubernetes Service (AKS) 叢集。 當要求傳送至服務時，此模型會使用 GPU 來執行推斷工作負載。

Gpu 提供的效能優於 Cpu 上可高度平行化的計算。 已啟用 GPU 的 Vm 的絕佳的使用案例包括深度學習模型訓練和推斷，特別是針對大型批次要求。

此範例示範如何將模型儲存至 Azure Machine Learning TensorFlow 的部署。 請執行下列步驟：

* 建立已啟用 GPU 的 AKS 叢集
* 部署 GPU Tensorflow 模型

## <a name="prerequisites"></a>必要條件

* Azure 機器學習服務工作區
* Python 散發套件
* 已註冊的 Tensorflow 儲存模型。 若要了解如何註冊模型，請參閱[部署模型](../service/how-to-deploy-and-where.md#registermodel)。

這篇文章根據 Jupyter notebook [AKS 部署 Tensorflow 模型](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/production-deploy-to-aks-gpu/production-deploy-to-aks-gpu.ipynb)。 Jupyter notebook 使用 TensorFlow 儲存模型，然後將它們部署至 AKS 叢集。 您也可以套用至任何機器學習架構，可對評分檔案及環境檔案進行小變更支援 Gpu 的 notebook。  

## <a name="provision-an-aks-cluster-with-gpus"></a>佈建以 Gpu AKS 叢集

Azure 有許多不同的 GPU 選項。 您可以使用其中任何的推斷。 請參閱[的 N 系列 Vm 清單](https://azure.microsoft.com/pricing/details/virtual-machines/linux/#n-series)如需完整的功能和成本解析。

如需有關使用 AKS 使用 Azure Machine Learning 服務的詳細資訊，請參閱[如何部署和位置](../service/how-to-deploy-and-where.md#deploy-aks)。

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
> Azure 會向您收費，只要 AKS 叢集中已佈建。 請務必刪除您的 AKS 叢集，當您完成時使用它。

## <a name="write-the-entry-script"></a>寫入項目指令碼

將下列程式碼儲存至您的工作目錄為`score.py`。 此檔案分數的映像，因為它們會被傳送到您的服務。 該載入儲存的 TensorFlow 模型、 每個 POST 要求傳遞到 TensorFlow 的工作階段輸入的映像，然後傳回產生的分數。 其他推斷架構需要不同的評分檔案。

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

## <a name="define-the-conda-environment"></a>定義的 conda 環境

建立名為的 conda 環境檔案`myenv.yml`到指定的相依性服務。 請務必指定您要使用`tensorflow-gpu`達到加速的效能。

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

## <a name="define-the-gpu-inferenceconfig-class"></a>定義 GPU InferenceConfig 類別

建立`InferenceConfig`啟用 Gpu 並確保 CUDA 會隨您的 Docker 映像的物件。

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

如需詳細資訊，請參閱

- [InferenceConfig 類別](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py)
- [AksServiceDeploymentConfiguration 類別](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aks.aksservicedeploymentconfiguration?view=azure-ml-py)

## <a name="deploy-the-model"></a>部署模型

將模型部署到您的 AKS 叢集，並等候建立您的服務。

```python
aks_service = Model.deploy(ws,
                           models=[model],
                           inference_config=inference_config, 
                           deployment_config=gpu_aks_config,
                           deployment_target=aks_target,
                           name=aks_service_name)

aks_service.wait_for_deployment(show_output = True)
print(aks_service.state)
```

> [!NOTE]
> Azure Machine Learning 服務不會將部署具有模型`InferenceConfig`預期沒有 GPU 叢集啟用 GPU 的物件。

如需詳細資訊，請參閱 <<c0> [ 模型類別](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py)。

## <a name="issue-a-sample-query-to-your-deployed-model"></a>發給您已部署的模型中的範例查詢

將測試查詢傳送至部署的模型。 當您將 jpeg 影像傳送至模型時，它會分數映像。

```python
scoring_url = aks_service.scoring_uri
api_key = aks_service.get_key()(0)
IMAGEURL = "https://upload.wikimedia.org/wikipedia/commons/thumb/6/68/Lynx_lynx_poing.jpg/220px-Lynx_lynx_poing.jpg"

headers = {'Authorization':('Bearer '+ api_key)}
img_data = read_image_from(IMAGEURL).read()
r = requests.post(scoring_url, data = img_data, headers=headers)
```

> [!IMPORTANT]
> 若要延遲降至最低，並達到最佳輸送量，請確定您的用戶端位於相同的 Azure 區域，做為端點。 在此範例中，Api 會在美國東部 Azure 區域中建立。

## <a name="clean-up-the-resources"></a>清除資源

此範例使用完畢之後，請刪除您的資源。

> [!IMPORTANT]
> Azure 的計費，您根據部署 AKS 叢集的時間長度。 請確定它後清除完成它。

```python
aks_service.delete()
aks_target.delete()
```

## <a name="next-steps"></a>後續步驟

* [部署模型上 FPGA](../service/how-to-deploy-fpga-web-service.md)
* [部署與 ONNX 模型](../service/concept-onnx.md#deploy-onnx-models-in-azure)
* [訓練的 DNN Tensorflow 模型](../service/how-to-train-tensorflow.md)
