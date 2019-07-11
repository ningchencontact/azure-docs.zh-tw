---
title: 將 Azure 儲存體佇列繫結新增至您的 Python 函式
description: 了解如何使用 Azure CLI 和 Functions Core Tools 將 Azure 儲存體佇列輸出繫結新增至您的 Python 函式。
services: functions
keywords: ''
author: ggailey777
ms.author: glenga
ms.date: 04/24/2019
ms.topic: quickstart
ms.service: azure-functions
ms.custom: mvc
ms.devlang: python
manager: jeconnoc
ms.openlocfilehash: c2565a5549cbca08b987883e5905f09070b5ab2c
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/28/2019
ms.locfileid: "67443192"
---
# <a name="add-an-azure-storage-queue-binding-to-your-python-function"></a>將 Azure 儲存體佇列繫結新增至您的 Python 函式

Azure Functions 可讓您直接將 Azure 服務和其他資源連線至函式，而不需要自行撰寫整合程式碼。 這些*繫結*同時代表輸入和輸出，會宣告於函式定義內。 繫結中的資料會提供給函式作為參數。 觸發程序是一種特殊的輸入繫結。 函式雖然只有一個觸發程序，但可以有多個輸入和輸出繫結。 若要深入了解，請參閱 [Azure Functions 觸發程序和繫結概念](functions-triggers-bindings.md)。

本文說明如何整合您在[先前的快速入門文章](functions-create-first-function-python.md)中建立的函式與 Azure 儲存體佇列。 您新增至此函式的輸出繫結，會將資料從 HTTP 要求寫入至佇列中的訊息。 

大部分的繫結都需要函式用來存取繫結服務的預存連接字串。 為了方便作業，您可以使用您以函式應用程式建立的儲存體帳戶。 此帳戶的連線已儲存在名為 `AzureWebJobsStorage` 的應用程式設定中。  

## <a name="prerequisites"></a>必要條件

在開始這篇文章之前，請先完成 [Python 快速入門第 1 部分](functions-create-first-function-python.md)中的步驟。

## <a name="download-the-function-app-settings"></a>下載函式應用程式設定

在先前的快速入門文章中，您已在 Azure 中建立函式應用程式與必要的儲存體帳戶。 此帳戶的連接字串會安全地儲存在 Azure 的應用程式設定中。 在本文中，您會將訊息寫入至相同帳戶中的儲存體佇列。 在本機執行函式時若要連線至儲存體帳戶，您必須將應用程式設定下載到 local.settings.json 檔案。 執行下列 Azure Functions Core Tools 命令以將設定下載至 local.settings.json，並將 `<APP_NAME>` 取代為上一篇文章中的函式應用程式名稱：

```bash
func azure functionapp fetch-app-settings <APP_NAME>
```

您可能需要登入 Azure 帳戶。

> [!IMPORTANT]  
> local.settings.json 檔案中包含秘密，因此絕不可發行，且應從原始檔控制中排除。

您需要值 `AzureWebJobsStorage`，這是儲存體帳戶的連接字串。 您將使用此連線來確認輸出繫結會如預期般運作。

## <a name="enable-extension-bundles"></a>啟用延伸模組搭售方案

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

現在，您可以將儲存體輸出繫結新增至您的專案。

## <a name="add-an-output-binding"></a>新增輸出繫結

在函式中，每一種繫結都需要在 function.json 檔案中定義 `direction`、`type` 和唯一的 `name`。 根據繫結類型，可能需要額外的屬性。 [佇列輸出組態](functions-bindings-storage-queue.md#output---configuration)會說明 Azure 儲存體佇列繫結所需的欄位。

若要建立繫結，您必須將繫結組態物件新增至 `function.json` 檔案。 請編輯 HttpTrigger 資料夾中的 function.json 檔案，以將物件新增至具有下列屬性的 `bindings` 陣列：

| 屬性 | 值 | 說明 |
| -------- | ----- | ----------- |
| **`name`** | `msg` | 識別您的程式碼中參考之繫結參數的名稱。 |
| **`type`** | `queue` | 此繫結是 Azure 儲存體佇列繫結。 |
| **`direction`** | `out` | 此繫結為輸出繫結。 |
| **`queueName`** | `outqueue` | 作為繫結寫入目標的佇列名稱。 當 *queueName* 不存在，繫結會在第一次使用時加以建立。 |
| **`connection`** | `AzureWebJobsStorage` | 包含儲存體帳戶連接字串之應用程式設定的名稱。 `AzureWebJobsStorage` 設定會包含您以函式應用程式建立之儲存體帳戶的連接字串。 |

Function.json 檔案現在應該會如下列範例所示：

```json
{
  "scriptFile": "__init__.py",
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
    },
  {
      "type": "queue",
      "direction": "out",
      "name": "msg",
      "queueName": "outqueue",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```

## <a name="add-code-that-uses-the-output-binding"></a>新增會使用輸出繫結的程式碼

在繫結設定後，您即可開始使用繫結的 `name` 加以存取，作為函式簽章中的方法屬性。 在下列範例中，`msg` 是 [`azure.functions.InputStream class`](/python/api/azure-functions/azure.functions.httprequest) 的執行個體。

```python
import logging

import azure.functions as func


def main(req: func.HttpRequest, msg: func.Out[func.QueueMessage]) -> str:

    name = req.params.get('name')
    if not name:
        try:
            req_body = req.get_json()
        except ValueError:
            pass
        else:
            name = req_body.get('name')

    if name:
        msg.set(name)
        return func.HttpResponse(f"Hello {name}!")
    else:
        return func.HttpResponse(
            "Please pass a name on the query string or in the request body",
            status_code=400
        )
```

藉由使用輸出繫結，您無須使用 Azure 儲存體 SDK 程式碼來進行驗證、取得佇列參考或寫入資料。 Functions 執行階段和佇列輸出繫結會為您進行這些工作。

## <a name="run-the-function-locally"></a>在本機執行函式

同樣地，請使用下列命令在本機啟動 Functions 執行階段：

```bash
func host start
```

> [!NOTE]  
> 由於您已在前一篇文章啟用 host.json 中的擴充功能套件組合，因此[儲存體繫結擴充功能](functions-bindings-storage-blob.md#packages---functions-2x)已在啟動期間下載並安裝，連同其他 Microsoft 繫結擴充功能。

從執行階段輸出複製 `HttpTrigger` 函式的 URL，並將它貼到瀏覽器的網址列。 將查詢字串 `?name=<yourname>` 附加至此 URL 並執行要求。 您應該會在瀏覽器中看到與前一篇文章中相同的回應。

這次，輸出繫結也會在您的儲存體帳戶中建立名為 `outqueue` 的佇列，並新增具有這個相同字串的訊息。

接下來，您可以使用 Azure CLI 來檢視新的佇列，並確認已新增一則訊息。 您也可以使用 [Microsoft Azure 儲存體總管][Azure Storage Explorer]或在 [Azure 入口網站](https://portal.azure.com)中檢視您的佇列。

### <a name="set-the-storage-account-connection"></a>設定儲存體帳戶連線

開啟 local.settings.json 檔案並複製 `AzureWebJobsStorage` 的值，這是儲存體帳戶的連接字串。 請使用下列 Bash 命令將 `AZURE_STORAGE_CONNECTION_STRING` 環境變數設為此連接字串：

```azurecli-interactive
export AZURE_STORAGE_CONNECTION_STRING=<STORAGE_CONNECTION_STRING>
```

在 `AZURE_STORAGE_CONNECTION_STRING` 環境變數中設定連接字串後，您將可直接存取您的儲存體帳戶，而不需要每次都提供驗證。

### <a name="query-the-storage-queue"></a>查詢儲存體佇列

您可以使用 [`az storage queue list`](/cli/azure/storage/queue#az-storage-queue-list) 命令檢視您帳戶中的儲存體佇列，如下列範例所示：

```azurecli-interactive
az storage queue list --output tsv
```

此命令的輸出會包含名為 `outqueue` 的佇列，這是函式執行時所建立的佇列。

接著，請使用 [`az storage message peek`](/cli/azure/storage/message#az-storage-message-peek) 命令檢視此佇列中的訊息，如下列範例所示。

```azurecli-interactive
echo `echo $(az storage message peek --queue-name outqueue -o tsv --query '[].{Message:content}') | base64 --decode`
```

傳回的字串應該會與您傳送用來測試函式的訊息相同。

> [!NOTE]  
> 上述範例解碼了 base64 傳回的字串。 這是因為佇列儲存體繫結會以 [base64 字串](functions-bindings-storage-queue.md#encoding)的形式寫入和讀取自 Azure 儲存體。

現在，您可以將更新的函式應用程式重新發行至 Azure。

[!INCLUDE [functions-publish-project](../../includes/functions-publish-project.md)]

同樣地，您可以使用 cURL 或瀏覽器來測試已部署的函式。 如同以往，請將查詢字串 `&name=<yourname>` 附加至 URL，如下列範例所示：

```bash
curl https://myfunctionapp.azurewebsites.net/api/httptrigger?code=cCr8sAxfBiow548FBDLS1....&name=<yourname>
```

您可以[檢查儲存體佇列訊息](#query-the-storage-queue)，以確認輸出繫結再次在佇列中產生了新的訊息。

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

## <a name="next-steps"></a>後續步驟

您已更新 HTTP 觸發的函式，以將資料寫入至儲存體佇列。 若要深入了解如何使用 Python 開發 Azure Functions，請參閱 [Azure Functions Python 開發人員指南](functions-reference-python.md)和 [Azure Functions 觸發程序和繫結](functions-triggers-bindings.md)。

接下來，您應為函式應用程式啟用 Application Insights 監視：

> [!div class="nextstepaction"]
> [啟用 Application Insights 整合](functions-monitoring.md#manually-connect-an-app-insights-resource)

[Azure Storage Explorer]: https://storageexplorer.com/