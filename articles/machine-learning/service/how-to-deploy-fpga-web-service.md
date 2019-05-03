---
title: 在 FPGA 上部署模型
titleSuffix: Azure Machine Learning service
description: 了解如何使用 Azure Machine Learning 服務以 FPGA 上執行的模型來部署 Web 服務，以提供超低延遲的推斷。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: larryfr
ms.author: tedway
author: tedway
ms.date: 05/02/2019
ms.custom: seodec18
ms.openlocfilehash: cfe21d2119b92665c5950d792dec6500257c6316
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/02/2019
ms.locfileid: "65024171"
---
# <a name="deploy-a-model-as-a-web-service-on-an-fpga-with-azure-machine-learning-service"></a>使用 Azure Machine Learning 服務在 FPGA 上將模型部署為 Web 服務

您可以將模型部署為 web 服務上[欄位可程式化閘陣列 (Fpga)](concept-accelerate-with-fpgas.md)與 Azure Machine Learning 的硬體加速的模型。 使用 FPGA 時，即使是透過單一批次大小，也提供超低延遲推斷。

目前可使用以下模型：
  - ResNet 50
  - ResNet 152
  - DenseNet-121
  - VGG-16
  - SSD-VGG

Fpga 可在這些 Azure 區域中：
  - 美國東部
  - 美國西部 2
  - 西歐
  - 東南亞

> [!IMPORTANT]
> 若要最佳化延遲和輸送量，您將資料傳送到 FPGA 模型的用戶端應該在其中一個區域上方 （您已部署之模型的那一個）。

## <a name="prerequisites"></a>必要條件

- 如果您沒有 Azure 訂用帳戶，請在開始前先建立一個免費帳戶。 立即試用[免費或付費版本的 Azure Machine Learning 服務](https://aka.ms/AMLFree)。

- 已安裝 Azure Machine Learning services 工作區與適用於 Python 的 Azure Machine Learning SDK。 了解如何使用[如何設定開發環境](how-to-configure-environment.md)文件來取得這些必要條件。
 
  - 安裝 Python SDK 中的硬體加速的模型：

    ```shell
    pip install --upgrade azureml-accel-models
    ```

## <a name="sample-notebooks"></a>範例 Notebook

為了方便起見，[範例 notebook](https://aka.ms/aml-notebooks)適用於範例中，以下還有其他範例。  加速的模型，以查看作法-要-使用-azureml 和部署的資料夾底下。

## <a name="create-and-containerize-your-model"></a>建立並將容器化您的模型

本文件將說明如何建立前置處理輸入的映像，使其在 FPGA 中，使用 ResNet 50 featurizer TensorFlow 圖形，然後再執行透過 ImageNet 資料集上定型分類器的 功能。

依照指示執行以：

* 定義 TensorFlow 模型
* 部署模型
* 取用已部署的模型
* 刪除已部署的服務

### <a name="load-azure-ml-workspace"></a>載入 Azure ML 工作區

載入您的 Azure ML 工作區。

```python
import os
import tensorflow as tf
 
from azureml.core import Workspace
 
ws = Workspace.from_config()
print(ws.name, ws.resource_group, ws.location, ws.subscription_id, sep = '\n')
```

### <a name="preprocess-image"></a>預先處理映像

Web 服務的輸入是 JPEG 影像。  第一個步驟是解碼 JPEG 影像和前置處理它。  JPEG 影像會在字串和結果都將成為 ResNet 50 模型輸入的 tensors 視為。

```python
# Input images as a two-dimensional tensor containing an arbitrary number of images represented a strings
import azureml.accel.models.utils as utils
in_images = tf.placeholder(tf.string)
image_tensors = utils.preprocess_array(in_images)
print(image_tensors.shape)
```

### <a name="load-featurizer"></a>載入 featurizer

初始化模型並下載要做為功能化器之 ResNet50 量化版本的 TensorFlow 檢查點。  您可以藉由匯入其他深度類神經網路來取代"QuantizedResnet50 」 使用如下的程式碼片段：

- QuantizedResnet152
- QuantizedVgg16
- Densenet121

```python
from azureml.accel.models import QuantizedResnet50
save_path = os.path.expanduser('~/models')
model_graph = QuantizedResnet50(save_path, is_frozen = True)
feature_tensor = model_graph.import_graph_def(image_tensors)
print(model_graph.version)
print(feature_tensor.name)
print(feature_tensor.shape)
```

### <a name="add-classifier"></a>新增分類

此分類器已在 ImageNet 資料集上定型。  傳輸學習和訓練您的自訂的權數範例可在一組[範例 notebook](https://aka.ms/aml-notebooks)。

```python
classifier_output = model_graph.get_default_classifier(feature_tensor)
print(classifier_output)
```

### <a name="save-the-model"></a>儲存模型

既然已載入前置處理器、 ResNet 50 featurizer 並分類器，將儲存的圖形和相關聯的變數為模型。

```python
model_name = "resnet50"
model_def_path = os.path.join(save_path, model_name)
print("Saving model in {}".format(model_def_path))

with tf.Session() as sess:
    model_graph.restore_weights(sess)
    tf.saved_model.simple_save(sess, model_def_path,
                                   inputs={'images': in_images},
                                   outputs={'output_alias': classifier_output})
```

### <a name="register-model"></a>註冊模型

[註冊](./concept-model-management-and-deployment.md)您所建立的模型。  加入標記和有關模型的其他中繼資料，可協助您追蹤您已訓練的模型。

```python
from azureml.core.model import Model

registered_model = Model.register(workspace = ws
                                  model_path = model_def_path,
                                  model_name = model_name)

print("Successfully registered: ", registered_model.name, registered_model.description, registered_model.version, sep = '\t')
```

如果您已註冊模型，而想要將其載入，您可能會擷取它。

```python
from azureml.core.model import Model
model_name = "resnet50"
# By default, the latest version is retrieved. You can specify the version, i.e. version=1
registered_model = Model(ws, name="resnet50")
print(registered_model.name, registered_model.description, registered_model.version, sep = '\t')
```

### <a name="convert-model"></a>轉換模型

TensorFlow 圖形必須轉換成開放的類神經網路交換格式 ([ONNX](https://onnx.ai/))。  您必須提供的輸入和輸出 tensors 中，名稱，當您使用 web 服務時，將您的用戶端使用這些名稱。

```python
input_tensor = in_images.name
output_tensors = classifier_output.name

print(input_tensor)
print(output_tensors)


from azureml.accel.accel_onnx_converter import AccelOnnxConverter

convert_request = AccelOnnxConverter.convert_tf_model(ws, registered_model, input_tensor, output_tensors)
convert_request.wait_for_completion(show_output=True)

# If the above call succeeded, get the converted model
converted_model = convert_request.result
print(converted_model.name, converted_model.url, converted_model.version, converted_model.id,converted_model.created_time)
```

### <a name="create-docker-image"></a>建立 Docker 映像

已轉換的模型及其所有相依性會新增至 Docker 映像中。  此 Docker 映像可以再部署並在雲端或支援的 edge 裝置中這類執行個體化[Azure 資料方塊邊緣](https://docs.microsoft.com/azure/databox-online/data-box-edge-overview)。  您也可以加入標記和描述，您已註冊的 Docker 映像。

```python
from azureml.core.image import Image
from azureml.accel.accel_container_image import AccelContainerImage

image_config = AccelContainerImage.image_configuration()
image_name = "{}-image".format(model_name)

image = Image.create(name = image_name,
                     models = [converted_model],
                     image_config = image_config, 
                     workspace = ws)


image.wait_for_creation(show_output = True)
```

依標籤列出映像，並取得詳細的記錄任何偵錯。

```python
for i in Image.list(workspace = ws):
    print('{}(v.{} [{}]) stored at {} with build log {}'.format(i.name, i.version, i.creation_state, i.image_location, i.image_build_log_uri))
```

## <a name="model-deployment"></a>模型部署

### <a name="deploy-to-the-cloud"></a>部署至雲端

若要將模型部署為大規模生產 Web 服務，請使用 Azure Kubernetes Service (AKS)。 您可以建立新的資源使用 Azure 機器學習服務 SDK、 CLI 或 Azure 入口網站。

```python
# Use the default configuration (can also provide parameters to customize)
prov_config = AksCompute.provisioning_configuration()

aks_name = 'my-aks-9' 
# Create the cluster
aks_target = ComputeTarget.create(workspace = ws, 
                                  name = aks_name, 
                                  provisioning_configuration = prov_config)

%%time
aks_target.wait_for_completion(show_output = True)
print(aks_target.provisioning_state)
print(aks_target.provisioning_errors)

#Set the web service configuration (using default here)
aks_config = AksWebservice.deploy_configuration()

%%time
aks_service_name ='aks-service-1'

aks_service = Webservice.deploy_from_image(workspace = ws, 
                                           name = aks_service_name,
                                           image = image,
                                           deployment_config = aks_config,
                                           deployment_target = aks_target)
aks_service.wait_for_deployment(show_output = True)
print(aks_service.state)
print(aks_service.scoring_uri)
```

#### <a name="test-the-cloud-service"></a>測試雲端服務
Docker 映像支援 gRPC 和 TensorFlow 提供 「 預測 」 API。  使用範例用戶端來呼叫進入 Docker 映像，若要從模型取得預測。  範例用戶端程式碼有：
- [Python](https://github.com/Azure/aml-real-time-ai/blob/master/pythonlib/amlrealtimeai/client.py)
- [C#](https://github.com/Azure/aml-real-time-ai/blob/master/sample-clients/csharp)

如果您想要使用 TensorFlow 服務，您可以[下載範例用戶端](https://www.tensorflow.org/serving/setup)。

```python
import requests
classes_entries = requests.get("https://raw.githubusercontent.com/Lasagne/Recipes/master/examples/resnet50/imagenet_classes.txt").text.splitlines()

# Score image using input and output tensor names
results = client.score_file(path="./snowleopardgaze.jpg", 
                             input_name=input_tensor, 
                             outputs=output_tensors)

# map results [class_id] => [confidence]
results = enumerate(results)
# sort results by confidence
sorted_results = sorted(results, key=lambda x: x[1], reverse=True)
# print top 5 results
for top in sorted_results[:5]:
    print(classes_entries[top[0]], 'confidence:', top[1])
```

### <a name="clean-up-the-service"></a>清除服務
刪除您的 web 服務、 影像和模型 （必須以下列順序由於有相依性）。

```python
aks_service.delete()
image.delete()
registered_model.delete()
converted_model.delete()
```

## <a name="deploy-to-a-local-edge-server"></a>部署至本機的邊緣伺服器

所有[Azure 資料方塊的邊緣裝置](https://docs.microsoft.com/azure/databox-online/data-box-edge-overview
)包含 FPGA 執行模型。  只有一個模型可以在 FPGA 中一次執行。  若要執行不同的模型，只需將部署新的容器。 指示和範例程式碼可在[這個 Azure 範例](https://github.com/Azure-Samples/aml-hardware-accelerated-models)。

## <a name="secure-fpga-web-services"></a>保護 FPGA Web 服務

如需 FPGA Web 服務安全保護的相關資訊，請參閱[使用 SSL 來保護具有 Azure Machine Learning 服務的 Web 服務](how-to-secure-web-service.md) \(英文\) 一文。