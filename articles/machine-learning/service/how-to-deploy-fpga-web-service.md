---
title: 在 FPGA 上部署模型
titleSuffix: Azure Machine Learning service
description: 瞭解如何使用在 FPGA 上執行的模型來部署 web 服務, 以 Azure Machine Learning 服務進行 ultra 低延遲推斷。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: larryfr
ms.author: tedway
author: tedway
ms.date: 07/25/2019
ms.custom: seodec18
ms.openlocfilehash: 5e780c1a1d18954d0b3e9413e26f478e4ea3a8b1
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/08/2019
ms.locfileid: "68856096"
---
# <a name="deploy-a-model-as-a-web-service-on-an-fpga-with-azure-machine-learning-service"></a>使用 Azure Machine Learning 服務在 FPGA 上將模型部署為 Web 服務

您可以使用 Azure Machine Learning 硬體加速模型, 將模型部署為現場可程式化網[關陣列 (fpga)](concept-accelerate-with-fpgas.md)上的 web 服務。 使用 Fpga 可提供超低延遲推斷, 即使使用單一批次大小也一樣。 推斷 (或模型計分) 是已部署的模型用於預測的階段, 最常見的是生產資料。

目前可使用以下模型：
  - ResNet 50
  - ResNet 152
  - DenseNet-121
  - VGG-16
  - SSD-VGG

Fpga 可在下列 Azure 區域中使用:
  - East US
  - 東南亞
  - 西歐
  - 美國西部 2

> [!IMPORTANT]
> 若要將延遲和輸送量優化, 將資料傳送至 FPGA 模型的用戶端應該位於上述其中一個區域 (您已將模型部署至其中一個)。

## <a name="prerequisites"></a>先決條件

- Azure 訂用帳戶。  如果您沒有, 請在開始前建立免費帳戶。 立即試用[免費或付費版本的 Azure Machine Learning 服務](https://aka.ms/AMLFree)。

- FPGA 配額。 使用 Azure CLI 檢查您是否有配額:

    ```shell
    az vm list-usage --location "eastus" -o table --query "[?localName=='Standard PBS Family vCPUs']"
    ```

    > [!TIP]
    > 其他可能的位置為``southeastasia``、 ``westeurope``和``westus2``。

    命令會傳回類似下面的文字:

    ```text
    CurrentValue    Limit    LocalName
    --------------  -------  -------------------------
    0               6        Standard PBS Family vCPUs
    ```

    請確定您在__CurrentValue__下至少有6個個 vcpu。

    如果您沒有配額, 請在上[https://aka.ms/accelerateAI](https://aka.ms/accelerateAI)提交要求。

- 已安裝 Azure Machine Learning services 工作區與適用於 Python 的 Azure Machine Learning SDK。 如需詳細資訊, 請參閱[建立工作區](how-to-manage-workspace.md)。
 
- 適用于硬體加速模型的 Python SDK:

    ```shell
    pip install --upgrade azureml-accel-models
    ```

## <a name="sample-notebooks"></a>範例 Notebook

為了方便起見, 範例[筆記本](https://aka.ms/aml-accel-models-notebooks)適用于下列範例和其他範例。

## <a name="create-and-containerize-your-model"></a>建立和容器化您的模型

本檔將說明如何建立 TensorFlow 圖形來預先處理輸入影像、將它設為在 FPGA 上使用 ResNet 50 的 featurizer, 然後透過針對 ImageNet 資料集定型的分類器來執行功能。

依照指示執行以：

* 定義 TensorFlow 模型
* 轉換模型
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
print(ws.name, ws.resource_group, ws.location, ws.subscription_id, sep='\n')
```

### <a name="preprocess-image"></a>預先處理映像

Web 服務的輸入是 JPEG 影像。  第一個步驟是將 JPEG 影像解碼並進行前置處理。  JPEG 影像會視為字串, 而結果會 tensors, 而這會是 ResNet 50 模型的輸入。

```python
# Input images as a two-dimensional tensor containing an arbitrary number of images represented a strings
import azureml.accel.models.utils as utils
tf.reset_default_graph()

in_images = tf.placeholder(tf.string)
image_tensors = utils.preprocess_array(in_images)
print(image_tensors.shape)
```

### <a name="load-featurizer"></a>Load featurizer

初始化模型並下載要做為功能化器之 ResNet50 量化版本的 TensorFlow 檢查點。  您可以藉由匯入其他深度類神經網路, 取代下列程式碼片段中的 "QuantizedResnet50":

- QuantizedResnet152
- QuantizedVgg16
- Densenet121

```python
from azureml.accel.models import QuantizedResnet50
save_path = os.path.expanduser('~/models')
model_graph = QuantizedResnet50(save_path, is_frozen=True)
feature_tensor = model_graph.import_graph_def(image_tensors)
print(model_graph.version)
print(feature_tensor.name)
print(feature_tensor.shape)
```

### <a name="add-classifier"></a>加入分類器

此分類器已在 ImageNet 資料集上定型。  您可以在[範例筆記本](https://aka.ms/aml-notebooks)集合中取得轉移學習和訓練自訂權數的範例。

```python
classifier_output = model_graph.get_default_classifier(feature_tensor)
print(classifier_output)
```

### <a name="save-the-model"></a>儲存模型

現在已載入預處理器、ResNet 50 featurizer 和分類器, 請將圖形和關聯的變數儲存為模型。

```python
model_name = "resnet50"
model_save_path = os.path.join(save_path, model_name)
print("Saving model in {}".format(model_save_path))

with tf.Session() as sess:
    model_graph.restore_weights(sess)
    tf.saved_model.simple_save(sess, model_save_path,
                               inputs={'images': in_images},
                               outputs={'output_alias': classifier_output})
```

### <a name="save-input-and-output-tensors"></a>儲存輸入和輸出 tensors
在前置處理和分類器步驟期間建立的輸入和輸出 tensors, 將需要進行模型轉換和推斷。

```python
input_tensors = in_images.name
output_tensors = classifier_output.name

print(input_tensors)
print(output_tensors)
```

> [!IMPORTANT]
> 儲存輸入和輸出 tensors, 因為您需要它們才能進行模型轉換和推斷要求。

可用的模型和對應的預設分類器輸出 tensors 如下所示, 如果您使用預設分類器, 這就是您要用於推斷的結果。

+ Resnet50、QuantizedResnet50
  ```python
  output_tensors = "classifier_1/resnet_v1_50/predictions/Softmax:0"
  ```
+ Resnet152, QuantizedResnet152
  ```python
  output_tensors = "classifier/resnet_v1_152/predictions/Softmax:0"
  ```
+ Densenet121, QuantizedDensenet121
  ```python
  output_tensors = "classifier/densenet121/predictions/Softmax:0"
  ```
+ Vgg16, QuantizedVgg16
  ```python
  output_tensors = "classifier/vgg_16/fc8/squeezed:0"
  ```
+ SsdVgg, QuantizedSsdVgg
  ```python
  output_tensors = ['ssd_300_vgg/block4_box/Reshape_1:0', 'ssd_300_vgg/block7_box/Reshape_1:0', 'ssd_300_vgg/block8_box/Reshape_1:0', 'ssd_300_vgg/block9_box/Reshape_1:0', 'ssd_300_vgg/block10_box/Reshape_1:0', 'ssd_300_vgg/block11_box/Reshape_1:0', 'ssd_300_vgg/block4_box/Reshape:0', 'ssd_300_vgg/block7_box/Reshape:0', 'ssd_300_vgg/block8_box/Reshape:0', 'ssd_300_vgg/block9_box/Reshape:0', 'ssd_300_vgg/block10_box/Reshape:0', 'ssd_300_vgg/block11_box/Reshape:0']
  ```

### <a name="register-model"></a>註冊模型

[註冊](./concept-model-management-and-deployment.md)您所建立的模型。  加入標記和與模型相關的其他中繼資料, 可協助您追蹤已定型的模型。

```python
from azureml.core.model import Model

registered_model = Model.register(workspace=ws,
                                  model_path=model_save_path,
                                  model_name=model_name)

print("Successfully registered: ", registered_model.name,
      registered_model.description, registered_model.version, sep='\t')
```

如果您已經註冊模型, 而且想要將它載入, 您可以將它取出。

```python
from azureml.core.model import Model
model_name = "resnet50"
# By default, the latest version is retrieved. You can specify the version, i.e. version=1
registered_model = Model(ws, name="resnet50")
print(registered_model.name, registered_model.description,
      registered_model.version, sep='\t')
```

### <a name="convert-model"></a>轉換模型

將 TensorFlow 圖形轉換成 Open Neural Network Exchange 格式 ([ONNX](https://onnx.ai/))。  您將需要提供輸入和輸出 tensors 的名稱, 而您的用戶端將會在取用 web 服務時使用這些名稱。

```python
from azureml.accel import AccelOnnxConverter

convert_request = AccelOnnxConverter.convert_tf_model(
    ws, registered_model, input_tensors, output_tensors)

# If it fails, you can run wait_for_completion again with show_output=True.
convert_request.wait_for_completion(show_output=False)

# If the above call succeeded, get the converted model
converted_model = convert_request.result
print("\nSuccessfully converted: ", converted_model.name, converted_model.url, converted_model.version,
      converted_model.id, converted_model.created_time, '\n')
```

### <a name="create-docker-image"></a>建立 Docker 映射

已轉換的模型和所有相依性都會新增至 Docker 映射。  然後可以部署並具現化此 Docker 映射。  支援的部署目標包括雲端中的 AKS 或邊緣裝置 (例如[Azure Data Box Edge](https://docs.microsoft.com/azure/databox-online/data-box-edge-overview))。  您也可以為已註冊的 Docker 映射新增標記和描述。

```python
from azureml.core.image import Image
from azureml.accel import AccelContainerImage

image_config = AccelContainerImage.image_configuration()
# Image name must be lowercase
image_name = "{}-image".format(model_name)

image = Image.create(name=image_name,
                     models=[converted_model],
                     image_config=image_config,
                     workspace=ws)
image.wait_for_creation(show_output=False)
```

依標記列出影像, 並取得詳細的記錄以進行任何偵錯工具。

```python
for i in Image.list(workspace=ws):
    print('{}(v.{} [{}]) stored at {} with build log {}'.format(
        i.name, i.version, i.creation_state, i.image_location, i.image_build_log_uri))
```

## <a name="model-deployment"></a>模型部署

### <a name="deploy-to-the-cloud"></a>部署到雲端

若要將模型部署為大規模生產 Web 服務，請使用 Azure Kubernetes Service (AKS)。 您可以使用 Azure Machine Learning SDK、CLI 或 Azure 入口網站建立新的。

```python
from azureml.core.compute import AksCompute, ComputeTarget

# Specify the Standard_PB6s Azure VM
prov_config = AksCompute.provisioning_configuration(vm_size="Standard_PB6s",
                                                    agent_count=1)

aks_name = 'my-aks-cluster'
# Create the cluster
aks_target = ComputeTarget.create(workspace=ws,
                                  name=aks_name,
                                  provisioning_configuration=prov_config)
```

AKS 部署可能需要大約15分鐘的時間。  查看部署是否成功。

```python
aks_target.wait_for_completion(show_output=True)
print(aks_target.provisioning_state)
print(aks_target.provisioning_errors)
```

將容器部署至 AKS 叢集。
```python
from azureml.core.webservice import Webservice, AksWebservice

# For this deployment, set the web service configuration without enabling auto-scaling or authentication for testing
aks_config = AksWebservice.deploy_configuration(autoscale_enabled=False,
                                                num_replicas=1,
                                                auth_enabled=False)

aks_service_name = 'my-aks-service'

aks_service = Webservice.deploy_from_image(workspace=ws,
                                           name=aks_service_name,
                                           image=image,
                                           deployment_config=aks_config,
                                           deployment_target=aks_target)
aks_service.wait_for_deployment(show_output=True)
```

#### <a name="test-the-cloud-service"></a>測試雲端服務
Docker 映射支援 gRPC 和 TensorFlow 服務「預測」 API。  使用範例用戶端呼叫 Docker 映射, 以從模型取得預測。  可用的範例用戶端程式代碼:
- [Python](https://github.com/Azure/aml-real-time-ai/blob/master/pythonlib/amlrealtimeai/client.py)
- [C#](https://github.com/Azure/aml-real-time-ai/blob/master/sample-clients/csharp)

如果您想要使用 TensorFlow 服務, 您可以[下載範例用戶端](https://www.tensorflow.org/serving/setup)。

```python
# Using the grpc client in Azure ML Accelerated Models SDK package
from azureml.accel import PredictionClient

address = aks_service.scoring_uri
ssl_enabled = address.startswith("https")
address = address[address.find('/')+2:].strip('/')
port = 443 if ssl_enabled else 80

# Initialize AzureML Accelerated Models client
client = PredictionClient(address=address,
                          port=port,
                          use_ssl=ssl_enabled,
                          service_name=aks_service.name)
```

因為此分類器已在[ImageNet](http://www.image-net.org/)資料集上定型, 所以將類別對應到人們可讀取的標籤。

```python
import requests
classes_entries = requests.get(
    "https://raw.githubusercontent.com/Lasagne/Recipes/master/examples/resnet50/imagenet_classes.txt").text.splitlines()

# Score image with input and output tensor names
results = client.score_file(path="./snowleopardgaze.jpg",
                            input_name=input_tensors,
                            outputs=output_tensors)

# map results [class_id] => [confidence]
results = enumerate(results)
# sort results by confidence
sorted_results = sorted(results, key=lambda x: x[1], reverse=True)
# print top 5 results
for top in sorted_results[:5]:
    print(classes_entries[top[0]], 'confidence:', top[1])
```

### <a name="clean-up-the-service"></a>清理服務
刪除您的 web 服務、映射和模型 (必須依照此順序完成, 因為有相依性)。

```python
aks_service.delete()
aks_target.delete()
image.delete()
registered_model.delete()
converted_model.delete()
```

## <a name="deploy-to-a-local-edge-server"></a>部署到本機邊緣伺服器

所有[Azure Data Box Edge 裝置](https://docs.microsoft.com/azure/databox-online/data-box-edge-overview
)都包含執行模型的 FPGA。  FPGA 一次只能執行一個模型。  若要執行不同的模型, 請直接部署新的容器。 您可以在[此 Azure 範例](https://github.com/Azure-Samples/aml-hardware-accelerated-models)中找到指示和範例程式碼。

## <a name="secure-fpga-web-services"></a>保護 FPGA Web 服務

如需 FPGA Web 服務安全保護的相關資訊，請參閱[使用 SSL 來保護具有 Azure Machine Learning 服務的 Web 服務](how-to-secure-web-service.md) \(英文\) 一文。

## <a name="pbs-family-vms"></a>PBS 系列 Vm

Azure Vm 的 PBS 系列包含 Intel Arria 10 Fpga。  當您檢查 Azure 配額配置時, 它會顯示為「標準的 PBS 系列個 vcpu」。  PB6 VM 有六個個 vcpu 和一個 FPGA, 而且它會在將模型部署至 FPGA 時, 自動由 Azure ML 布建。  此檔案僅適用于 Azure ML, 而且無法執行任意 bitstreams。  例如, 您將無法使用 bitstreams 來快閃 FPGA 來執行加密、編碼等動作。 
