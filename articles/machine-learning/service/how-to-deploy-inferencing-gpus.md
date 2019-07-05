---
title: 部署具有 GPU 的推斷的模型
titleSuffix: Azure Machine Learning service
description: 這篇文章會教導您如何使用 Azure Machine Learning 服務來部署深度學習模型作為 web service.service 和分數推斷要求已啟用 GPU 功能 Tensorflow。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: vaidyas
author: csteegz
ms.reviewer: larryfr
ms.date: 06/01/2019
ms.openlocfilehash: 8086d059913cc61bff0bca31681368bea6d76777
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/03/2019
ms.locfileid: "67543810"
---
# <a name="deploy-a-deep-learning-model-for-inference-with-gpu"></a>部署推斷配備 GPU 的深度學習模型

這篇文章會教導您如何使用 Azure Machine Learning 服務來部署深度學習模型作為 web 服務已啟用 GPU 功能 Tensorflow。

將您的模型部署至 Azure Kubernetes Service (AKS) 叢集來執行已啟用 GPU 的推斷。 推斷，或模型計分，是已部署的模型，用於預測的階段。 使用 Gpu，而不可高度平行化的計算上的 Cpu 提供效能優點。

雖然這個範例會使用 TensorFlow 模型，您可以套用任何機器學習架構，可支援 Gpu 對評分檔案及環境檔案進行小變更下列步驟。 

在本文中，您會執行下列步驟：

* 建立已啟用 GPU 的 AKS 叢集
* 部署 GPU Tensorflow 模型
* 發給您已部署的模型中的範例查詢

## <a name="prerequisites"></a>必要條件

* Azure 機器學習服務工作區中。
* Python 散發套件。
* 已註冊的 Tensorflow 儲存模型。
    * 若要了解如何註冊模型，請參閱[部署模型](../service/how-to-deploy-and-where.md#registermodel)。

您可以完成本使用說明的系列的第一部分[如何定型 TensorFlow 模型](how-to-train-tensorflow.md)，以滿足所需的必要條件。

## <a name="provision-an-aks-cluster-with-gpus"></a>佈建以 Gpu AKS 叢集

Azure 有許多不同的 GPU 選項。 您可以使用其中任何的推斷。 請參閱[的 N 系列 Vm 清單](https://azure.microsoft.com/pricing/details/virtual-machines/linux/#n-series)如需完整的功能和成本解析。

如需有關使用 AKS 使用 Azure Machine Learning 服務的詳細資訊，請參閱[如何部署和位置](../service/how-to-deploy-and-where.md#deploy-aks)。

```Python
# Choose a name for your cluster
aks_name = "aks-gpu"

# Check to see if the cluster already exists
try:
    compute_target = ComputeTarget(workspace=ws, name=aks_name)
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
> Azure 會向您收費，只要 AKS 叢集中已佈建。 請務必刪除您的 AKS 叢集，當您完成時使用它。

## <a name="write-the-entry-script"></a>寫入項目指令碼

將下列程式碼儲存至您的工作目錄為`score.py`。 此檔案分數的映像，因為它們會被傳送到您的服務。 該載入儲存的 TensorFlow 模型、 每個 POST 要求傳遞到 TensorFlow 的工作階段輸入的映像，然後傳回產生的分數。 其他推斷架構需要不同的評分檔案。

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
## <a name="define-the-conda-environment"></a>定義的 conda 環境

建立名為的 conda 環境檔案`myenv.yml`到指定的相依性服務。 請務必指定您要使用`tensorflow-gpu`達到加速的效能。

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

建立`InferenceConfig`啟用 Gpu 並確保 CUDA 會隨您的 Docker 映像的物件。

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

## <a name="issue-a-sample-query-to-your-model"></a>發給您的模型中的範例查詢

將測試查詢傳送至部署的模型。 當您將 jpeg 影像傳送至模型時，它會分數映像。 下列程式碼範例會使用外部公用程式函式，載入影像。 您可以找到相關的程式碼在 pir [GitHub 上的 TensorFlow 範例](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-tensorflow/utils.py)。 

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
