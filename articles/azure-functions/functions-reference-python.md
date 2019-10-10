---
title: 適用於 Azure Functions 的 Python 開發人員參考
description: 了解如何使用 Python 開發函式
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
keywords: azure functions, 函式, 事件處理, 動態計算, 無伺服器架構, Pythong
ms.service: azure-functions
ms.devlang: python
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 04/16/2018
ms.author: glenga
ms.openlocfilehash: 4fd73f528ac823a8e794a880f87dd5f8872e1251
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/10/2019
ms.locfileid: "72243268"
---
# <a name="azure-functions-python-developer-guide"></a>Azure Functions Python 開發人員指南

本文是使用 Python 開發 Azure Functions 的簡介。 本文假設您已閱讀過下列 [Azure Functions 開發人員指南](functions-reference.md)。 

如需 Python 中的獨立函式範例專案，請參閱 Python 函式[範例](/samples/browse/?products=azure-functions&languages=python)。 

## <a name="programming-model"></a>程式設計模型

Azure Functions 預期函式在 Python 腳本中是可處理輸入並產生輸出的無狀態方法。 根據預設，執行時間會預期在 @no__t 1 檔案中，將方法實作為稱為 @no__t 0 的全域方法。 您也可以[指定替代的進入點](#alternate-entry-point)。

來自觸發程式和系結的資料，會使用函式*json*檔案中定義的 `name` 屬性，透過方法屬性系結至函數。 例如，下面的_函數. json_描述由名為 `req` 的 HTTP 要求所觸發的簡單函式：

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

您也可以使用 Python 類型注釋，在函式中明確宣告屬性類型和傳回類型。 這可協助您使用許多 Python 程式碼編輯器所提供的 intellisense 和自動完成功能。

```python
import azure.functions


def main(req: azure.functions.HttpRequest) -> str:
    user = req.params.get('user')
    return f'Hello, {user}!'
```

請使用 [azure.functions.*](/python/api/azure-functions/azure.functions?view=azure-python) 套件中所包含的 Python 註釋，以將輸入和輸出繫結至方法。

## <a name="alternate-entry-point"></a>替代進入點

您可以選擇性地在*函數. json*檔案中指定 `scriptFile` 和 `entryPoint` 屬性，以變更函式的預設行為。 例如 _，下列函_式會指示執行時間在_main.py_檔案中使用 `customentry()` 方法，做為您的 Azure 函數的進入點。

```json
{
  "scriptFile": "main.py",
  "entryPoint": "customentry",
  "bindings": [
      ...
  ]
}
```

## <a name="folder-structure"></a>資料夾結構

Python 函式專案的資料夾結構如下列範例所示：

```
 FunctionApp
 | - MyFirstFunction
 | | - __init__.py
 | | - function.json
 | | - example.py
 | - MySecondFunction
 | | - __init__.py
 | | - function.json
 | - SharedCode
 | | - myFirstHelperFunction.py
 | | - mySecondHelperFunction.py
 | - host.json
 | - requirements.txt
```

其中有一個可用來設定函數應用程式的共用 [host.json](functions-host-json.md) 檔案。 每個函式都具有本身的程式碼檔案和繫結設定檔 (function.json)。 

共用程式碼應該放在個別的資料夾。 若要參考 SharedCode 資料夾中的模組，您可以使用下列語法：

```
from __app__.SharedCode import myFirstHelperFunction
```

若要參考函數的本機模組，您可以使用相對的匯入語法，如下所示：

```
from . import example
```

將函式專案部署至 Azure 中的函式應用程式時， *FunctionApp*資料夾的完整內容應該包含在套件中，而不是資料夾本身。

## <a name="triggers-and-inputs"></a>觸發程式和輸入

在 Azure Functions 中輸入會分成兩個類別：觸發程序輸入和額外的輸入。 雖然它們在 @no__t 0 檔案中不同，但在 Python 程式碼中的使用方式是相同的。  在本機執行時，觸發程式和輸入來源的連接字串或密碼會對應至 @no__t 0 檔案中的值，以及在 Azure 中執行時的應用程式設定。 

例如，下列程式碼示範兩者之間的差異：

```json
// function.json
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

```json
// local.settings.json
{
  "IsEncrypted": false,
  "Values": {
    "FUNCTIONS_WORKER_RUNTIME": "python",
    "AzureWebJobsStorage": "<azure-storage-connection-string>"
  }
}
```

```python
# __init__.py
import azure.functions as func
import logging


def main(req: func.HttpRequest,
         obj: func.InputStream):

    logging.info(f'Python HTTP triggered function processed: {obj.read()}')
```

叫用此函式時，HTTP 要求會以 `req` 形式傳遞至函式。 系統會根據路由 URL 中的_識別碼_從 Azure Blob 儲存體中抓取專案，並在函式主體中以 `obj` 的形式提供。  在這裡，指定的儲存體帳戶是在中找到的連接字串，這是函數應用程式所使用的相同儲存體帳戶。


## <a name="outputs"></a>outputs

輸出可以使用傳回值和輸出參數來表示。 如果只有一個輸出，我們建議使用傳回的值。 若為多個輸出，您必須使用輸出參數。

若要使用函式的傳回值作為輸出繫結的值，應該將 `function.json` 中的繫結 `name` 屬性設定為 `$return`。

若要產生多個輸出，請使用[`azure.functions.Out`](/python/api/azure-functions/azure.functions.out?view=azure-python)介面提供的 `set()` 方法，將值指派給系結。 例如，下列函式可以將訊息推送至佇列，同時也會傳回 HTTP 回應。

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

| 方法                 | 描述                                |
| ---------------------- | ------------------------------------------ |
| **`critical(_message_)`**   | 在根記錄器上寫入層級為 CRITICAL (重大) 的訊息。  |
| **`error(_message_)`**   | 在根記錄器上寫入層級為 ERROR (錯誤) 的訊息。    |
| **`warning(_message_)`**    | 在根記錄器上寫入層級為 WARNING (警告) 的訊息。  |
| **`info(_message_)`**    | 在根記錄器上寫入層級為 INFO (資訊) 的訊息。  |
| **`debug(_message_)`** | 在根記錄器上寫入層級為 DEBUG (偵錯) 的訊息。  |

若要深入瞭解記錄，請參閱[監視 Azure Functions](functions-monitoring.md)。

## <a name="http-trigger-and-bindings"></a>HTTP 觸發程式和系結

HTTP 觸發程式定義于 function. jon 檔案中。 系結的 `name` 必須符合函式中的具名引數。 在先前的範例中，會使用 `req` 的系結名稱。 這個參數是[HttpRequest]物件，而且會傳回[HttpResponse]物件。

您可以從[HttpRequest]物件取得要求標頭、查詢參數、路由參數和訊息內文。 

下列範例來自[Python 的 HTTP 觸發程式範本](https://github.com/Azure/azure-functions-templates/tree/dev/Functions.Templates/Templates/HttpTrigger-Python)。 

```python
def main(req: func.HttpRequest) -> func.HttpResponse:
    headers = {"my-http-header": "some-value"}

    name = req.params.get('name')
    if not name:
        try:
            req_body = req.get_json()
        except ValueError:
            pass
        else:
            name = req_body.get('name')
            
    if name:
        return func.HttpResponse(f"Hello {name}!", headers=headers)
    else:
        return func.HttpResponse(
             "Please pass a name on the query string or in the request body",
             headers=headers, status_code=400
        )
```

在此函式中，會從[HttpRequest]物件的 `params` 參數中取得 `name` 查詢參數的值。 JSON 編碼的訊息本文會使用 `get_json` 方法來讀取。 

同樣地，您可以在傳回的[HttpResponse]物件中設定回應訊息的 `status_code` 和 `headers`。

## <a name="concurrency"></a>並行

根據預設，Python 執行時間一次只能處理一個函式的調用。 此並行層級可能不足以符合下列一或多個條件：

+ 您正嘗試處理同時進行的多個調用。
+ 您正在處理大量 i/o 事件。
+ 您的應用程式與 i/o 系結。

在這些情況下，您可以透過非同步方式執行，並使用多個語言背景工作進程來改善效能。  

### <a name="async"></a>非同步處理

建議您使用 `async def` 語句，讓您的函式以非同步協同程式的方式執行。

```python
# Runs with asyncio directly

async def main():
    await some_nonblocking_socket_io_op()
```

當 `main()` 函數是同步的（沒有 `async` 限定詞）時，函式會自動在 @no__t 2 的執行緒集區中執行。

```python
# Runs in an asyncio thread-pool

def main():
    some_blocking_socket_io()
```

### <a name="use-multiple-language-worker-processes"></a>使用多個語言工作者進程

根據預設，每個函式主控制項實例都有單一的語言工作者進程。 不過，支援每個主控制項實例有多個語言工作者進程。 接著，函式呼叫可以平均分散在這些語言工作者進程中。 使用[FUNCTIONS_WORKER_PROCESS_COUNT](functions-app-settings.md#functions_worker_process_count)應用程式設定來變更此值。 

## <a name="context"></a>Context

若要在執行期間取得函數的調用內容，請在其簽章中包含[`context`](/python/api/azure-functions/azure.functions.context?view=azure-python)引數。 

例如:

```python
import azure.functions


def main(req: azure.functions.HttpRequest,
         context: azure.functions.Context) -> str:
    return f'{context.invocation_id}'
```

[**內容**](/python/api/azure-functions/azure.functions.context?view=azure-python)類具有下列字串屬性：

`function_directory`  
函式執行所在的目錄。

`function_name`  
函式的名稱。

`invocation_id`  
目前函式引動的識別碼。

## <a name="global-variables"></a>全域變數

不保證會保留您應用程式的狀態，以供未來執行之用。 不過，Azure Functions 執行時間通常會針對相同應用程式的多個執行重複使用相同的進程。 為了快取昂貴計算的結果，請將它宣告為全域變數。 

```python
CACHED_DATA = None


def main(req):
    global CACHED_DATA
    if CACHED_DATA is None:
        CACHED_DATA = load_json()

    # ... use CACHED_DATA in code
```

## <a name="environment-variables"></a>環境變數

在函數中，[應用程式設定](functions-app-settings.md)（例如服務連接字串）在執行期間會公開為環境變數。 您可以藉由宣告 `import os`，然後使用 `setting = os.environ["setting-name"]` 來存取這些設定。

下列範例會取得[應用程式設定](functions-how-to-use-azure-function-app-settings.md#settings)，其金鑰名為 `myAppSetting`：

```python
import logging
import os
import azure.functions as func

def main(req: func.HttpRequest) -> func.HttpResponse:

    # Get the setting named 'myAppSetting'
    my_app_setting_value = os.environ["myAppSetting"]
    logging.info(f'My app setting value:{my_app_setting_value}')
```

就本機開發而言，應用程式設定會[保留在本機的設定 json 檔案中](functions-run-local.md#local-settings-file)。  

## <a name="python-version-and-package-management"></a>Python 版本和套件管理

Azure Functions 目前僅支援 Python 3.6.x (官方 CPython 發佈)。

使用 Azure Functions Core Tools 或 Visual Studio Code 在本機進行開發時，將所需的套件名稱和版本新增到 `requirements.txt` 檔案，並使用 `pip` 進行安裝。

例如，可以使用下列要求和 pip 命令從 PyPl 安裝 `requests` 套件。

```txt
requests==2.19.1
```

```bash
pip install -r requirements.txt
```

## <a name="publishing-to-azure"></a>發行到 Azure

當您準備好要發行時，請確定您的所有相依性都列在 [*需求 .txt* ] 檔案中，該檔案位於專案目錄的根目錄中。 Azure Functions 可以[從遠端建立](functions-deployment-technologies.md#remote-build)這些相依性。

從發行排除的專案檔和資料夾（包括虛擬環境資料夾）會列在 funcignore 檔案中。  

若要部署至 Azure 並執行遠端組建，請使用下列命令：

```bash
func azure functionapp publish <app name> --build remote
```

如果您不是使用遠端組建，並使用需要編譯器的套件，且不支援從 PyPI 安裝許多 Linux 相容的輪，則發行至 Azure 而不在本機建立，將會因下列錯誤而失敗：

```
There was an error restoring dependencies.ERROR: cannot install <package name - version> dependency: binary dependencies without wheels are not supported.  
The terminal process terminated with exit code: 1
```

若要在本機建立並設定必要的二進位檔，請在您的本機電腦上[安裝 Docker](https://docs.docker.com/install/) ，然後執行下列命令，使用[Azure Functions Core Tools](functions-run-local.md#v2) （func）進行發佈。 在 Azure 中，請記得以您的函式應用程式名稱取代 `<app name>`。 

```bash
func azure functionapp publish <app name> --build-native-deps
```

基本上，Core Tools 會使用 docker 將 [mcr.microsoft.com/azure-functions/python](https://hub.docker.com/r/microsoft/azure-functions/) 映像作為本機電腦上的容器。 使用此環境時，它會從來源散發建立並安裝必要的模組，然後再將其封裝為最終部署至 Azure。

若要建立您的相依性，並使用持續傳遞（CD）系統發佈，請[使用 Azure Pipelines](functions-how-to-azure-devops.md)。 

## <a name="unit-testing"></a>單元測試

以 Python 撰寫的函式可以使用標準測試架構來測試，就像其他 Python 程式碼一樣。 對於大部分的系結，您可以從 `azure.functions` 封裝建立適當類別的實例，以建立模擬輸入物件。 因為[@no__t 1](https://pypi.org/project/azure-functions/)套件無法立即使用，請務必透過您的 @no__t 2 檔案加以安裝，如上面的[Python 版本和套件管理](#python-version-and-package-management)一節中所述。

例如，下列是 HTTP 觸發函式的模擬測試：

```json
{
  "scriptFile": "httpfunc.py",
  "entryPoint": "my_function",
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
  ]
}
```

```python
# myapp/httpfunc.py
import azure.functions as func
import logging

def my_function(req: func.HttpRequest) -> func.HttpResponse:
    logging.info('Python HTTP trigger function processed a request.')

    name = req.params.get('name')
    if not name:
        try:
            req_body = req.get_json()
        except ValueError:
            pass
        else:
            name = req_body.get('name')

    if name:
        return func.HttpResponse(f"Hello {name}")
    else:
        return func.HttpResponse(
             "Please pass a name on the query string or in the request body",
             status_code=400
        )
```

```python
# myapp/test_httpfunc.py
import unittest

import azure.functions as func
from httpfunc import my_function


class TestFunction(unittest.TestCase):
    def test_my_function(self):
        # Construct a mock HTTP request.
        req = func.HttpRequest(
            method='GET',
            body=None,
            url='/api/HttpTrigger',
            params={'name': 'Test'})

        # Call the function.
        resp = my_function(req)

        # Check the output.
        self.assertEqual(
            resp.get_body(),
            b'Hello Test',
        )
```

以下是另一個範例，其中包含已觸發佇列的函式：

```python
# myapp/__init__.py
import azure.functions as func


def my_function(msg: func.QueueMessage) -> str:
    return f'msg body: {msg.get_body().decode()}'
```

```python
# myapp/test_func.py
import unittest

import azure.functions as func
from . import my_function


class TestFunction(unittest.TestCase):
    def test_my_function(self):
        # Construct a mock Queue message.
        req = func.QueueMessage(
            body=b'test')

        # Call the function.
        resp = my_function(req)

        # Check the output.
        self.assertEqual(
            resp,
            'msg body: test',
        )
```

## <a name="known-issues-and-faq"></a>已知問題和常見問題集

所有已知問題和功能要求則會使用 [GitHub 問題](https://github.com/Azure/azure-functions-python-worker/issues)清單追蹤。 如果您遇到問題，但在 GitHub 中卻找不到此問題，請開啟新的問題，並包含問題的詳細說明。

### <a name="cross-origin-resource-sharing"></a>跨原始資源共用

Azure Functions 支援跨原始來源資源分享（CORS）。 CORS 會[在入口網站中](functions-how-to-use-azure-function-app-settings.md#cors)以及透過[Azure CLI](/cli/azure/functionapp/cors)進行設定。 CORS 允許的原始來源清單適用于函數應用層級。 啟用 CORS 後，回應會包含 `Access-Control-Allow-Origin` 標頭。 如需詳細資訊，請參閱 [跨原始來源資源分享](functions-how-to-use-azure-function-app-settings.md#cors)(英文)。

Python 函式應用程式[目前不支援](https://github.com/Azure/azure-functions-python-worker/issues/444)允許的原始來源清單。 由於這項限制，您必須在 HTTP 函式中明確設定 @no__t 0 標頭，如下列範例所示：

```python
def main(req: func.HttpRequest) -> func.HttpResponse:

    # Define the allow origin headers.
    headers = {"Access-Control-Allow-Origin": "https://contoso.com"}

    # Set the headers in the response.
    return func.HttpResponse(
            f"Allowed origin '{headers}'.",
            headers=headers, status_code=200
    )
``` 

請確定您也更新了函數. json，以支援 OPTIONS HTTP 方法：

```json
    ...
      "methods": [
        "get",
        "post",
        "options"
      ]
    ...
```

Chrome 瀏覽器會使用這個方法來協調允許的原始來源清單。 

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱下列資源：

* [Azure Functions 套件 API 檔](/python/api/azure-functions/azure.functions?view=azure-python)
* [Azure Functions 的最佳做法](functions-best-practices.md)
* [Azure Functions 觸發程序和繫結](functions-triggers-bindings.md)
* [Blob 儲存體繫結](functions-bindings-storage-blob.md)
* [HTTP 和 Webhook 繫結](functions-bindings-http-webhook.md)
* [佇列儲存體繫結](functions-bindings-storage-queue.md)
* [計時器觸發程序](functions-bindings-timer.md)


[HttpRequest]: /python/api/azure-functions/azure.functions.httprequest?view=azure-python
[HttpResponse]: /python/api/azure-functions/azure.functions.httpresponse?view=azure-python
