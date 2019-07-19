---
title: 使用 GPU 部署模型以進行推斷
titleSuffix: Azure Machine Learning service
description: 本文會教您如何使用 Azure Machine Learning 服務將已啟用 GPU 的 Tensorflow 深度學習模型部署為 web 服務。服務和分數推斷要求。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: vaidyas
author: csteegz
ms.reviewer: larryfr
ms.date: 06/01/2019
ms.openlocfilehash: eeb1bc35e0438a7e99ea5ed8284f0c8611108da0
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/18/2019
ms.locfileid: "68326996"
---
# <a name="deploy-a-deep-learning-model-for-inference-with-gpu"></a>使用 GPU 部署深度學習模型以進行推斷

本文會教您如何使用 Azure Machine Learning 服務將已啟用 GPU 的 Tensorflow 深度學習模型部署為 web 服務。

將您的模型部署到 Azure Kubernetes Service (AKS) 叢集, 以執行已啟用 GPU 的推斷。 推斷 (或模型評分) 是已部署的模型用於預測的階段。 使用 Gpu (而不是 Cpu) 提供高度可平行計算的效能優勢。

雖然此範例使用 TensorFlow 模型, 但您可以對評分檔案和環境檔案進行較小的變更, 以將下列步驟套用至任何支援 Gpu 的機器學習架構。 

在本文中，您會執行下列步驟：

* 建立已啟用 GPU 的 AKS 叢集
* 部署 Tensorflow GPU 模型
* 對已部署的模型發出範例查詢

## <a name="prerequisites"></a>必要條件

* Azure Machine Learning 服務 工作區。
* Python 散發版本。
* 已註冊的 Tensorflow 儲存模型。
    * 若要瞭解如何註冊模型, 請參閱[部署模型](../service/how-to-deploy-and-where.md#registermodel)。

您可以完成本操作說明系列的第一部分,[如何訓練 TensorFlow 模型](how-to-train-tensorflow.md), 以滿足必要的必要條件。

## <a name="provision-an-aks-cluster-with-gpus"></a>布建具有 Gpu 的 AKS 叢集

Azure 有許多不同的 GPU 選項。 您可以使用其中任何一個來進行推斷。 如需功能和成本的完整細目, 請參閱[N 系列 vm 的清單](https://azure.microsoft.com/pricing/details/virtual-machines/linux/#n-series)。

如需有關使用 AKS 搭配 Azure Machine Learning 服務的詳細資訊, 請參閱[如何部署和位置](../service/how-to-deploy-and-where.md#deploy-aks)。

```Python
# Choose a name for your cluster
aks_name = "aks-gpu"

# Check to see if the cluster already exists
try:
    aks_target = ComputeTarget(workspace=ws, name=aks_name)
    print('Found existing compute target')
except ComputeTargetException:
    print('Creating a new compute target...')
    # Provision AKS cluster with GPU machine
    prov_config = AksCompute.provisioning_configuration(vm_size="Standard_NC6")

    # Create the cluster
    aks_target = ComputeTarget.create(
        workspace=ws, name=aks_name, provisioning_configuration=prov_config
    )

    aks_target.wait_for_completion(show_output=True)
```

> [!IMPORTANT]
> 只要布建 AKS 叢集, Azure 就會向您收取費用。 當您完成 AKS 叢集時, 請務必將其刪除。

## <a name="write-the-entry-script"></a>撰寫專案腳本

將下列程式碼儲存到您的工作`score.py`目錄中。 此檔案會在影像傳送到您的服務時對其進行評分。 它會載入 TensorFlow 儲存的模型、將輸入影像傳遞至每個 POST 要求上的 TensorFlow 會話, 然後傳回產生的分數。 其他推斷架構則需要不同的評分檔案。

```python
import json
import numpy as np
import os
import tensorflow as tf

from azureml.core.model import Model

def init():
    global X, output, sess
    tf.reset_default_graph()
    model_root = Model.get_model_path('tf-dnn-mnist')
    saver = tf.train.import_meta_graph(os.path.join(model_root, 'mnist-tf.model.meta'))
    X = tf.get_default_graph().get_tensor_by_name("network/X:0")
    output = tf.get_default_graph().get_tensor_by_name("network/output/MatMul:0")
    
    sess = tf.Session()
    saver.restore(sess, os.path.join(model_root, 'mnist-tf.model'))

def run(raw_data):
    data = np.array(json.loads(raw_data)['data'])
    # make prediction
    out = output.eval(session=sess, feed_dict={X: data})
    y_hat = np.argmax(out, axis=1)
    return y_hat.tolist()

```
## <a name="define-the-conda-environment"></a>定義 conda 環境

建立名`myenv.yml`為的 conda 環境檔案, 以指定服務的相依性。 請務必指定您要使用`tensorflow-gpu`來達到加速效能。

```yaml
name: project_environment
dependencies:
  # The python interpreter version.
  # Currently Azure ML only supports 3.5.2 and later.
- python=3.6.2

- pip:
  - azureml-defaults==1.0.43.*
- numpy
- tensorflow-gpu=1.12
channels:
- conda-forge
```

## <a name="define-the-gpu-inferenceconfig-class"></a>定義 GPU InferenceConfig 類別

建立可啟用 gpu 並確保 CUDA 與您的 Docker 映射一起安裝的物件。`InferenceConfig`

```python
from azureml.core.model import Model
from azureml.core.model import InferenceConfig

aks_service_name ='aks-dnn-mnist'
gpu_aks_config = AksWebservice.deploy_configuration(autoscale_enabled = False, 
                                                    num_replicas = 3, 
                                                    cpu_cores=2, 
                                                    memory_gb=4)
model = Model(ws,"tf-dnn-mnist")

inference_config = InferenceConfig(runtime= "python", 
                                   entry_script="score.py",
                                   conda_file="myenv.yml", 
                                   enable_gpu=True)
```

如需詳細資訊，請參閱：

- [InferenceConfig 類別](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py)
- [AksServiceDeploymentConfiguration 類別](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aks.aksservicedeploymentconfiguration?view=azure-ml-py)

## <a name="deploy-the-model"></a>部署模型

將模型部署到您的 AKS 叢集, 並等候它建立您的服務。

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
> Azure Machine Learning 服務不會將需要啟用 gpu `InferenceConfig`的物件, 部署到沒有 gpu 的叢集。

如需詳細資訊, 請參閱[模型類別](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py)。

## <a name="issue-a-sample-query-to-your-model"></a>對您的模型發出範例查詢

將測試查詢傳送至已部署的模型。 當您將 jpeg 影像傳送到模型時, 它會對影像進行評分。 下列程式碼範例會使用外部公用程式函式來載入影像。 您可以在[GitHub 上的 Pir TensorFlow 範例](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-tensorflow/utils.py)中找到相關的程式碼。 

```python
# Used to test your webservice
from utils import load_data 

# Load test data from model training
X_test = load_data('./data/mnist/test-images.gz', False) / 255.0
y_test = load_data('./data/mnist/test-labels.gz', True).reshape(-1)

# send a random row from the test set to score
random_index = np.random.randint(0, len(X_test)-1)
input_data = "{\"data\": [" + str(list(X_test[random_index])) + "]}"

api_key = aks_service.get_keys()[0]
headers = {'Content-Type':'application/json', 'Authorization':('Bearer '+ api_key)}
resp = requests.post(aks_service.scoring_uri, input_data, headers=headers)

print("POST to url", aks_service.scoring_uri)
#print("input data:", input_data)
print("label:", y_test[random_index])
print("prediction:", resp.text)
```

> [!IMPORTANT]
> 若要將延遲降至最低並將輸送量優化, 請確定您的用戶端位於與端點相同的 Azure 區域中。 在此範例中, Api 會建立在「美國東部」 Azure 區域中。

## <a name="clean-up-the-resources"></a>清除資源

如果您特別針對此範例建立了 AKS 叢集, 請在完成後刪除您的資源。

> [!IMPORTANT]
> Azure 會根據 AKS 叢集的部署時間來計費。 完成使用之後, 請務必將它清除。

```python
aks_service.delete()
aks_target.delete()
```

## <a name="next-steps"></a>後續步驟

* [在 FPGA 上部署模型](../service/how-to-deploy-fpga-web-service.md)
* [使用 ONNX 部署模型](../service/concept-onnx.md#deploy-onnx-models-in-azure)
* [定型 Tensorflow DNN 模型](../service/how-to-train-tensorflow.md)
