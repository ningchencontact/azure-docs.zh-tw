---
title: 部署疑難排解指南
titleSuffix: Azure Machine Learning service
description: 了解如何解決、 解決問題，並針對搭配 AKS 和使用 Azure Machine Learning 服務的 ACI 常見的 Docker 部署錯誤進行疑難排解。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: chris-lauren
ms.author: clauren
ms.reviewer: jmartens
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 815be7400e0a0560ace7e07b317aeb25c2feacd5
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2019
ms.locfileid: "57450969"
---
# <a name="troubleshooting-azure-machine-learning-service-aks-and-aci-deployments"></a>針對 Azure Machine Learning 服務 AKS 和 ACI 部署進行疑難排解

在本文中，您將了解如何使用 Azure Machine Learning 服務，來因應或解決使用 Azure 容器執行個體 (ACI) 和 Azure Kubernetes Service (AKS) 的常見 Docker 部署錯誤。

在 Azure Machine Learning 服務中部署模型時，系統就會執行數項工作。 這是一系列複雜的事件，而且有時會發生問題。 部署後工作包含：

1. 在工作區模型登錄中註冊模型。

2. 建置 Docker 映像，包括：
    1. 從登錄下載已登錄的模型。 
    2. 在以環境 yaml 檔案中所指定相依性為基礎的 Python 環境中建立 dockerfile。
    3. 在 dockerfile 中新增您的模型檔案和您提供的評分指令碼。
    4. 使用 dockerfile 建置新的 Docker 映像。
    5. 向與工作區相關聯的 Azure Container Registry 註冊 Docker 映像。

3. 將 Docker 映像部署至 Azure 容器執行個體 (ACI) 服務或 Azure Kubernetes Service (AKS)。

4. 在 ACI 或 AKS 中啟動新的容器。 

在[模型管理](concept-model-management-and-deployment.md)簡介中深入了解此程序。

## <a name="before-you-begin"></a>開始之前

如果您遇到任何問題時，首先要做的事就是將部署工作 (先前所述) 分成個別步驟，以將問題隔離。 

這是如果您使用很有幫助`Webservice.deploy`API，或`Webservice.deploy_from_model`API，因為這些函式組成群組至單一動作的上述步驟。 這些 Api 通常很方便，但最好先藉由取代以進行疑難排解時，分解步驟下列 API 呼叫。

1. 註冊模型。 以下是一些範例程式碼：

    ```python
    # register a model out of a run record
    model = best_run.register_model(model_name='my_best_model', model_path='outputs/my_model.pkl')

    # or, you can register a file or a folder of files as a model
    model = Model.register(model_path='my_model.pkl', model_name='my_best_model', workspace=ws)
    ```

2. 建置映像。 以下是一些範例程式碼：

    ```python
    # configure the image
    image_config = ContainerImage.image_configuration(runtime="python",
                                                      execution_script="score.py",
                                                      conda_file="myenv.yml")

    # create the image
    image = Image.create(name='myimg', models=[model], image_config=image_config, workspace=ws)

    # wait for image creation to finish
    image.wait_for_creation(show_output=True)
    ```

3. 將映像部署為服務。 以下是一些範例程式碼：

    ```python
    # configure an ACI-based deployment
    aci_config = AciWebservice.deploy_configuration(cpu_cores=1, memory_gb=1)

    aci_service = Webservice.deploy_from_image(deployment_config=aci_config, 
                                               image=image, 
                                               name='mysvc', 
                                               workspace=ws)
    aci_service.wait_for_deployment(show_output=True)    
    ```

將部署程序分成個別的工作後，我們可以查看一些最常見的錯誤。

## <a name="image-building-fails"></a>映像建置失敗
如果系統無法建置 Docker 映像，`image.wait_for_creation()` 呼叫會失敗，並包含一些能提供部分線索的錯誤訊息。 您也可以從映像組建記錄檔中了解更多有關錯誤的資訊。 以下一些範例程式碼會示範如何找出映像組建記錄檔的 URI。

```python
# if you already have the image object handy
print(image.image_build_log_uri)

# if you only know the name of the image (note there might be multiple images with the same name but different version number)
print(ws.images['myimg'].image_build_log_uri)

# list logs for all images in the workspace
for name, img in ws.images.items():
    print (img.name, img.version, img.image_build_log_uri)
```
映像記錄 URI 是 SAS URL，其指向 Azure Blob 儲存體中儲存的記錄檔。 只要將 URI 複製並貼到瀏覽器視窗中，您就可以下載並檢視記錄檔。

### <a name="azure-key-vault-access-policy-and-azure-resource-manager-templates"></a>Azure 金鑰保存庫存取原則和 Azure Resource Manager 範本

映像組建也會因為 Azure Key Vault 的存取原則的問題而失敗。 這可能是當您使用 Azure Resource Manager 範本建立的工作區和相關聯的資源 （包括 Azure Key Vault），許多次。 例如，使用範本多次使用相同的參數做為一部分的連續整合和部署管線。

大部分的資源建立作業，透過範本具有等冪性，但金鑰保存庫清除的存取原則每次使用此範本。 這會中斷存取金鑰保存庫的任何現有的工作區正在使用它。 這會導致錯誤，當您嘗試建立新的映像。 您可以接收錯誤的範例如下：

__入口網站__：
```text
Create image "myimage": An internal server error occurred. Please try again. If the problem persists, contact support.
```

__SDK__:
```python
image = ContainerImage.create(name = "myimage", models = [model], image_config = image_config, workspace = ws)
Creating image
Traceback (most recent call last):
  File "C:\Python37\lib\site-packages\azureml\core\image\image.py", line 341, in create
    resp.raise_for_status()
  File "C:\Python37\lib\site-packages\requests\models.py", line 940, in raise_for_status
    raise HTTPError(http_error_msg, response=self)
requests.exceptions.HTTPError: 500 Server Error: Internal Server Error for url: https://eastus.modelmanagement.azureml.net/api/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.MachineLearningServices/workspaces/<workspace-name>/images?api-version=2018-11-19

Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  File "C:\Python37\lib\site-packages\azureml\core\image\image.py", line 346, in create
    'Content: {}'.format(resp.status_code, resp.headers, resp.content))
azureml.exceptions._azureml_exception.WebserviceException: Received bad response from Model Management Service:
Response Code: 500
Headers: {'Date': 'Tue, 26 Feb 2019 17:47:53 GMT', 'Content-Type': 'application/json', 'Transfer-Encoding': 'chunked', 'Connection': 'keep-alive', 'api-supported-versions': '2018-03-01-preview, 2018-11-19', 'x-ms-client-request-id': '3cdcf791f1214b9cbac93076ebfb5167', 'x-ms-client-session-id': '', 'Strict-Transport-Security': 'max-age=15724800; includeSubDomains; preload'}
Content: b'{"code":"InternalServerError","statusCode":500,"message":"An internal server error occurred. Please try again. If the problem persists, contact support"}'
```

__CLI__：
```text
ERROR: {'Azure-cli-ml Version': None, 'Error': WebserviceException('Received bad response from Model Management Service:\nResponse Code: 500\nHeaders: {\'Date\': \'Tue, 26 Feb 2019 17:34:05
GMT\', \'Content-Type\': \'application/json\', \'Transfer-Encoding\': \'chunked\', \'Connection\': \'keep-alive\', \'api-supported-versions\': \'2018-03-01-preview, 2018-11-19\', \'x-ms-client-request-id\':
\'bc89430916164412abe3d82acb1d1109\', \'x-ms-client-session-id\': \'\', \'Strict-Transport-Security\': \'max-age=15724800; includeSubDomains; preload\'}\nContent:
b\'{"code":"InternalServerError","statusCode":500,"message":"An internal server error occurred. Please try again. If the problem persists, contact support"}\'',)}
```

若要避免這個問題，我們建議下列方式之一：

* 請勿部署範本一次以上相同的參數。 或使用範本來重新建立它們之前刪除現有的資源。
* 檢查金鑰保存庫的存取原則，並使用此設定`accessPolicies`範本的屬性。
* 檢查 Key Vault 資源是否已經存在。 若是如此，不會重建它透過範本。 例如，加入參數，可讓您停用建立金鑰保存庫資源，如果已經存在。

## <a name="service-launch-fails"></a>服務啟動失敗
成功建置映像之後，系統就會嘗試在 ACI 或 AKS 中 (取決於部署組態) 啟動容器。 建議您先，嘗試 ACI 部署，因為它是簡單的單一容器部署。 如此一來，您可以先排除任何與 AKS 相關的問題。

作為容器啟動程序的一部分，系統會叫用評分指令碼中的 `init()` 函式。 如果 `init()` 函式中有無法攔截的例外狀況，您可能會在錯誤訊息中看到 **CrashLoopBackOff** 錯誤。 以下是一些秘訣，協助您對問題進行疑難排解。

### <a name="inspect-the-docker-log"></a>檢查 Docker 記錄
您可以從服務物件中列印詳細的 Docker 引擎記錄訊息。

```python
# if you already have the service object handy
print(service.get_logs())

# if you only know the name of the service (note there might be multiple services with the same name but different version number)
print(ws.webservices['mysvc'].get_logs())
```

### <a name="debug-the-docker-image-locally"></a>在本機上對 Docker 映像進行偵錯
有時候，Docker 記錄發出的資訊不足以找出錯誤原因。 您可以繼續下一步並提取已建置的 Docker 映像，然後啟動本機容器，以互動方式直接在作用中的容器內進行偵錯。 若要啟動本機容器，您必須讓 Docker 引擎在本機執行，而且如果您已安裝 [azure-cli](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)，該程序會簡單很多。

首先，我們需要找出的映像位置：

```python
# print image location
print(image.image_location)
```

映像位置的格式為：`<acr-name>.azurecr.io/<image-name>:<version-number>`，例如 `myworkpaceacr.azurecr.io/myimage:3`。 

現在，移至您的命令列視窗。 如果您已安裝 azure-cli，您可以輸入下列命令來登入與工作區 (映像所在位置) 相關聯的 ACR (Azure Container Registry)。 

```sh
# log on to Azure first if you haven't done so before
$ az login

# make sure you set the right subscription in case you have access to multiple subscriptions
$ az account set -s <subscription_name_or_id>

# now let's log in to the workspace ACR
# note the acr-name is the domain name WITHOUT the ".azurecr.io" postfix
# e.g.: az acr login -n myworkpaceacr
$ az acr login -n <acr-name>
```
如果您沒有安裝 azure-cli，可以使用 `docker login` 命令來登入 ACR。 但是，您必須先從 Azure 入口網站擷取 ACR 的使用者名稱和密碼。

登入 ACR 後，您就可以提取 Docker 映像並在本機啟動容器，然後使用 `docker run` 命令來啟動用於偵錯的 bash 工作階段：

```sh
# note the image_id is <acr-name>.azurecr.io/<image-name>:<version-number>
# for example: myworkpaceacr.azurecr.io/myimage:3
$ docker run -it <image_id> /bin/bash
```

一旦針對執行中的容器啟動 bash 工作階段後，您可以在 `/var/azureml-app` 資料夾中找到評分指令碼。 然後，您可以啟動 Python 工作階段來對評分指令碼進行偵錯。 

```sh
# enter the directory where scoring scripts live
cd /var/azureml-app

# find what Python packages are installed in the python environment
pip freeze

# sanity-check on score.py
# you might want to edit the score.py to trigger init().
# as most of the errors happen in init() when you are trying to load the model.
python score.py
```
如果您需要文字編輯器來修改指令碼，您可以安裝 vim、nano、Emacs 或您慣用的編輯器。

```sh
# update package index
apt-get update

# install a text editor of your choice
apt-get install vim
apt-get install nano
apt-get install emacs

# launch emacs (for example) to edit score.py
emacs score.py

# exit the container bash shell
exit
```

您也可以在本機上啟動 Web 服務，並將 HTTP 流量傳送至此。 Docker 容器中的 Flask 伺服器會在連接埠 5001 上執行。 您可以對應到主機上任何可使用的其他連接埠。
```sh
# you can find the scoring API at: http://localhost:8000/score
$ docker run -p 8000:5001 <image_id>
```

## <a name="function-fails-getmodelpath"></a>函式錯誤：get_model_path()
通常，在評分指令碼的 `init()` 函式中，呼叫 `Model.get_model_path()` 函式是為了找出容器中的模型檔案或模型檔案資料夾。 如果找不到模型檔案或資料夾，這通常是來源錯誤。 若要對此錯誤進行偵錯，最簡單方式是在容器殼層中執行下列 Python 程式碼：

```python
import logging
logging.basicConfig(level=logging.DEBUG)
from azureml.core.model import Model
print(Model.get_model_path(model_name='my-best-model'))
```

這會列印出容器中的本機路徑 (相對於 `/var/azureml-app`)，而您的評分指令碼預期會在其中找到模型檔案或資料夾。 然後您可以確認該檔案或資料夾是否確實是您需要的。

將記錄層級設定為 [偵錯] 可記錄額外資訊，這在要找出失敗原因時可能很實用。

## <a name="function-fails-runinputdata"></a>函式失敗：run(input_data)
如果已成功部署服務，但此服務在您發佈資料到評分端點時發生損毀，您可以在 `run(input_data)` 函式中新增錯誤攔截陳述式，以傳回詳細的錯誤訊息。 例如︰

```python
def run(input_data):
    try:
        data = json.loads(input_data)['data']
        data = np.array(data)
        result = model.predict(data)
        return json.dumps({"result": result.tolist()})
    except Exception as e:
        result = str(e)
        # return error message back to the client
        return json.dumps({"error": result})
```
**注意**：從 `run(input_data)` 呼叫傳回錯誤訊息的方式應僅用於偵錯目的。 基於安全性考量，這可能不適合在生產環境中進行。

## <a name="http-status-code-503"></a>HTTP 狀態碼 503

Azure Kubernetes 服務部署支援自動調整功能，可允許加入以支援額外的負載的複本。 不過，自動調整程式設計來處理**逐漸**負載的變化。 如果您在每秒的要求中收到大型的高峰，用戶端可能會收到 HTTP 狀態碼 503。

有兩件事，可協助防止 503 狀態碼：

* 變更在哪一個自動調整的使用率等級建立新的複本。
    
    根據預設，自動調整目標使用率設定為 70%，這表示服務可處理每秒要求 (RPS 最多 30%的) 尖峰。 您可以藉由設定調整的使用率目標`autoscale_target_utilization`較低的值。

    > [!IMPORTANT]
    > 這項變更不會造成要建立的複本*更快*。 相反地，它們會建立在較低的使用率閾值。 而不是等到服務 70%時，將值變更為 30%，將會導致 30%的使用率，就會發生時要建立的複本。
    
    如果 web 服務已經在使用目前的最大複本，而且您仍然看到 503 狀態碼，增加`autoscale_max_replicas`值來提高複本數目上限。

* 變更複本的數目下限。 增加 最小的複本，提供較大的集區，以處理傳入的暴增情況。

    若要增加複本的最小數目，設定`autoscale_min_replicas`較高的值。 您可以計算必要的複本，使用下列程式碼，取代特有的值的值新增至您的專案：

    ```python
    from math import ceil
    # target requests per second
    targetRps = 20
    # time to process the request (in seconds)
    reqTime = 10
    # Maximum requests per container
    maxReqPerContainer = 1
    # target_utilization. 70% in this example
    targetUtilization = .7

    concurrentRequests = targetRps * reqTime / targetUtilization

    # Number of container replicas
    replicas = ceil(concurrentRequests / maxReqPerContainer)
    ```

    > [!NOTE]
    > 如果收到大於新的最小複本可以處理的要求尖峰，可能會收到 503年一次。 比方說，為您的服務增加的流量，您可能需要增加 最小的複本。

如需設定的詳細資訊`autoscale_target_utilization`， `autoscale_max_replicas`，並`autoscale_min_replicas`，請參閱[AksWebservice](https://docs.microsoft.com/en-us/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py)模組參考。


## <a name="next-steps"></a>後續步驟

深入了解部署： 
* [部署方式及位置](how-to-deploy-and-where.md)

* [教學課程：定型與部署模型](tutorial-train-models-with-aml.md)
