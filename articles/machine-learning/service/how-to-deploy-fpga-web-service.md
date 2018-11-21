---
title: 使用 Azure Machine Learning 在 FPGA 上將模型部署為 Web 服務
description: 了解如何使用 Azure Machine Learning 以 FPGA 上執行的模型來部署 Web 服務。
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: tedway
author: tedway
ms.date: 10/29/2018
ms.openlocfilehash: 35b24dc2f21e2504432521c53e2d0f41caa0eeb7
ms.sourcegitcommit: 96527c150e33a1d630836e72561a5f7d529521b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/09/2018
ms.locfileid: "51344054"
---
# <a name="deploy-a-model-as-a-web-service-on-an-fpga-with-azure-machine-learning"></a>使用 Azure Machine Learning 在 FPGA 上將模型部署為 Web 服務

您可以將模型部署為[現場可程式化邏輯閘陣列 (FPGA)](concept-accelerate-with-fpgas.md) 上的 Web 服務。  使用 FPGA 時，即使是透過單一批次大小，也提供超低延遲推斷。   

## <a name="prerequisites"></a>必要條件

- Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

- 已安裝 Azure Machine Learning services 工作區與適用於 Python 的 Azure Machine Learning SDK。 了解如何使用[如何設定開發環境](how-to-configure-environment.md)文件來取得這些必要條件。
 
  - 您的工作區必須位於*美國東部 2* 區域。

  - 安裝 contrib extras：

    ```shell
    pip install --upgrade azureml-sdk[contrib]
    ```  

## <a name="create-and-deploy-your-model"></a>建立及部署您的模型
建立管道以預先處理輸入映像、在 FPGA 上使用 ResNet 50 將它功能化，然後透過在 ImageNet 資料集上定型的分類器執行功能。

依照指示執行以：

* 定義模型管道
* 部署模型
* 取用已部署的模型
* 刪除已部署的服務

> [!IMPORTANT]
> 若要將延遲和輸送量最佳化，您的用戶端應該位於與端點相同 Azure 區域中。  目前 API 是在美國東部的 Azure 區域中建立的。



### <a name="preprocess-image"></a>預先處理映像
管道的第一個步驟是預先處理映像。

```python
import os
import tensorflow as tf

# Input images as a two-dimensional tensor containing an arbitrary number of images represented a strings
import azureml.contrib.brainwave.models.utils as utils
in_images = tf.placeholder(tf.string)
image_tensors = utils.preprocess_array(in_images)
print(image_tensors.shape)
```

### <a name="add-featurizer"></a>加入功能化器
初始化模型並下載要做為功能化器之 ResNet50 量化版本的 TensorFlow 檢查點。

```python
from azureml.contrib.brainwave.models import QuantizedResnet50, Resnet50
model_path = os.path.expanduser('~/models')
model = QuantizedResnet50(model_path, is_frozen = True)
feature_tensor = model.import_graph_def(image_tensors)
print(model.version)
print(feature_tensor.name)
print(feature_tensor.shape)
```

### <a name="add-classifier"></a>新增分類器
此分類器已在 ImageNet 資料集上定型。

```python
classifier_input, classifier_output = Resnet50.get_default_classifier(feature_tensor, model_path)
```

### <a name="create-service-definition"></a>建立服務定義
您現在已定義映像預先處理、功能化器與在服務上執行的分類器，您可以建立服務定義。 服務定義是從部署到 FPGA 服務之模型產生的一組檔案。 服務定義由管道組成。 管道是依序執行的一系列階段。  支援 TensorFlow 階段、Keras 階段與 BrainWave 階段。  階段會在服務上依序執行，而且每個階段的輸出會做為後續階段的輸入。

若要建立 TensorFlow 階段，請指定包含圖形 (在此案例中會使用預設圖形) 和此階段之輸入與輸出 tensors 的工作階段。  此資訊會用來將儲存圖形，以便它可以在服務上執行。

```python
from azureml.contrib.brainwave.pipeline import ModelDefinition, TensorflowStage, BrainWaveStage

save_path = os.path.expanduser('~/models/save')
model_def_path = os.path.join(save_path, 'service_def.zip')

model_def = ModelDefinition()
with tf.Session() as sess:
    model_def.pipeline.append(TensorflowStage(sess, in_images, image_tensors))
    model_def.pipeline.append(BrainWaveStage(sess, model))
    model_def.pipeline.append(TensorflowStage(sess, classifier_input, classifier_output))
    model_def.save(model_def_path)
    print(model_def_path)
```

### <a name="deploy-model"></a>部署模型
從服務定義建立服務。  您的工作區必須位於 美國東部 2 位置。

```python
from azureml.core import Workspace

ws = Workspace.from_config()
print(ws.name, ws.resource_group, ws.location, ws.subscription_id, sep = '\n')

from azureml.core.model import Model
model_name = "resnet-50-rtai"
registered_model = Model.register(ws, model_def_path, model_name)

from azureml.core.webservice import Webservice
from azureml.exceptions import WebserviceException
from azureml.contrib.brainwave import BrainwaveWebservice, BrainwaveImage
service_name = "imagenet-infer"
service = None
try:
    service = Webservice(ws, service_name)
except WebserviceException:
    image_config = BrainwaveImage.image_configuration()
    deployment_config = BrainwaveWebservice.deploy_configuration()
    service = Webservice.deploy_from_model(ws, service_name, [registered_model], image_config, deployment_config)
    service.wait_for_deployment(true)
```

### <a name="test-the-service"></a>測試服務
若要將影像傳送到 API 並測試回應，請新增從輸出類別識別碼到 ImageNet 類別名稱的對應。

```python
import requests
classes_entries = requests.get("https://raw.githubusercontent.com/Lasagne/Recipes/master/examples/resnet50/imagenet_classes.txt").text.splitlines()
```

呼叫您的服務並將下面的 "your-image.jpg" 檔案名稱取代為來自您電腦的影像。 

```python
with open('your-image.jpg') as f:
    results = service.run(f)
# map results [class_id] => [confidence]
results = enumerate(results)
# sort results by confidence
sorted_results = sorted(results, key=lambda x: x[1], reverse=True)
# print top 5 results
for top in sorted_results[:5]:
    print(classes_entries[top[0]], 'confidence:', top[1])
``` 

### <a name="clean-up-service"></a>清除服務
刪除服務。

```python
service.delete()
    
registered_model.delete()
```

## <a name="secure-fpga-web-services"></a>保護 FPGA Web 服務

在 FPGA 上執行的 Azure Machine Learning 模型提供 SSL 支援和金鑰型驗證。 這可讓您限制對服務的存取，並保護用戶端所提交的資料。 [了解如何保護 Web 服務](how-to-secure-web-service.md)。


## <a name="sample-notebook"></a>範例 Notebook

本文中的概念會在 [project-brainwave/project-brainwave-quickstart.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/project-brainwave/project-brainwave-quickstart.ipynb) Notebook 中示範。

請取得此筆記本：

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>後續步驟

了解如何[取用部署為 Web 服務的 ML 模型](how-to-consume-web-service.md)。