---
title: Azure Machine Learning 服務的部署疑難排解指南
description: 了解如何因應和解決 Azure Machine Learning 服務中常見的 Docker 部署錯誤，並對其進行疑難排解。
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: haining
author: hning86
ms.reviewer: jmartens
ms.date: 10/01/2018
ms.openlocfilehash: a10b05e95fa719b80775191e48bd4117e3a785fd
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/15/2018
ms.locfileid: "49321677"
---
# <a name="troubleshooting-azure-machine-learning-service-deployments"></a>針對 Azure Machine Learning 服務部署進行疑難排解

在本文中，您將了解如何因應和解決 Azure Machine Learning 服務中常見的 Docker 部署錯誤。

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

如果您使用 `Webservice.deploy` API 或 `Webservice.deploy_from_model` API，此方法會特別實用，因為這些函式會將上述步驟全部群組至單一動作中。 這些 API 通常很方便，但若要在進行疑難排解時將步驟分開，應以下列 API 呼叫取代這些 API。

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
print(ws.images()['myimg'].image_build_log_uri)

# list logs for all images in the workspace
for name, img in ws.images().items():
    print (img.name, img.version, img.image_build_log_uri)
```
映像記錄 URI 是 SAS URL，其指向 Azure Blob 儲存體中儲存的記錄檔。 只要將 URI 複製並貼到瀏覽器視窗中，您就可以下載並檢視記錄檔。


## <a name="service-launch-fails"></a>服務啟動失敗
成功建置映像之後，系統就會嘗試在 ACI 或 AKS 中 (取決於部署組態) 啟動容器。 通常會建議先嘗試 ACI 部署，因為這是簡單的單一容器部署。 如此一來，您可以先排除任何與 AKS 相關的問題。

作為容器啟動程序的一部分，系統會叫用評分指令碼中的 `init()` 函式。 如果 `init()` 函式中有無法攔截的例外狀況，您可能會在錯誤訊息中看到 **CrashLoopBackOff** 錯誤。 以下是一些秘訣，協助您對問題進行疑難排解。

### <a name="inspect-the-docker-log"></a>檢查 Docker 記錄
您可以從服務物件中列印詳細的 Docker 引擎記錄訊息。

```python
# if you already have the service object handy
print(service.get_logs())

# if you only know the name of the service (note there might be multiple services with the same name but different version number)
print(ws.webservices()['mysvc'].get_logs())
```

### <a name="debug-the-docker-image-locally"></a>在本機上對 Docker 映像進行偵錯
有時候，Docker 記錄發出的資訊不足以找出錯誤原因。 您可以繼續下一步並提取已建置的 Docker 映像，然後啟動本機容器，以互動方式直接在作用中的容器內進行偵錯。 若要啟動本機容器，您必須讓 Docker 引擎在本機執行，而且如果您已安裝 [azure-cli](/cli/azure/install-azure-cli?view=azure-cli-latest)，該程序會簡單很多。

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
from azureml.core.model import Model
print(Model.get_model_path(model_name='my-best-model'))
```

這會列印出容器中的本機路徑 (相對於 `/var/azureml-app`)，而您的評分指令碼預期會在其中找到模型檔案或資料夾。 然後您可以確認該檔案或資料夾是否確實是您需要的。


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
**附註**：從 `run(input_data)` 呼叫傳回錯誤訊息的方式應僅用於偵錯目的。 基於安全性考量，這可能不適合在生產環境中進行。


## <a name="next-steps"></a>後續步驟

深入了解部署： 
* [如何部署至 ACI](how-to-deploy-to-aci.md)

* [如何部署至 AKS](how-to-deploy-to-aks.md)

* [教學課程第 1 部分：訓練模型](tutorial-train-models-with-aml.md)

* [教學課程第 2 部分：部署模型](tutorial-deploy-models-with-aml.md)
