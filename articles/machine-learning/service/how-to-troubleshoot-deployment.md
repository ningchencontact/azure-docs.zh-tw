---
title: 部署疑難排解指南
titleSuffix: Azure Machine Learning service
description: 瞭解如何使用 Azure Machine Learning 服務 Azure Kubernetes Service 和 Azure 容器實例來解決、解決常見的 Docker 部署錯誤, 並對其進行疑難排解。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: chris-lauren
ms.author: clauren
ms.reviewer: jmartens
ms.date: 07/09/2019
ms.custom: seodec18
ms.openlocfilehash: 24716a9b9fa5174d899cf0678b83b2da0c59957c
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/19/2019
ms.locfileid: "68358665"
---
# <a name="troubleshooting-azure-machine-learning-service-azure-kubernetes-service-and-azure-container-instances-deployment"></a>針對 Azure Machine Learning 服務 Azure Kubernetes Service 和 Azure 容器實例部署進行疑難排解

瞭解如何使用 Azure Machine Learning 服務, 透過 Azure 容器實例 (ACI) 和 Azure Kubernetes Service (AKS) 來解決或解決常見的 Docker 部署錯誤。

在 Azure Machine Learning 服務中部署模型時，系統就會執行數項工作。 部署後工作包含：

1. 在工作區模型登錄中註冊模型。

2. 建置 Docker 映像，包括：
    1. 從登錄下載已登錄的模型。 
    2. 在以環境 yaml 檔案中所指定相依性為基礎的 Python 環境中建立 dockerfile。
    3. 在 dockerfile 中新增您的模型檔案和您提供的評分指令碼。
    4. 使用 dockerfile 建置新的 Docker 映像。
    5. 向與工作區相關聯的 Azure Container Registry 註冊 Docker 映像。

    > [!IMPORTANT]
    > 視您的程式碼而定, 會自動建立映射而不需要您的輸入。

3. 將 Docker 映像部署至 Azure 容器執行個體 (ACI) 服務或 Azure Kubernetes Service (AKS)。

4. 在 ACI 或 AKS 中啟動新的容器。 

在[模型管理](concept-model-management-and-deployment.md)簡介中深入了解此程序。

## <a name="before-you-begin"></a>開始之前

如果您遇到任何問題時，首先要做的事就是將部署工作 (先前所述) 分成個別步驟，以將問題隔離。

如果您使用[webservice ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#deploy-workspace--name--model-paths--image-config--deployment-config-none--deployment-target-none-) Api 或[webservice. deploy_from_model ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#deploy-from-model-workspace--name--models--image-config--deployment-config-none--deployment-target-none-) api, 將部署中斷至工作會很有説明, 因為這兩個函式都會以單一動作執行上述步驟。 這些 Api 通常很方便, 但使用下列 API 呼叫來取代它們, 有助於在進行疑難排解時分解步驟。

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
                                                      entry_script="score.py",
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

如果無法建立 Docker 映射, 則[wait_for_creation ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image.image(class)?view=azure-ml-py#wait-for-creation-show-output-false-)或[wait_for_deployment ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#wait-for-deployment-show-output-false-)呼叫會失敗, 並出現一些可提供一些線索的錯誤訊息。 您也可以從映像組建記錄檔中了解更多有關錯誤的資訊。 以下一些範例程式碼會示範如何找出映像組建記錄檔的 URI。

```python
# if you already have the image object handy
print(image.image_build_log_uri)

# if you only know the name of the image (note there might be multiple images with the same name but different version number)
print(ws.images['myimg'].image_build_log_uri)

# list logs for all images in the workspace
for name, img in ws.images.items():
    print(img.name, img.version, img.image_build_log_uri)
```

映像記錄 URI 是 SAS URL，其指向 Azure Blob 儲存體中儲存的記錄檔。 只要將 URI 複製並貼到瀏覽器視窗中，您就可以下載並檢視記錄檔。

### <a name="azure-key-vault-access-policy-and-azure-resource-manager-templates"></a>Azure Key Vault 存取原則和 Azure Resource Manager 範本

映射組建也可能因為 Azure Key Vault 上的存取原則發生問題而失敗。 當您使用 Azure Resource Manager 範本來建立工作區和相關聯的資源 (包括 Azure Key Vault) 時, 可能會發生這種情況。 例如, 使用範本多次, 並以相同的參數作為持續整合和部署管線的一部分。

大部分透過範本的資源建立作業都是等冪的, 但 Key Vault 會在每次使用範本時清除存取原則。 清除存取原則會中斷對使用它之任何現有工作區的 Key Vault 存取。 當您嘗試建立新的映射時, 此狀況會導致錯誤。 以下是您可以接收的錯誤範例:

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

若要避免這個問題, 建議您採用下列其中一種方法:

* 不要針對相同的參數多次部署範本。 或刪除現有的資源, 然後再使用此範本重新建立它們。
* 檢查 Key Vault 的存取原則, 然後使用這些原則來設定`accessPolicies`範本的屬性。
* 檢查 Key Vault 資源是否已存在。 如果有, 請不要透過範本重新建立它。 例如, 新增可讓您停用建立 Key Vault 資源的參數 (如果已經存在)。

## <a name="debug-locally"></a>本機的調試

如果您在將模型部署至 ACI 或 AKS 時遇到問題, 請嘗試將它部署為本機 web 服務。 使用本機 web 服務可讓您更輕鬆地針對問題進行疑難排解。 包含模型的 Docker 映射會下載並在您的本機系統上啟動。

> [!IMPORTANT]
> 本機 web 服務部署需要在您的本機系統上執行正常的 Docker 安裝。 在您部署本機 web 服務之前, 必須先執行 Docker。 如需安裝和使用 Docker 的詳細資訊[https://www.docker.com/](https://www.docker.com/), 請參閱。

> [!WARNING]
> 針對生產案例, 不支援本機 web 服務部署。

若要在本機部署, 請修改您`LocalWebservice.deploy_configuration()`的程式碼, 以用來建立部署設定。 然後使用`Model.deploy()`來部署服務。 下列範例會將模型 (包含在`model`變數中) 部署為本機 web 服務:

```python
from azureml.core.model import InferenceConfig, Model
from azureml.core.webservice import LocalWebservice

# Create inference configuration. This creates a docker image that contains the model.
inference_config = InferenceConfig(runtime="python",
                                   entry_script="score.py",
                                   conda_file="myenv.yml")

# Create a local deployment, using port 8890 for the web service endpoint
deployment_config = LocalWebservice.deploy_configuration(port=8890)
# Deploy the service
service = Model.deploy(
    ws, "mymodel", [model], inference_config, deployment_config)
# Wait for the deployment to complete
service.wait_for_deployment(True)
# Display the port that the web service is available on
print(service.port)
```

此時, 您可以照常使用服務。 例如, 下列程式碼示範如何將資料傳送至服務:

```python
import json

test_sample = json.dumps({'data': [
    [1, 2, 3, 4, 5, 6, 7, 8, 9, 10],
    [10, 9, 8, 7, 6, 5, 4, 3, 2, 1]
]})

test_sample = bytes(test_sample, encoding='utf8')

prediction = service.run(input_data=test_sample)
print(prediction)
```

### <a name="update-the-service"></a>更新服務

在本機測試期間, 您可能需要更新`score.py`檔案以新增記錄, 或嘗試解決您發現的任何問題。 若要重載檔案的`score.py`變更, 請`reload()`使用。 例如, 下列程式碼會重載服務的腳本, 然後將資料傳送給它。 資料會使用更新`score.py`的檔案進行評分:

```python
service.reload()
print(service.run(input_data=test_sample))
```

> [!NOTE]
> 此腳本會從服務所使用的`InferenceConfig`物件所指定的位置重載。

若要變更模型、Conda 相依性或部署設定, 請使用[update ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#update--args-)。 下列範例會更新服務所使用的模型:

```python
service.update([different_model], inference_config, deployment_config)
```

### <a name="delete-the-service"></a>刪除服務

若要刪除服務, 請使用[delete ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#delete--)。

### <a id="dockerlog"></a>檢查 Docker 記錄檔

您可以從服務物件中列印詳細的 Docker 引擎記錄訊息。 您可以針對 ACI、AKS 和本機部署來查看記錄。 下列範例示範如何列印記錄。

```python
# if you already have the service object handy
print(service.get_logs())

# if you only know the name of the service (note there might be multiple services with the same name but different version number)
print(ws.webservices['mysvc'].get_logs())
```

## <a name="service-launch-fails"></a>服務啟動失敗

成功建立映射之後, 系統會嘗試使用您的部署設定啟動容器。 作為容器啟動程序的一部分，系統會叫用評分指令碼中的 `init()` 函式。 如果 `init()` 函式中有無法攔截的例外狀況，您可能會在錯誤訊息中看到 **CrashLoopBackOff** 錯誤。

請使用[檢查 Docker 記錄](#dockerlog)一節中的資訊來檢查記錄。

## <a name="function-fails-getmodelpath"></a>函式錯誤：get_model_path()

通常, 在評分`init()`腳本的函式中, 會呼叫[_model_path ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#get-model-path-model-name--version-none---workspace-none-)函式, 以在容器中尋找模型檔案或模型檔案的資料夾。 如果找不到模型檔案或資料夾, 則函式會失敗。 若要對此錯誤進行偵錯，最簡單方式是在容器殼層中執行下列 Python 程式碼：

```python
from azureml.core.model import Model
import logging
logging.basicConfig(level=logging.DEBUG)
print(Model.get_model_path(model_name='my-best-model'))
```

這個範例會在您的評分腳本預期要`/var/azureml-app`尋找模型檔案或資料夾的容器中, 印出本機路徑 (相對於)。 然後您可以確認該檔案或資料夾是否確實是您需要的。

將記錄層級設定為 [DEBUG] 可能會導致記錄額外的資訊, 這可能有助於識別失敗。

## <a name="function-fails-runinputdata"></a>函式失敗：run(input_data)

如果已成功部署服務，但此服務在您發佈資料到評分端點時發生損毀，您可以在 `run(input_data)` 函式中新增錯誤攔截陳述式，以傳回詳細的錯誤訊息。 例如:

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

**注意**：從 `run(input_data)` 呼叫傳回錯誤訊息的方式應僅用於偵錯目的。 基於安全性理由, 您不應該在生產環境中以這種方式傳回錯誤訊息。

## <a name="http-status-code-503"></a>HTTP 狀態碼503

Azure Kubernetes Service 部署支援自動調整, 這可讓您新增複本以支援額外的負載。 不過, 自動調整程式是設計用來處理負載中的**漸進**式變更。 如果您每秒收到要求數尖峰, 用戶端可能會收到 HTTP 狀態碼503。

有兩個方法可協助防止503狀態碼:

* 變更自動調整建立新複本的使用率層級。
    
    根據預設, 自動調整目標使用率會設定為 70%, 這表示服務可以處理每秒要求數 (RPS) 的尖峰, 最高可達 30%。 您可以藉由將`autoscale_target_utilization`設為較低的值, 來調整使用率目標。

    > [!IMPORTANT]
    > 這種變更並不會讓複本的建立*速度更快*。 相反地, 它們會以較低的使用率閾值來建立。 若不等到服務已使用 70%, 將值變更為 30%, 會在 30% 使用率發生時建立複本。
    
    如果 web 服務已在使用目前的最大複本, 而您仍看到503狀態碼, 請增加`autoscale_max_replicas`值以增加複本的最大數目。

* 變更複本的最小數目。 增加最小複本可提供較大的集區來處理傳入尖峰。

    若要增加複本的最小數目, `autoscale_min_replicas`請將設定為較高的值。 您可以使用下列程式碼來計算所需的複本, 並將值取代為您專案的特定值:

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
    > 如果您收到的要求尖峰超過新的最小複本可以處理的數目, 您可能會再次收到503s。 例如, 隨著服務的流量增加, 您可能需要增加最小複本。

如需針對`autoscale_target_utilization`設定、 `autoscale_max_replicas`和`autoscale_min_replicas`的詳細資訊, 請參閱[AksWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py)模組參考。


## <a name="advanced-debugging"></a>先進的調試

在某些情況下, 您可能需要以互動方式來對模型部署中包含的 Python 程式碼進行驗證。 例如, 如果專案腳本失敗, 而且無法由其他記錄來判斷原因。 藉由使用 Visual Studio Code 和適用於 Visual Studio 的 Python 工具 (PTVSD), 您可以附加至在 Docker 容器內執行的程式碼。

> [!IMPORTANT]
> 使用`Model.deploy()`和在本機部署模型時, `LocalWebservice.deploy_configuration`這種調試方法無法運作。 相反地, 您必須使用[install-containerimage](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image.containerimage?view=azure-ml-py)類別來建立映射。 
>
> 本機 web 服務部署需要在您的本機系統上執行正常的 Docker 安裝。 在您部署本機 web 服務之前, 必須先執行 Docker。 如需安裝和使用 Docker 的詳細資訊[https://www.docker.com/](https://www.docker.com/), 請參閱。

### <a name="configure-development-environment"></a>設定開發環境

1. 若要在您的本機 VS Code 開發環境上安裝適用於 Visual Studio 的 Python 工具 (PTVSD), 請使用下列命令:

    ```
    python -m pip install --upgrade ptvsd
    ```

    如需搭配 VS Code 使用 PTVSD 的詳細資訊, 請參閱[遠端偵錯](https://code.visualstudio.com/docs/python/debugging#_remote-debugging)程式。

1. 若要設定 VS Code 與 Docker 映射通訊, 請建立新的 debug 設定:

    1. 從 VS Code 選取 [__調試__] 功能表, 然後選取 [__開啟__設定]。 隨即開啟名為 [__啟動 json__ ] 的檔案。

    1. 在__啟動 json__檔案中, 尋找包含`"configurations": [`的行, 並在其後插入下列文字:

        ```json
        {
            "name": "Azure Machine Learning service: Docker Debug",
            "type": "python",
            "request": "attach",
            "port": 5678,
            "host": "localhost",
            "pathMappings": [
                {
                    "localRoot": "${workspaceFolder}",
                    "remoteRoot": "/var/azureml-app"
                }
            ]
        }
        ```

        > [!IMPORTANT]
        > 如果 [設定] 區段中已經有其他專案, 請在您插入的程式碼後面新增逗號 (,)。

        本節會使用埠5678附加至 Docker 容器。

    1. 儲存 [__啟動 json__檔案]。

### <a name="create-an-image-that-includes-ptvsd"></a>建立包含 PTVSD 的映射

1. 修改部署的 conda 環境, 使其包含 PTVSD。 下列範例示範如何使用`pip_packages`參數來新增它:

    ```python
    from azureml.core.conda_dependencies import CondaDependencies 
    
    # Usually a good idea to choose specific version numbers
    # so training is made on same packages as scoring
    myenv = CondaDependencies.create(conda_packages=['numpy==1.15.4',            
                                'scikit-learn==0.19.1', 'pandas==0.23.4'],
                                 pip_packages = ['azureml-defaults==1.0.17', 'ptvsd'])
    
    with open("myenv.yml","w") as f:
        f.write(myenv.serialize_to_string())
    ```

1. 若要在服務啟動時啟動 PTVSD 並等候連接, 請將下列內容新增至檔案頂端`score.py` :

    ```python
    import ptvsd
    # Allows other computers to attach to ptvsd on this IP address and port.
    ptvsd.enable_attach(address=('0.0.0.0', 5678), redirect_output = True)
    # Wait 30 seconds for a debugger to attach. If none attaches, the script continues as normal.
    ptvsd.wait_for_attach(timeout = 30)
    print("Debugger attached...")
    ```

1. 在調試過程中, 您可能會想要變更映射中的檔案, 而不需要重新建立。 若要在 Docker 映射中安裝文字編輯器 (vim), 請建立名為`Dockerfile.steps`的新文字檔, 並使用下列做為檔案的內容:

    ```text
    RUN apt-get update && apt-get -y install vim
    ```

    文字編輯器可讓您修改 docker 映射內的檔案, 以測試變更, 而不需要建立新的映射。

1. 若要建立使用`Dockerfile.steps`檔案的映射, 請在建立映射時`docker_file`使用參數。 下列範例示範如何執行這項操作:

    > [!NOTE]
    > 這個範例假設`ws`指向您的 Azure Machine Learning 工作區, 而這`model`是要部署的模型。 `myenv.yml`檔案包含在步驟1中建立的 conda 相依性。

    ```python
    from azureml.core.image import Image, ContainerImage
    image_config = ContainerImage.image_configuration(runtime= "python",
                                 execution_script="score.py",
                                 conda_file="myenv.yml",
                                 docker_file="Dockerfile.steps")

    image = Image.create(name = "myimage",
                     models = [model],
                     image_config = image_config, 
                     workspace = ws)
    # Print the location of the image in the repository
    print(image.image_location)
    ```

建立映射之後, 就會顯示登錄中的映射位置。 位置與下列文字類似:

```text
myregistry.azurecr.io/myimage:1
```

在此文字範例中, 登錄名稱為`myregistry` , 映射命名`myimage`為。 映射版本為`1`。

### <a name="download-the-image"></a>下載映射

1. 開啟命令提示字元、終端機或其他 shell, 並使用下列[Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)命令向包含您 Azure Machine Learning 工作區的 Azure 訂用帳戶進行驗證:

    ```azurecli
    az login
    ```

1. 若要向包含您映射的 Azure Container Registry (ACR) 進行驗證, 請使用下列命令。 以`myregistry`您註冊映射時所傳回的取代:

    ```azurecli
    az acr login --name myregistry
    ```

1. 若要將映射下載到本機 Docker, 請使用下列命令。 將`myimagepath`取代為您在註冊映射時所傳回的位置:

    ```bash
    docker pull myimagepath
    ```

    影像路徑應該類似`myregistry.azurecr.io/myimage:1`。 其中`myregistry`是您的登錄`myimage` 、是您的映射`1` , 而是映射版本。

    > [!TIP]
    > 上一個步驟的驗證不會持續下去。 如果您在驗證命令和 pull 命令之間等候夠久, 您將會收到驗證失敗。 如果發生這種情況, 請重新驗證。

    完成下載所需的時間取決於您的網際網路連線速度。 在程式期間會顯示下載狀態。 下載完成後, 您就可以使用`docker images`命令來驗證它是否已下載。

1. 若要更輕鬆地使用影像, 請使用下列命令來新增標記。 取代`myimagepath`為步驟2中的位置值。

    ```bash
    docker tag myimagepath debug:1
    ```

    在其餘的步驟中, 您可以參考本機映射`debug:1` , 而不是完整的映射路徑值。

### <a name="debug-the-service"></a>調試服務

> [!TIP]
> 如果您在檔案中`score.py`設定 PTVSD 連接的超時時間, 您必須先將 VS Code 連接到 debug 會話, 才會到期。 啟動 VS Code、開啟的本機複本`score.py`、設定中斷點, 並在使用本節中的步驟之前準備好進行。
>
> 如需有關偵錯工具和設定中斷點的詳細資訊, 請參閱[調試](https://code.visualstudio.com/Docs/editor/debugging)程式。

1. 若要使用映射啟動 Docker 容器, 請使用下列命令:

    ```bash
    docker run --rm --name debug -p 8000:5001 -p 5678:5678 debug:1
    ```

1. 若要將 VS Code 附加至容器內的 PTVSD, 請開啟 VS Code 並使用 F5 鍵或選取 [ __Debug__]。 出現提示時, 選取__Azure Machine Learning 服務:Docker Debug__設定。 您也可以從側邊列選取 [debug] 圖示, __Azure Machine Learning 服務:[調試__程式] 下拉式功能表中的 [Docker Debug] 專案, 然後使用綠色箭號來附加偵錯工具。

    ![[偵錯工具] 圖示、[開始偵錯工具] 按鈕和 [設定選取器]](media/how-to-troubleshoot-deployment/start-debugging.png)

此時, VS Code 會連接到 Docker 容器內的 PTVSD, 並在您先前設定的中斷點停止。 您現在可以在程式碼執行時逐步執行、查看變數等。

如需使用 VS Code 來對 Python 進行偵錯工具的詳細資訊, 請參閱[debug 您的 python 程式碼](https://docs.microsoft.com/visualstudio/python/debugging-python-in-visual-studio?view=vs-2019)。

<a id="editfiles"></a>
### <a name="modify-the-container-files"></a>修改容器檔案

若要對映射中的檔案進行變更, 您可以附加至執行中的容器, 並執行 bash shell。 您可以從該處使用 vim 來編輯檔案:

1. 若要連接到執行中的容器, 並在容器中啟動 bash shell, 請使用下列命令:

    ```bash
    docker exec -it debug /bin/bash
    ```

1. 若要尋找服務所使用的檔案, 請從容器中的 bash shell 使用下列命令:

    ```bash
    cd /var/azureml-app
    ```

    從這裡開始, 您可以使用 vim 來編輯`score.py`檔案。 如需有關使用 vim 的詳細資訊, 請參閱[使用 vim 編輯器](https://www.tldp.org/LDP/intro-linux/html/sect_06_02.html)。

1. 容器的變更通常不會保存。 若要儲存您所做的任何變更, 請使用下列命令, 然後在您結束上述步驟 (也就是另一個 shell) 中啟動的 shell 之前:

    ```bash
    docker commit debug debug:2
    ```

    此命令會建立名為`debug:2`的新映射, 其中包含您的編輯。

    > [!TIP]
    > 您必須停止目前的容器, 並開始使用新的版本, 變更才會生效。

1. 請務必將您對容器中的檔案所做的變更, 與 VS Code 使用的本機檔案保持同步。 否則, 偵錯工具體驗將無法如預期般運作。

### <a name="stop-the-container"></a>停止容器

若要停止容器, 請使用下列命令:

```bash
docker stop debug
```

## <a name="next-steps"></a>後續步驟

深入了解部署：

* [部署方式及位置](how-to-deploy-and-where.md)
* [教學課程：定型與部署模型](tutorial-train-models-with-aml.md)
