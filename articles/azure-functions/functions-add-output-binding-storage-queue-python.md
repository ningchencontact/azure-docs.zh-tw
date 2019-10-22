---
title: 將 Azure 儲存體佇列繫結新增至您的 Python 函式
description: 了解如何將 Azure 儲存體佇列輸出繫結新增至您的 Python 函式。
author: ggailey777
ms.author: glenga
ms.date: 10/02/2019
ms.topic: quickstart
ms.service: azure-functions
manager: gwallace
ms.openlocfilehash: 2307a296453247a5deee082aadb474f3641cce88
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/15/2019
ms.locfileid: "72329740"
---
# <a name="add-an-azure-storage-queue-binding-to-your-python-function"></a>將 Azure 儲存體佇列繫結新增至您的 Python 函式

[!INCLUDE [functions-add-storage-binding-intro](../../includes/functions-add-storage-binding-intro.md)]

本文說明如何整合您在[先前的快速入門文章](functions-create-first-function-python.md)中建立的函式與 Azure 儲存體佇列。 您新增至此函式的輸出繫結，會將資料從 HTTP 要求寫入至佇列中的訊息。

大部分的繫結都需要函式用來存取繫結服務的預存連接字串。 為了方便進行此連線，您可以使用您以函式應用程式建立的儲存體帳戶。 此帳戶的連線已儲存在名為 `AzureWebJobsStorage` 的應用程式設定中。  

## <a name="prerequisites"></a>必要條件

在開始這篇文章之前，請先完成 [Python 快速入門第 1 部分](functions-create-first-function-python.md)中的步驟。

[!INCLUDE [functions-cloud-shell-note](../../includes/functions-cloud-shell-note.md)]

## <a name="download-the-function-app-settings"></a>下載函式應用程式設定

[!INCLUDE [functions-app-settings-download-cli](../../includes/functions-app-settings-download-local-cli.md)]

## <a name="enable-extension-bundles"></a>啟用延伸模組搭售方案

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

您現在可以將儲存體輸出繫結新增至您的專案。

## <a name="add-an-output-binding"></a>新增輸出繫結

在函式中，每一種繫結都需要在 function.json 檔案中定義 `direction`、`type` 和唯一的 `name`。 定義這些屬性的方式會取決於您函式應用程式的語言。

[!INCLUDE [functions-add-output-binding-json](../../includes/functions-add-output-binding-json.md)]

## <a name="add-code-that-uses-the-output-binding"></a>新增會使用輸出繫結的程式碼

[!INCLUDE [functions-add-output-binding-python](../../includes/functions-add-output-binding-python.md)]

當您使用輸出繫結時，無須使用 Azure 儲存體 SDK 程式碼來進行驗證、取得佇列參考或寫入資料。 Functions 執行階段和佇列輸出繫結會為您進行這些工作。

## <a name="run-the-function-locally"></a>在本機執行函式

同樣地，請使用下列命令在本機啟動 Functions 執行階段：

```bash
func host start
```

> [!NOTE]  
> 由於您已啟用 host.json 中的擴充功能套件組合，因此[儲存體繫結擴充功能](functions-bindings-storage-blob.md#packages---functions-2x)已在啟動期間下載並安裝，連同其他 Microsoft 繫結擴充功能。

從執行階段輸出複製 `HttpTrigger` 函式的 URL，並將它貼到瀏覽器的網址列。 將查詢字串 `?name=<yourname>` 附加至此 URL 並執行要求。 您應該會在瀏覽器中看到與前一篇文章中相同的回應。

這次，輸出繫結也會在您的儲存體帳戶中建立名為 `outqueue` 的佇列，並新增具有這個相同字串的訊息。

接下來，您可以使用 Azure CLI 來檢視新的佇列，並確認已新增一則訊息。 您也可以使用 [Microsoft Azure 儲存體總管][Azure Storage Explorer]或在 [Azure 入口網站](https://portal.azure.com)中檢視您的佇列。

[!INCLUDE [functions-storage-account-set-cli](../../includes/functions-storage-account-set-cli.md)]

[!INCLUDE [functions-query-storage-cli](../../includes/functions-query-storage-cli.md)]

### <a name="redeploy-the-project"></a>重新部署專案 

若要更新已發佈的應用程式，請使用 [`func azure functionapp publish`](functions-run-local.md#project-file-deployment) Core Tools 命令，將您的專案程式碼部署至 Azure。 在此範例中，使用您的應用程式名稱取代 `<APP_NAME>`。

```command
func azure functionapp publish <APP_NAME> --build remote
```

同樣地，您可以使用 cURL 或瀏覽器來測試已部署的函式。 如同以往，請將查詢字串 `&name=<yourname>` 附加至 URL，如下列範例所示：

```bash
curl https://myfunctionapp.azurewebsites.net/api/httptrigger?code=cCr8sAxfBiow548FBDLS1....&name=<yourname>
```

您可以再次[檢查儲存體佇列訊息](#query-the-storage-queue)，以確認輸出繫結會如預期般在佇列中產生新訊息。

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

## <a name="next-steps"></a>後續步驟

您已更新 HTTP 觸發的函式，以將資料寫入至儲存體佇列。 若要深入了解如何使用 Python 開發 Azure Functions，請參閱 [Azure Functions Python 開發人員指南](functions-reference-python.md)和 [Azure Functions 觸發程序和繫結](functions-triggers-bindings.md)。 如需 Python 中完整函式專案的範例，請參閱 [ython 函式範例](/samples/browse/?products=azure-functions&languages=python)。 

接下來，您應為函式應用程式啟用 Application Insights 監視：

> [!div class="nextstepaction"]
> [啟用 Application Insights 整合](functions-monitoring.md#manually-connect-an-app-insights-resource)

[Azure Storage Explorer]: https://storageexplorer.com/