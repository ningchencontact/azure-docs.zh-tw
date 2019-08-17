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
ms.openlocfilehash: 0cdd7f291b43f442b8471a19f515e4a2d12b4e74
ms.sourcegitcommit: 39d95a11d5937364ca0b01d8ba099752c4128827
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/16/2019
ms.locfileid: "69562867"
---
# <a name="azure-functions-python-developer-guide"></a>Azure Functions Python 開發人員指南

本文是使用 Python 開發 Azure Functions 的簡介。 本文假設您已閱讀過下列 [Azure Functions 開發人員指南](functions-reference.md)。

## <a name="programming-model"></a>程式設計模型

Python 指令碼中的 Azure 函式應該是無狀態方法，處理輸入及產生輸出。 根據預設, 執行時間會預期方法會實作為`main()` `__init__.py`在檔案中呼叫的全域方法。

您可以藉由在*函數. json*檔案`scriptFile`中`entryPoint`指定和屬性來變更預設設定。 例如, 下列函式會指示執行時間在_main.py_檔案中使用`customentry()`方法, 做為您的 Azure 函式的進入點。

```json
{
  "scriptFile": "main.py",
  "entryPoint": "customentry",
  ...
}
```

來自觸發程式和系結的資料會使用函式*json*檔案中`name`定義的屬性, 透過方法屬性系結至函式。 例如, 下面的_函數. json_說明由 HTTP 要求`req`所觸發的簡單函式:

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

(選擇性) 若要利用程式碼編輯器所提供的 intellisense 和自動完成功能, 您也可以使用 Python 類型注釋, 在函式中宣告屬性類型和傳回類型。 

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
```

其中有一個可用來設定函數應用程式的共用 [host.json](functions-host-json.md) 檔案。 每個函式都具有本身的程式碼檔案和繫結設定檔 (function.json)。 

共用程式碼應該放在個別的資料夾。 若要參考 SharedCode 資料夾中的模組，您可以使用下列語法：

```
from __app__.SharedCode import myFirstHelperFunction
```

將函式專案部署至 Azure 中的函式應用程式時, *FunctionApp*資料夾的完整內容應該包含在套件中, 而不是資料夾本身。

## <a name="triggers-and-inputs"></a>觸發程式和輸入

在 Azure Functions 中輸入會分成兩個類別：觸發程序輸入和額外的輸入。 雖然它們在檔案中`function.json`不同, 但在 Python 程式碼中的使用方式是相同的。  在本機執行時, 觸發程式和輸入來源的連接字串或`local.settings.json`秘密會對應至檔案中的值, 以及在 Azure 中執行時的應用程式設定。 

例如, 下列程式碼示範兩者之間的差異:

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

叫用此函式時，HTTP 要求會以 `req` 形式傳遞至函式。 系統會根據路由 URL 中的_識別碼_從 Azure Blob 儲存體中抓取專案, 並在函式主體中`obj`以的形式提供。  在這裡, 指定的儲存體帳戶是在中`AzureWebJobsStorage`找到的連接字串, 這是函數應用程式所使用的相同儲存體帳戶。


## <a name="outputs"></a>outputs

輸出可以使用傳回值和輸出參數來表示。 如果只有一個輸出，我們建議使用傳回的值。 若為多個輸出，您必須使用輸出參數。

若要使用函式的傳回值作為輸出繫結的值，應該將 `function.json` 中的繫結 `name` 屬性設定為 `$return`。

若要產生多個輸出, `set()`請使用[`azure.functions.Out`](/python/api/azure-functions/azure.functions.out?view=azure-python)介面提供的方法, 將值指派給系結。 例如，下列函式可以將訊息推送至佇列，同時也會傳回 HTTP 回應。

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
| logging.**critical(_message_)**   | 在根記錄器上寫入層級為 CRITICAL (重大) 的訊息。  |
| logging.**error(_message_)**   | 在根記錄器上寫入層級為 ERROR (錯誤) 的訊息。    |
| logging.**warning(_message_)**    | 在根記錄器上寫入層級為 WARNING (警告) 的訊息。  |
| logging.**info(_message_)**    | 在根記錄器上寫入層級為 INFO (資訊) 的訊息。  |
| logging.**debug(_message_)** | 在根記錄器上寫入層級為 DEBUG (偵錯) 的訊息。  |

## <a name="async"></a>非同步處理

建議您使用`async def`語句, 將您的 Azure Function 撰寫為非同步協同程式。

```python
# Will be run with asyncio directly


async def main():
    await some_nonblocking_socket_io_op()
```

如果 main () 函式是同步的 ( `async`沒有辨識符號), 我們會自動`asyncio`線上程集區中執行函數。

```python
# Would be run in an asyncio thread-pool


def main():
    some_blocking_socket_io()
```

## <a name="context"></a>內容

若要在執行期間取得函數的調用內容, 請在[`context`](/python/api/azure-functions/azure.functions.context?view=azure-python)其簽章中包含引數。 

例如:

```python
import azure.functions


def main(req: azure.functions.HttpRequest,
         context: azure.functions.Context) -> str:
    return f'{context.invocation_id}'
```

[**內容**](/python/api/azure-functions/azure.functions.context?view=azure-python)類具有下列方法:

`function_directory`  
函式執行所在的目錄。

`function_name`  
函式的名稱。

`invocation_id`  
目前函式引動的識別碼。

## <a name="global-variables"></a>全域變數

不保證會保留您應用程式的狀態, 以供未來執行之用。 不過, Azure Functions 執行時間通常會針對相同應用程式的多個執行重複使用相同的進程。 為了快取昂貴計算的結果, 請將它宣告為全域變數。 

```python
CACHED_DATA = None


def main(req):
    global CACHED_DATA
    if CACHED_DATA is None:
        CACHED_DATA = load_json()

    # ... use CACHED_DATA in code
```

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

當您準備好要發行時, 請確定您的所有相依性都列在 [*需求 .txt* ] 檔案中, 該檔案位於專案目錄的根目錄中。 如果您正在使用需要編譯器且不支援從 PyPl 安裝多版 Linux 相容的套件，則發行至 Azure 會失敗，並發生下列錯誤： 

```
There was an error restoring dependencies.ERROR: cannot install <package name - version> dependency: binary dependencies without wheels are not supported.  
The terminal process terminated with exit code: 1
```

若要自動建置和設定必要的二進位檔，請在本機電腦上[安裝 Docker](https://docs.docker.com/install/)，並執行下列命令以使用 [Azure Functions Core Tools](functions-run-local.md#v2) (func) 來發行。 在 Azure 中，請記得以您的函式應用程式名稱取代 `<app name>`。 

```bash
func azure functionapp publish <app name> --build-native-deps
```

基本上，Core Tools 會使用 docker 將 [mcr.microsoft.com/azure-functions/python](https://hub.docker.com/r/microsoft/azure-functions/) 映像作為本機電腦上的容器。 接著，它會利用此環境從來源發佈建置並安裝必要的模組，再將其封裝以最終部署至 Azure。

若要建立您的相依性, 並使用持續傳遞 (CD) 系統進行發佈, 請[使用 Azure DevOps 管線](functions-how-to-azure-devops.md)。 

## <a name="unit-testing"></a>單元測試

以 Python 撰寫的函式可以使用標準測試架構來測試, 就像其他 Python 程式碼一樣。 對於大部分的系結, 您可以從`azure.functions`封裝建立適當類別的實例, 以建立模擬輸入物件。 因為套件[`azure.functions`](https://pypi.org/project/azure-functions/)無法立即使用, 請務必透過您`requirements.txt`的檔案進行安裝, 如上面的[Python 版本和套件管理](#python-version-and-package-management)一節中所述。

例如, 下列是 HTTP 觸發函式的模擬測試:

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

以下是另一個範例, 其中包含已觸發佇列的函式:

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

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱下列資源：

* [Azure Functions 套件 API 檔](/python/api/azure-functions/azure.functions?view=azure-python)
* [Azure Functions 的最佳做法](functions-best-practices.md)
* [Azure Functions 觸發程序和繫結](functions-triggers-bindings.md)
* [Blob 儲存體繫結](functions-bindings-storage-blob.md)
* [HTTP 和 Webhook 繫結](functions-bindings-http-webhook.md)
* [佇列儲存體繫結](functions-bindings-storage-queue.md)
* [計時器觸發程序](functions-bindings-timer.md)
