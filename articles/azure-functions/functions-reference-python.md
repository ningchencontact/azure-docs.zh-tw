---
title: 適用於 Azure Functions 的 Python 開發人員參考
description: 了解如何使用 Python 開發函式
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
keywords: azure functions, 函式, 事件處理, 動態計算, 無伺服器架構, Pythong
ms.service: functions
ms.devlang: python
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 04/16/2018
ms.author: glenga
ms.openlocfilehash: dadb645067a0f6cac436d638a829ac4c0937bd60
ms.sourcegitcommit: 70471c4febc7835e643207420e515b6436235d29
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/15/2019
ms.locfileid: "54304361"
---
# <a name="azure-functions-python-developer-guide"></a>Azure Functions Python 開發人員指南

本文是使用 Python 開發 Azure Functions 的簡介。 本文假設您已閱讀過下列 [Azure Functions 開發人員指南](functions-reference.md)。

[!INCLUDE [functions-python-preview-note](../../includes/functions-python-preview-note.md)]

## <a name="programming-model"></a>程式設計模型

Python 指令碼中的 Azure 函式應該是無狀態方法，處理輸入及產生輸出。 依預設，執行階段預期這將會實作為全域方法 (在 `__init__.py` 檔案中稱為 `main()`)。

您可以藉由指定 `function.json` 檔案中的 `scriptFile` 和 `entryPoint` 屬性來變更預設組態。 例如，下方的 _function.json_ 會告訴執行階段使用在 _main.py_ 檔案中的 _customentry()_ 方法，以作為 Azure 函式的進入點。

```json
{
  "scriptFile": "main.py",
  "entryPoint": "customentry",
  ...
}
```

來自觸發程序和繫結的資料，透過方法屬性 (使用 `function.json` 組態檔中定義的 `name` 屬性) 繫結至函式。 例如，下方的 _function.json_ 說明由名為 `req` HTTP 要求觸發的簡單函式：

```json
{
  "bindings": [
    {
      "name": "req",
      "direction": "in",
      "type": "httpTrigger",
      "authLevel": "anonymous"
    },
    {
      "name": "$return",
      "direction": "out",
      "type": "http"
    }
  ]
}
```

`__init__.py` 檔案包含下列函式程式碼：

```python
def main(req):
    user = req.params.get('user')
    return f'Hello, {user}!'
```

或者，您也可以使用 Python 型別註釋在函式中宣告參數類型並傳回類型。 例如，您可以使用註釋撰寫相同的函式，如下所示：

```python
import azure.functions

def main(req: azure.functions.HttpRequest) -> str:
    user = req.params.get('user')
    return f'Hello, {user}!'
```  

請使用 [azure.functions.*](/python/api/azure-functions/azure.functions?view=azure-python) 套件中所包含的 Python 註釋，以將輸入和輸出繫結至方法。 

## <a name="folder-structure"></a>資料夾結構

Python 函式專案的資料夾結構看起來如下：

```
 FunctionApp
 | - MyFirstFunction
 | | - __init__.py
 | | - function.json
 | - MySecondFunction
 | | - __init__.py
 | | - function.json
 | - SharedCode
 | | - myFirstHelperFunction.py
 | | - mySecondHelperFunction.py
 | - host.json
 | - requirements.txt
 | - extensions.csproj
 | - bin
```

其中有一個可用來設定函數應用程式的共用 [host.json](functions-host-json.md) 檔案。 每個函式都具有本身的程式碼檔案和繫結設定檔 (function.json)。 

共用程式碼應該放在個別的資料夾。 若要參考 SharedCode 資料夾中的模組，您可以使用下列語法：

```
from ..SharedCode import myFirstHelperFunction
```

在函式執行階段時使用的繫結擴充功能，是以 `bin` 資料夾中的實際程式庫檔案在 `extensions.csproj` 檔案中所定義。 在本機開發時，您必須使用 Azure Functions Core Tools [註冊繫結擴充功能](functions-triggers-bindings.md#local-development-azure-functions-core-tools)。 

將 Functions 專案部署到 Azure 中的功能應用程式時，FunctionApp 資料夾的整個內容應包含在套件中，但不應包含資料夾本身。

## <a name="inputs"></a>輸入

在 Azure Functions 中輸入會分成兩個類別：觸發程序輸入和額外的輸入。 雖然它們在 `function.json` 中是不同的，在 Python 程式碼中的使用方式則相同。 讓我們以下列程式碼片段為例：

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "req",
      "direction": "in",
      "type": "httpTrigger",
      "authLevel": "anonymous",
      "route": "items/{id}"
    },
    {
      "name": "obj",
      "direction": "in",
      "type": "blob",
      "path": "samples/{id}",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```

```python
import azure.functions as func
import logging

def main(req: func.HttpRequest,
         obj: func.InputStream):

    logging.info(f'Python HTTP triggered function processed: {obj.read()}')
```

叫用此函式時，HTTP 要求會以 `req` 形式傳遞至函式。 系統會根據路由 URL 中的 _id_ 從 Azure Blob 儲存體擷取輸入，並在函式主體中以 `obj` 形式提供。

## <a name="outputs"></a>輸出

輸出可以使用傳回值和輸出參數來表示。 如果只有一個輸出，我們建議使用傳回的值。 若為多個輸出，您必須使用輸出參數。

若要使用函式的傳回值作為輸出繫結的值，應該將 `function.json` 中的繫結 `name` 屬性設定為 `$return`。

若要產生多個輸出，請使用 `azure.functions.Out` 介面提供的 `set()` 方法為繫結指派值。 例如，下列函式可以將訊息推送至佇列，同時也會傳回 HTTP 回應。

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "req",
      "direction": "in",
      "type": "httpTrigger",
      "authLevel": "anonymous"
    },
    {
      "name": "msg",
      "direction": "out",
      "type": "queue",
      "queueName": "outqueue",
      "connection": "AzureWebJobsStorage"
    },
    {
      "name": "$return",
      "direction": "out",
      "type": "http"
    }
  ]
}
```

```python
import azure.functions as func

def main(req: func.HttpRequest,
         msg: func.Out[func.QueueMessage]) -> str:

    message = req.params.get('body')
    msg.set(message)
    return message
```

## <a name="logging"></a>記錄

可以透過函式應用程式中的根 [`logging`](https://docs.python.org/3/library/logging.html#module-logging) 處理常式來存取 Azure Functions 執行階段記錄器。 這個記錄器會繫結至 Application Insights，並可讓您將函式執行期間遇到的警告和錯誤加上旗標。

下列範例在透過 HTTP 觸發程序叫用函式時，會記錄一則資訊訊息。

```python
import logging

def main(req):
    logging.info('Python HTTP trigger function processed a request.')
```

其他的記錄方法可讓您在不同追蹤層級寫入主控台記錄中︰

| 方法                 | 說明                                |
| ---------------------- | ------------------------------------------ |
| logging.**critical(_message_)**   | 在根記錄器上寫入層級為 CRITICAL (重大) 的訊息。  |
| logging.**error(_message_)**   | 在根記錄器上寫入層級為 ERROR (錯誤) 的訊息。    |
| logging.**warning(_message_)**    | 在根記錄器上寫入層級為 WARNING (警告) 的訊息。  |
| logging.**info(_message_)**    | 在根記錄器上寫入層級為 INFO (資訊) 的訊息。  |
| logging.**debug(_message_)** | 在根記錄器上寫入層級為 DEBUG (偵錯) 的訊息。  |

## <a name="importing-shared-code-into-a-function-module"></a>將共用程式碼匯入函式模組

與函式模組一起發行的 Python 模組必須使用相對的匯入語法匯入：

```python
from . import helpers  # Use more dots to navigate up the folder structure.
def main(req: func.HttpRequest):
    helpers.process_http_request(req)
```

或者，將共用程式碼放入獨立套件、將其發行至公用或私人 PyPI 執行個體，並將其指定為一般相依性。

## <a name="async"></a>非同步處理

由於每個函式應用程式只可存在單一 Python 處理序，因此建議使用 `async def` 陳述式將 Azure 函式實作為非同步處理協同程序。

```python
# Will be run with asyncio directly
async def main():
    await some_nonblocking_socket_io_op()
```

如果 main() 函式是同步處理函式 (非 `async` 限定詞)，我們會在 `asyncio` 執行緒集區自動執行該函式。

```python
# Would be run in an asyncio thread-pool
def main():
    some_blocking_socket_io()
```

## <a name="context"></a>Context

若要在執行期間取得函式的引動內容，請在其簽章中包含 `context`引數。 

例如︰

```python
import azure.functions

def main(req: azure.functions.HttpRequest,
            context: azure.functions.Context) -> str:
    return f'{context.invocation_id}'
```

**Context** 類別具有下列方法：

`function_directory`  
函式執行所在的目錄。

`function_name`  
函式的名稱。

`invocation_id`  
目前函式引動的識別碼。

## <a name="python-version-and-package-management"></a>Python 版本和套件管理

Azure Functions 目前僅支援 Python 3.6.x (官方 CPython 發佈)。

使用 Azure Functions Core Tools 或 Visual Studio Code 在本機進行開發時，將所需的套件名稱和版本新增到 `requirements.txt` 檔案，並使用 `pip` 進行安裝。

例如，可以使用下列要求和 pip 命令從 PyPl 安裝 `requests` 套件。

```bash
pip install requests
```

```txt
requests==2.19.1
```

```bash
pip install -r requirements.txt
```

當您準備發行時，請確保所有相依性都列在專案目錄根目錄下的 `requirements.txt` 檔案中。 若要成功地執行 Azure Functions，需求檔案應包含下列套件的最小需求：

```txt
azure-functions
azure-functions-worker
grpcio==1.14.1
grpcio-tools==1.14.1
protobuf==3.6.1
six==1.11.0
```

## <a name="publishing-to-azure"></a>發行到 Azure

如果您正在使用需要編譯器且不支援從 PyPl 安裝多版 Linux 相容的套件，則發行至 Azure 會失敗，並發生下列錯誤： 

```
There was an error restoring dependencies.ERROR: cannot install <package name - version> dependency: binary dependencies without wheels are not supported.  
The terminal process terminated with exit code: 1
```

若要自動建置和設定必要的二進位檔，請在本機電腦上[安裝 Docker](https://docs.docker.com/install/)，並執行下列命令以使用 [Azure Functions Core Tools](functions-run-local.md#v2) (func) 來發行。 在 Azure 中，請記得以您的函式應用程式名稱取代 `<app name>`。 

```bash
func azure functionapp publish <app name> --build-native-deps
```

基本上，Core Tools 會使用 docker 將 [mcr.microsoft.com/azure-functions/python](https://hub.docker.com/r/microsoft/azure-functions/) 映像作為本機電腦上的容器。 接著，它會利用此環境從來源發佈建置並安裝必要的模組，再將其封裝以最終部署至 Azure。

> [!NOTE]
> Core Tools (func) 會使用 PyInstaller 程式將使用者的程式碼和相依性凍結到單一獨立的可執行檔，以在 Azure 中執行。 這項功能目前為預覽狀態，且可能不會延伸到所有類型的 Python 套件。 如果無法匯入您的模組，請使用 `--no-bundler` 選項再次嘗試發佈。 
> ```
> func azure functionapp publish <app_name> --build-native-deps --no-bundler
> ```
> 如果仍然遇到問題，請[提出問題](https://github.com/Azure/azure-functions-core-tools/issues/new)並包含問題的描述讓我們知道問題所在。 


若要建置相依性並使用持續整合 (CI) 與持續傳遞 (CD) 系統進行發佈，您可以使用 [Azure 管線](https://docs.microsoft.com/azure/devops/pipelines/get-started-yaml?view=vsts)或[Travis CI 自訂指令碼](https://docs.travis-ci.com/user/deployment/script/)。 

以下是建置和發佈程序的範例 `azure-pipelines.yml` 指令碼。
```yml
pool:
  vmImage: 'Ubuntu 16.04'

steps:
- task: NodeTool@0
  inputs:
    versionSpec: '8.x'

- script: |
    set -e
    echo "deb [arch=amd64] https://packages.microsoft.com/repos/azure-cli/ wheezy main" | sudo tee /etc/apt/sources.list.d/azure-cli.list
    curl -L https://packages.microsoft.com/keys/microsoft.asc | sudo apt-key add -
    sudo apt-get install -y apt-transport-https
    echo "install Azure CLI..."
    sudo apt-get update && sudo apt-get install -y azure-cli
    npm i -g azure-functions-core-tools --unsafe-perm true
    echo "installing dotnet core"
    curl -sSL https://dot.net/v1/dotnet-install.sh | bash /dev/stdin --channel 2.0
- script: |
    set -e
    az login --service-principal --username "$(APP_ID)" --password "$(PASSWORD)" --tenant "$(TENANT_ID)" 
    func settings add FUNCTIONS_WORKER_RUNTIME python
    func extensions install
    func azure functionapp publish $(APP_NAME) --build-native-deps
```

以下是建置和發佈程序的範例 `.travis.yaml` 指令碼。

```yml
sudo: required

language: node_js

node_js:
  - "8"

services:
  - docker

before_install:
  - echo "deb [arch=amd64] https://packages.microsoft.com/repos/azure-cli/ wheezy main" | sudo tee /etc/apt/sources.list.d/azure-cli.list
  - curl -L https://packages.microsoft.com/keys/microsoft.asc | sudo apt-key add -
  - sudo apt-get install -y apt-transport-https
  - sudo apt-get update && sudo apt-get install -y azure-cli
  - npm i -g azure-functions-core-tools --unsafe-perm true


script:
  - az login --service-principal --username "$APP_ID" --password "$PASSWORD" --tenant "$TENANT_ID"
  - az account get-access-token --query "accessToken" | func azure functionapp publish $APP_NAME --build-native-deps

```

## <a name="known-issues-and-faq"></a>已知問題和常見問題集

所有已知問題和功能要求則會使用 [GitHub 問題](https://github.com/Azure/azure-functions-python-worker/issues)清單追蹤。 如果您遇到問題，但在 GitHub 中卻找不到此問題，請開啟新的問題，並包含問題的詳細說明。

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱下列資源：

* [Azure Functions 的最佳做法](functions-best-practices.md)
* [Azure Functions 觸發程序和繫結](functions-triggers-bindings.md)
* [Blob 儲存體繫結](functions-bindings-storage-blob.md)
* [HTTP 和 Webhook 繫結](functions-bindings-http-webhook.md)
* [佇列儲存體繫結](functions-bindings-storage-queue.md)
* [計時器觸發程序](functions-bindings-timer.md)
