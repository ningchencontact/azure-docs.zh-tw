---
title: 部署模型作為 Web 服務
titleSuffix: Azure Machine Learning service
description: 了解部署 Azure Machine Learning 服務模型的方式和位置，這類模型包括：Azure 容器執行個體、Azure Kubernetes Service、Azure IoT Edge 或可現場程式化閘道陣列。
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: 4d62885743a4e50ece1c032c7b3405d8766d95cd
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/24/2019
ms.locfileid: "54850581"
---
# <a name="deploy-models-with-the-azure-machine-learning-service"></a>使用 Azure Machine Learning 服務部署模型

Azure Machine Learning 服務會使用 SDK 來提供數種可部署已定型模型的方式。 在此文件中，了解如何將模型部署為 Azure 雲端中的 Web 服務，或部署到 IoT 邊緣裝置。

> [!IMPORTANT]
> 將模型部署為 Web 服務時，目前不支援跨原始來源資源共用 (CORS)。

您可以將模型部署到下列計算目標：

| 計算目標 | 部署類型 | 說明 |
| ----- | ----- | ----- |
| [Azure 容器執行個體 (ACI)](#aci) | Web 服務 | 快速部署。 適合用於開發或測試。 |
| [Azure Kubernetes Service (AKS)](#aks) | Web 服務 | 適用於大規模生產環境部署。 提供自動調整和快速的回應時間。 |
| [Azure IoT Edge](#iotedge) | IoT 模組 | 在 IoT 裝置上部署模型。 裝置上會發生推斷。 |
| [可現場程式化閘道陣列 (FPGA)](#fpga) | Web 服務 | 即時推斷的超低延遲。 |

部署模型的程序與所有計算目標類似：

1. 定型和註冊模型。
1. 設定和註冊使用模型的映像。
1. 將映像部署到計算目標。
1. 測試部署

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2Kwk3]


如需部署工作流程中相關概念的詳細資訊，請參閱[使用 Azure Machine Learning 服務來管理、部署及監視模型](concept-model-management-and-deployment.md)。

## <a name="prerequisites"></a>必要條件

- Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前先建立一個免費帳戶。 立即試用[免費或付費版本的 Azure Machine Learning 服務](http://aka.ms/AMLFree)。

- 已安裝 Azure Machine Learning services 工作區與適用於 Python 的 Azure Machine Learning SDK。 使用[開始使用 Azure Machine Learning 快速入門](quickstart-get-started.md)了解如何取得這些必要條件。

- 已定型的模型。 如果您沒有已定型的模型，請使用[將模型定型](tutorial-train-models-with-aml.md)教學課程中的步驟，利用 Azure Machine Learning 服務來定型並註冊一個模型。

    > [!NOTE]
    > 雖然 Azure Machine Learning 服務可搭配任何能夠在 Python 3 中載入的一般模型運作，但本文件中的範例示範使用以 Pickle 格式儲存的模型。
    > 
    > 如需使用 ONNX 模型的詳細資訊，請參閱 [ONNX 和 Azure Machine Learning](how-to-build-deploy-onnx.md) 文件。

## <a id="registermodel"></a> 註冊已定型的模型

模型註冊是在 Azure 雲端中儲存和組織已定型模型的方法。 模型是註冊在您的 Azure Machine Learning 服務工作區中。 您可以使用 Azure Machine Learning 或另一個服務來將模型定型。 若要從檔案註冊模型，請使用下列程式碼：

```python
from azureml.core.model import Model

model = Model.register(model_path = "model.pkl",
                       model_name = "Mymodel",
                       tags = {"key": "0.1"},
                       description = "test",
                       workspace = ws)
```

**估計時間**：大約 10 秒。

如需詳細資訊，請參閱 [Model 類別](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py) \(英文\) 的參考文件。

## <a id="configureimage"></a> 建立和註冊映像

已部署的模型會封裝為映像。 映像會包含執行模型所需的相依性。

針對 **Azure 容器執行個體**、**Azure Kubernetes Service** 及 **Azure IoT Edge** 部署，使用 [azureml.core.image.ContainerImage](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image.containerimage?view=azure-ml-py) 類別來建立映像設定。 接著使用映像設定來建立新的 Docker 映像。 

下列程式碼示範如何建立新的映像設定：

```python
from azureml.core.image import ContainerImage

# Image configuration
image_config = ContainerImage.image_configuration(execution_script = "score.py",
                                                 runtime = "python",
                                                 conda_file = "myenv.yml",
                                                 description = "Image with ridge regression model",
                                                 tags = {"data": "diabetes", "type": "regression"}
                                                 )
```

**估計時間**：大約 10 秒。

下表說明此範例中的重要參數：

| 參數 | 說明 |
| ----- | ----- |
| `execution_script` | 指定用來接收提交給服務之要求的 Python 指令碼。 在此範例中，指令碼包含在 `score.py` 檔案中。 如需詳細資訊，請參閱[執行指令碼](#script)一節。 |
| `runtime` | 指出映像使用 Python。 其他選項是 `spark-py`，其使用 Python 搭配 Apache Spark。 |
| `conda_file` | 用來提供 Conda 環境檔。 此檔案會為已部署的模型定義 conda 環境。 如需建立此檔案的詳細資訊，請參閱[建立環境檔案 (myenv.yml)](tutorial-deploy-models-with-aml.md#create-environment-file)。 |

如需詳細資訊，請參閱 [ContainerImage 類別](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image.containerimage?view=azure-ml-py) \(英文\) 的參考文件。

### <a id="script"></a> 執行指令碼

執行指令碼會接收提交給已部署映像的資料，並將它傳遞給模型。 然後，它會採用模型傳回的回應，並將該回應傳回至用戶端。 指令碼是模型專用的，其必須了解模型所預期和傳回的資料。 指令碼通常包含載入和執行模型的兩個函式：

* `init()`：此函式通常會將模型載入到全域物件。 此函式只會在 Docker 容器啟動時執行一次。 

* `run(input_data)`：此函式會使用模型，依據輸入資料來預測值。 執行的輸入和輸出通常使用 JSON 進行序列化和還原序列化。 您也可以使用原始的二進位資料。 您可以先轉換資料，再將資料傳送給模型或傳回用戶端。 

#### <a name="working-with-json-data"></a>使用 JSON 資料

下列範例指令碼會接受並傳回 JSON 資料。 `run` 函式會將資料從 JSON 轉換為模型預期的格式，然後將回應轉換為 JSON，再將回應傳回：

```python
# import things required by this script
import json
import numpy as np
import os
import pickle
from sklearn.externals import joblib
from sklearn.linear_model import LogisticRegression

from azureml.core.model import Model

# load the model
def init():
    global model
    # retrieve the path to the model file using the model name
    model_path = Model.get_model_path('sklearn_mnist')
    model = joblib.load(model_path)

# Passes data to the model and returns the prediction
def run(raw_data):
    data = np.array(json.loads(raw_data)['data'])
    # make prediction
    y_hat = model.predict(data)
    return json.dumps(y_hat.tolist())
```

#### <a name="working-with-binary-data"></a>使用二進位資料

如果您的模型接受__二進位資料__，請使用 `AMLRequest`、`AMLResponse` 和 `rawhttp`。 下列範例指令碼會接受二進位資料，並針對 POST 要求傳回反轉的位元組。 針對 GET 要求，它會在回應本文中傳回完整的 URL：

```python
from azureml.contrib.services.aml_request  import AMLRequest, rawhttp
from azureml.contrib.services.aml_response import AMLResponse

def init():
    print("This is init()")

# Accept and return binary data
@rawhttp
def run(request):
    print("This is run()")
    print("Request: [{0}]".format(request))
    # handle GET requests
    if request.method == 'GET':
        respBody = str.encode(request.full_path)
        return AMLResponse(respBody, 200)
    # handle POST requests
    elif request.method == 'POST':
        reqBody = request.get_data(False)
        respBody = bytearray(reqBody)
        respBody.reverse()
        respBody = bytes(respBody)
        return AMLResponse(respBody, 200)
    else:
        return AMLResponse("bad request", 500)
```

> [!IMPORTANT]
> `azureml.contrib` 命名空間會因為我們致力於改善本服務而經常變更。 因此，此命名空間中的任何項目均應被視為預覽，而且 Microsoft 不會完全支援。
>
> 如果您需要在本機開發環境中測試此項，您可以使用下列命令，在 `contrib` 命名空間中安裝元件： 
> ```shell
> pip install azureml-contrib-services
> ```

### <a id="createimage"></a> 註冊映像

當您建立映像設定之後，就可以使用它來註冊映像。 此映像會儲存於您工作區的容器登錄中。 建立之後，您就可以將同一個映像部署到多個服務。

```python
# Register the image from the image configuration
image = ContainerImage.create(name = "myimage", 
                              models = [model], #this is the model object
                              image_config = image_config,
                              workspace = ws
                              )
```

**估計時間**：約 3 分鐘。

當您使用相同名稱註冊多個映像時，即會自動為映像建立版本。 例如，會將識別碼 `myimage:1` 指派給註冊為 `myimage` 的第一個映像。 下一次當您將映像註冊為 `myimage` 時，新映像的識別碼會是 `myimage:2`。

如需詳細資訊，請參閱 [ContainerImage 類別](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image.containerimage?view=azure-ml-py) \(英文\) 的參考文件。

## <a id="deploy"></a> 部署映像

當您進行部署時，此程序會根據您部署到其中的計算目標而略微不同。 使用後續各節的資訊來了解如何部署到：

* [Azure 容器執行個體](#aci)
* [Azure Kubernetes Service](#aks)
* [Project Brainwave (可現場程式化閘道陣列)](#fpga)
* [Azure IoT Edge 裝置](#iotedge)

> [!NOTE]
> 當**部署為 Web 服務**時，有三個部署方法可以使用：
>
> | 方法 | 注意 |
> | ----- | ----- |
> | [deploy_from_image](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#deploy-from-image-workspace--name--image--deployment-config-none--deployment-target-none-) | 您必須先註冊模型並建立映像，然後才能使用此方法。 |
> | [deploy](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#deploy-workspace--name--model-paths--image-config--deployment-config-none--deployment-target-none-) | 使用此方法時，您不需要註冊模型或建立映像。 不過，您無法控制模型或映像的名稱，或相關聯的標記和描述。 |
> | [deploy_from_model](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#deploy-from-model-workspace--name--models--image-config--deployment-config-none--deployment-target-none-) | 使用此方法時，您不需要建立映像。 但您無法控制所建立的映像名稱。 |
>
> 本文件中的範例會使用 `deploy_from_image`。

### <a id="aci"></a> 部署到 Azure 容器執行個體

如果符合下列一或多個條件，請使用 Azure 容器執行個體來將您的模型部署為 Web 服務：

- 您需要快速部署及驗證模型。 ACI 部署會在 5 分鐘內完成。
- 您正在測試處於開發狀態的模型。 若要查看 ACI 的配額和區域可用性，請參閱 [Azure 容器執行個體的配額和區域可用性](https://docs.microsoft.com/azure/container-instances/container-instances-quotas)文件。

若要部署到 Azure 容器執行個體，請使用下列步驟：

1. 定義部署設定。 下列範例會定義一個設定來使用一個 CPU 核心和 1 GB 的記憶體：

    [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-deploy-to-aci/how-to-deploy-to-aci.py?name=configAci)]

2. 若要部署在本文[建立映像](#createimage)一節中所建立的映像，請使用下列程式碼，

    [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-deploy-to-aci/how-to-deploy-to-aci.py?name=option3Deploy)]

    **估計時間**：約 3 分鐘。

    > [!TIP]
    > 如果部署期間發生錯誤，請使用 `service.get_logs()` 來檢視服務記錄。 記錄資訊可能會指出發生錯誤的原因。

如需詳細資訊，請參閱 [AciWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py) \(英文\) 和 [Webservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice?view=azure-ml-py) \(英文\) 類別的參考文件。

### <a id="aks"></a> 部署到 Azure Kubernetes 服務

若要將模型部署為大規模生產 Web 服務，請使用 Azure Kubernetes Service (AKS)。 您可以使用現有的 AKS 叢集，或使用 Azure Machine Learning SDK、CLI 或 Azure 入口網站建立新的叢集。

建立 AKS 叢集是工作區的一次性程序。 您可以重複使用此叢集進行多個部署。 如果刪除叢集，就必須在下次需要部署時建立新的叢集。

Azure Kubernetes Service 提供以下功能：

* 自動調整
* 記錄
* 模型資料收集
* 快速的 Web 服務回應時間

#### <a name="create-a-new-cluster"></a>建立新叢集

若要建立新的 Azure Kubernetes Service 叢集，使用下列程式碼：

> [!IMPORTANT]
> 建立 AKS 叢集是工作區的一次性程序。 建立之後，就可以重複使用此叢集進行多個部署。 如果刪除叢集或包含該叢集的資源群組，就必須在下次需要部署時建立新的叢集。
> 針對 [`provisioning_configuration()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py)，如果您要為 agent_count 和 vm_size 挑選自訂值，則必須先確定 agent_count 乘以 vm_size 會大於或等於 12 個虛擬 CPU。 例如，如果您使用的 vm_size 為 "Standard_D3_v2" (其具有 4 個虛擬 CPU)，則您應該挑選等於或大於 3 的 agent_count。

```python
from azureml.core.compute import AksCompute, ComputeTarget

# Use the default configuration (you can also provide parameters to customize this)
prov_config = AksCompute.provisioning_configuration()

aks_name = 'aml-aks-1' 
# Create the cluster
aks_target = ComputeTarget.create(workspace = ws, 
                                    name = aks_name, 
                                    provisioning_configuration = prov_config)

# Wait for the create process to complete
aks_target.wait_for_completion(show_output = True)
print(aks_target.provisioning_state)
print(aks_target.provisioning_errors)
```

**估計時間**：約 20 分鐘。

#### <a name="use-an-existing-cluster"></a>使用現有的叢集

如果您在 Azure 訂用帳戶中已經有 AKS 叢集，而且它的版本為 1.11.*，則您可以使用它來部署映像。 下列程式碼示範如何將現有叢集附加至您的工作區：

```python
from azureml.core.compute import AksCompute, ComputeTarget
# Set the resource group that contains the AKS cluster and the cluster name
resource_group = 'myresourcegroup'
cluster_name = 'mycluster'

# Attach the cluster to your workgroup
attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                         cluster_name = cluster_name)
aks_target = ComputeTarget.attach(ws, 'mycompute', attach_config)

# Wait for the operation to complete
aks_target.wait_for_completion(True)
```

**估計時間**：約 3 分鐘。

#### <a name="deploy-the-image"></a>部署映像

若要將本文件[建立映像](#createimage)一節中所建立的映像部署至 Azure Kubernetes 伺服器叢集，請使用下列程式碼：

```python
from azureml.core.webservice import Webservice, AksWebservice

# Set configuration and service name
aks_config = AksWebservice.deploy_configuration()
aks_service_name ='aks-service-1'
# Deploy from image
service = Webservice.deploy_from_image(workspace = ws, 
                                            name = aks_service_name,
                                            image = image,
                                            deployment_config = aks_config,
                                            deployment_target = aks_target)
# Wait for the deployment to complete
service.wait_for_deployment(show_output = True)
print(service.state)
```

**估計時間**：約 3 分鐘。

> [!TIP]
> 如果部署期間發生錯誤，請使用 `service.get_logs()` 來檢視服務記錄。 記錄資訊可能會指出發生錯誤的原因。

如需詳細資訊，請參閱 [AksWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py) \(英文\) 和 [Webservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice.webservice?view=azure-ml-py) \(英文\) 類別的參考文件。

### <a id="fpga"></a> 部署到可現場程式化閘道陣列 (FPGA)

Project Brainwave 使其對於即時推斷要求能達到超低延遲。 Project Brainwave 加速了 Azure 雲端中部署在現場可程式化閘陣列上的深度類神經網路 (DNN)。 常用的 DNN 可供取得的形式有轉送學習的特徵化對象，或從您自己的資料定型所得之權重的可自訂項目。

如需使用 Project Brainwave 部署模型的逐步解說，請參閱[部署到 FPGA](how-to-deploy-fpga-web-service.md) 文件。

### <a id="iotedge"></a> 部署到 Azure IoT Edge

Azure IoT Edge 裝置是以 Linux 或 Windows 為基礎並執行 Azure IoT Edge 執行階段的裝置。 機器學習模型可以部署到這些裝置作為 IoT Edge 模組。 將模型部署到 IoT Edge 裝置可讓裝置直接使用模型，而不需要將資料傳送到雲端進行處理。 您可以獲得更快的回應時間並傳輸較少的資料。

Azure IoT Edge 模組會從容器登錄部署到您的裝置。 當您從模型建立映像時，它會儲存於您工作區的容器登錄中。

#### <a name="set-up-your-environment"></a>設定環境

* 開發環境。 如需詳細資訊，請參閱[如何設定開發環境](how-to-configure-environment.md)文件。

* Azure 訂用帳戶中的 [Azure IoT 中樞](../../iot-hub/iot-hub-create-through-portal.md)。 

* 已定型的模型。 如需模型定型方式範例，請參閱[使用 Azure Machine Learning 將映像分類模型定型](tutorial-train-models-with-aml.md)文件。 在[適用於 Azure IoT Edge 的 AI 工具組 GitHub 存放庫](https://github.com/Azure/ai-toolkit-iot-edge/tree/master/IoT%20Edge%20anomaly%20detection%20tutorial)中可取得預先定型的模型。

#### <a name="prepare-the-iot-device"></a>準備 IoT 裝置
您必須建立 IoT 中樞並註冊裝置，或利用[這段指令碼](https://raw.githubusercontent.com/Azure/ai-toolkit-iot-edge/master/amliotedge/createNregister)來重複使用您所擁有的裝置。

``` bash
ssh <yourusername>@<yourdeviceip>
sudo wget https://raw.githubusercontent.com/Azure/ai-toolkit-iot-edge/master/amliotedge/createNregister
sudo chmod +x createNregister
sudo ./createNregister <The Azure subscriptionID you want to use> <Resourcegroup to use or create for the IoT hub> <Azure location to use e.g. eastus2> <the Hub ID you want to use or create> <the device ID you want to create>
```

儲存 "cs":"{複製這個字串}" 之後所產生的連接字串。

將[這段指令碼](https://raw.githubusercontent.com/Azure/ai-toolkit-iot-edge/master/amliotedge/installIoTEdge)下載到 UbuntuX64 IoT Edge 節點或 DSVM，並執行下列命令，藉以將裝置初始化：

```bash
ssh <yourusername>@<yourdeviceip>
sudo wget https://raw.githubusercontent.com/Azure/ai-toolkit-iot-edge/master/amliotedge/installIoTEdge
sudo chmod +x installIoTEdge
sudo ./installIoTEdge
```

IoT Edge 節點已準備好接收您 IoT 中樞的連接字串。 尋找包含 ```device_connection_string:``` 的程式碼行，並將上述連接字串貼入引號之間。

您也可以遵循[快速入門：將您的第一個 IoT Edge 模組部署至 Linux x64 裝置](../../iot-edge/quickstart-linux.md)文件，以逐步了解如何註冊裝置並安裝 IoT 執行階段。


#### <a name="get-the-container-registry-credentials"></a>取得容器登錄認證
若要將 IoT Edge 模組部署到裝置，Azure IoT 需要 Azure Machine Learning 服務儲存 Docker 映像所在之容器登錄的認證。

您可以利用下列兩種方式，輕鬆地擷取必要的容器登錄認證：

+ **在 Azure 入口網站中**：

  1. 登入 [Azure 入口網站](https://portal.azure.com/signin/index)。

  1. 前往您的 Azure Machine Learning 服務工作區並選取 [概觀]。 前往容器登錄設定，選取 [登錄] 連結。

     ![容器登錄項目影像](./media/how-to-deploy-and-where/findregisteredcontainer.png)

  1. 進入容器登錄之後，請選取 [存取金鑰]，然後啟用管理使用者。
 
     ![存取金鑰畫面影像](./media/how-to-deploy-and-where/findaccesskey.png)

  1. 儲存**登入伺服器**、**使用者名稱**與**密碼**的值。 

+ **使用 Python 指令碼**：

  1. 在您於上方執行的程式碼之後使用下列 Python 指令碼來建立容器：

     ```python
     # Getting your container details
     container_reg = ws.get_details()["containerRegistry"]
     reg_name=container_reg.split("/")[-1]
     container_url = "\"" + image.image_location + "\","
     subscription_id = ws.subscription_id
     from azure.mgmt.containerregistry import ContainerRegistryManagementClient
     from azure.mgmt import containerregistry
     client = ContainerRegistryManagementClient(ws._auth,subscription_id)
     result= client.registries.list_credentials(resource_group_name, reg_name, custom_headers=None, raw=False)
     username = result.username
     password = result.passwords[0].value
     print('ContainerURL{}'.format(image.image_location))
     print('Servername: {}'.format(reg_name))
     print('Username: {}'.format(username))
     print('Password: {}'.format(password))
     ```
  1. 儲存 ContainerURL、servername、username 和 password 的值。 

     必須透過這些認證提供存取權，IoT Edge 裝置才能存取您私人容器登錄中的映像。

#### <a name="deploy-the-model-to-the-device"></a>將模型部署到裝置

您可以藉由執行[這段指令碼](https://raw.githubusercontent.com/Azure/ai-toolkit-iot-edge/master/amliotedge/deploymodel)並提供從上述步驟中取得的下列資訊，輕鬆地部署模型：容器登錄名稱、使用者名稱、密碼、映像位置 url、所需的部署名稱、IoT 中樞名稱，以及您所建立的裝置識別碼。 您可以遵循這些步驟，在 VM 中執行此動作： 

```bash 
wget https://raw.githubusercontent.com/Azure/ai-toolkit-iot-edge/master/amliotedge/deploymodel
sudo chmod +x deploymodel
sudo ./deploymodel <ContainerRegistryName> <username> <password> <imageLocationURL> <DeploymentID> <IoTHubname> <DeviceID>
```

或者，您可以遵循[從 Azure 入口網站部署 Azure IoT Edge 模組](../../iot-edge/how-to-deploy-modules-portal.md)文件中的步驟，將映像部署到您的裝置。 設定裝置的__登錄設定__時，請使用適用於您工作區容器登錄的__登入伺服器__、__使用者名稱__及__密碼__。

> [!NOTE]
> 如果您不熟悉 Azure IoT，請參閱下列文件以取得開始使用服務的相關資訊：
>
> * [快速入門：將您的第一個 IoT Edge 模組部署至 Linux 裝置](../../iot-edge/quickstart-linux.md)
> * [快速入門：將您的第一個 IoT Edge 模組部署至 Windows 裝置](../../iot-edge/quickstart.md)


## <a name="testing-web-service-deployments"></a>測試 Web 服務部署

若要測試 Web 服務部署，您可以使用 Webservice 物件的 `run` 方法。 在下列範例中，會將 JSON 文件設定為 Web 服務並顯示結果。 所傳送的資料必須符合模型的預期。 在此範例中，資料格式會符合糖尿病模型所預期的輸入。

```python
import json

test_sample = json.dumps({'data': [
    [1,2,3,4,5,6,7,8,9,10], 
    [10,9,8,7,6,5,4,3,2,1]
]})
test_sample = bytes(test_sample,encoding = 'utf8')

prediction = service.run(input_data = test_sample)
print(prediction)
```

Webservice 是 REST API，因此您可以使用各種程式設計語言建立用戶端應用程式。 如需詳細資訊，請參閱[建立用戶端應用程式以使用 Webservice](how-to-consume-web-service.md)。

## <a id="update"></a> 更新 Web 服務

若要更新 Web 服務，請使用 `update` 方法。 下列程式碼示範如何更新 Web 服務以使用新的映像：

```python
from azureml.core.webservice import Webservice
from azureml.core.image import Image

service_name = 'aci-mnist-3'
# Retrieve existing service
service = Webservice(name = service_name, workspace = ws)

# point to a different image
new_image = Image(workspace = ws, id="myimage2:1")

# Update the image used by the service
service.update(image = new_image)
print(service.state)
```

> [!NOTE]
> 當您更新映像時，不會自動更新 Web 服務。 您必須手動更新您想要使用新映像的每個服務。

如需詳細資訊，請參閱 [Webservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py) 類別的參考文件。

## <a name="clean-up"></a>清除

若要刪除已部署的 Web 服務，請使用 `service.delete()`。

若要删除映像，請使用 `image.delete()`。

若要刪除已註冊的模型，請使用 `model.delete()`。

如需詳細資訊，請參閱 [WebService.delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#delete--)、[Image.delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image.image(class)?view=azure-ml-py#delete--) 和 [Model.delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#delete--) 參考文件。

## <a name="next-steps"></a>後續步驟

* [使用 SSL 保護 Azure Machine Learning Web 服務](how-to-secure-web-service.md)
* [取用部署為 Web 服務的 ML 模型](how-to-consume-web-service.md)
* [如何執行批次預測](how-to-run-batch-predictions.md)
* [使用 Application Insights 監視您的 Azure Machine Learning 模型](how-to-enable-app-insights.md)
* [在生產環境中收集模型資料](how-to-enable-data-collection.md)
* [Azure Machine Learning 服務 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)
* [透過 Azure 虛擬網路使用 Azure Machine Learning 服務](how-to-enable-virtual-network.md)
