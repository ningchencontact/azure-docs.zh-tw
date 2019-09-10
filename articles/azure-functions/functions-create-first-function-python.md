---
title: 在 Azure 中建立 HTTP 觸發的函式
description: 了解如何使用 Azure Functions Core Tools 和 Azure CLI 在 Azure 中建立您的第一個 Python 函式。
author: ggailey777
ms.author: glenga
ms.date: 04/24/2019
ms.topic: quickstart
ms.service: azure-functions
ms.custom: mvc
ms.devlang: python
manager: gwallace
ms.openlocfilehash: cb7f5a10169c8baaecae0fc1916a439d61bfbf7c
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/03/2019
ms.locfileid: "70170903"
---
# <a name="create-an-http-triggered-function-in-azure"></a>在 Azure 中建立 HTTP 觸發的函式

本文說明如何使用命令列工具建立在 Azure Functions 中執行的 Python 專案。 您所建立的函式會由 HTTP 要求觸發。 最後，您會在 Azure 中發行專案，使其以[無伺服器函式](functions-scale.md#consumption-plan)的形式執行。

本文是兩個 Azure Functions 快速入門中的第一個。 當您完成本文後，您可以[將 Azure 儲存體佇列輸出繫結新增至](functions-add-output-binding-storage-queue-python.md)您的函式。

## <a name="prerequisites"></a>必要條件

開始之前，您必須符合下列條件：

+ 安裝 [Python 3.6.x](https://www.python.org/downloads/)。

+ 安裝 [Azure Functions Core Tools](./functions-run-local.md#v2) 2.7.1575 版或更新版本。

+ 安裝 [Azure CLI](/cli/azure/install-azure-cli) 2.x 版或更新版本。

+ 有效的 Azure 訂用帳戶。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-and-activate-a-virtual-environment-optional"></a>建立並啟用虛擬環境 (選擇性)

若要在本機開發及測試 Python 函式，建議您使用 Python 3.6 環境。 執行下列命令來建立並啟用名為 `.venv` 的虛擬環境。

### <a name="bash"></a>Bash：

```bash
python3.6 -m venv .venv
source .venv/bin/activate
```

### <a name="powershell-or-a-windows-command-prompt"></a>PowerShell 或 Windows 命令提示字元：

```powershell
py -3.6 -m venv .venv
.venv\scripts\activate
```

其餘命令會在虛擬環境中執行。

## <a name="create-a-local-functions-project"></a>建立本機的 Functions 專案

Functions 專案相當於 Azure 中的函式應用程式。 它可以有多個全都共用相同本機和裝載設定的函式。

在虛擬環境中，選擇 **python** 作為您的背景工作執行階段，以執行下列命令。

```console
func init MyFunctionProj
```

此時會建立名為 _MyFunctionProj_ 的資料夾，其中包含下列三個檔案：

* `local.settings.json` 可在執行於本機時用來儲存應用程式設定和連接字串。 此檔案不會發行至 Azure。
* `requirements.txt` 包含在發行至 Azure 時所要安裝的套件清單。
* `host.json` 包含會對函式應用程式中的所有函式產生影響的全域設定選項。 此檔案會發行至 Azure。

瀏覽至新的 MyFunctionProj 資料夾：

```console
cd MyFunctionProj
```

## <a name="create-a-function"></a>建立函式

若要將函式新增至專案，請執行下列命令：

```console
func new
```

選擇 [HTTP 觸發程序]  範本，並輸入 `HttpTrigger` 作為函式名稱，然後按 Enter 鍵。

此時會建立名為 _HttpTrigger_ 的子資料夾，其中包含下列檔案：

* **function.json**：定義函式、觸發程序和其他繫結的組態檔。 請檢閱此檔案，並確認 `scriptFile` 的值指向包含函式的檔案，而叫用觸發程序和繫結則定義於 `bindings` 陣列中。

  每個繫結都需要方向、類型和唯一名稱。 HTTP 觸發程序具有 [`httpTrigger`](functions-bindings-http-webhook.md#trigger) 類型的輸入繫結，和 [`http`](functions-bindings-http-webhook.md#output) 類型的輸出繫結。

* **\_\_init\_\_.py**：HTTP 觸發函式的指令碼檔案。 請檢閱此指令碼，並確認其中包含預設的 `main()`。 來自觸發程序的 HTTP 資料會使用 `req` 指定的繫結參數傳至此函式。 `req` 定義於 function.json 中，是 [azure.functions.HttpRequest 類別](/python/api/azure-functions/azure.functions.httprequest)的執行個體。 

    傳回物件在 function.json 中定義為 `$return`，是 [azure.functions.HttpResponse 類別](/python/api/azure-functions/azure.functions.httpresponse)的執行個體。 若要深入了解，請參閱 [Azure Functions HTTP 觸發程序和繫結](functions-bindings-http-webhook.md)。

## <a name="run-the-function-locally"></a>在本機執行函式

下列命令會啟動函式應用程式，且該應用程式會使用 Azure 中的相同 Azure Functions 執行階段在本機執行。

```console
func host start
```

當 Functions 主機啟動時，它會寫入類似下列輸出的內容 (已截斷來提高可讀性)：

```output

                  %%%%%%
                 %%%%%%
            @   %%%%%%    @
          @@   %%%%%%      @@
       @@@    %%%%%%%%%%%    @@@
     @@      %%%%%%%%%%        @@
       @@         %%%%       @@
         @@      %%%       @@
           @@    %%      @@
                %%
                %

...

Content root path: C:\functions\MyFunctionProj
Now listening on: http://0.0.0.0:7071
Application started. Press Ctrl+C to shut down.

...

Http Functions:

        HttpTrigger: http://localhost:7071/api/HttpTrigger

[8/27/2018 10:38:27 PM] Host started (29486ms)
[8/27/2018 10:38:27 PM] Job host started
```

從執行階段輸出複製 `HttpTrigger` 函式的 URL，並將它貼到瀏覽器的網址列。 將查詢字串 `?name=<yourname>` 附加至此 URL 並執行要求。 下圖顯示瀏覽器中對於本機函式傳回之 GET 要求所做出的回應︰

![在瀏覽器中進行本機測試](./media/functions-create-first-function-python/function-test-local-browser.png)

您現在已經在本機執行您的函式，您可以開始在 Azure 中建立函數應用程式與其他必要資源。

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-function-app-in-azure"></a>在 Azure 中建立函式應用程式

函式應用程式會提供環境來執行函式程式碼。 它可讓您將多個函式群組為邏輯單位，以方便您管理、部署和共用資源。

執行下列命令，並請使用唯一函式應用程式名稱來替代 `<APP_NAME>` 預留位置，並使用儲存體帳戶名稱來替代 `<STORAGE_NAME>`。 `<APP_NAME>` 也是函式應用程式的預設 DNS 網域。 此名稱在 Azure 中的所有應用程式之間必須是唯一的。

```azurecli-interactive
az functionapp create --resource-group myResourceGroup --os-type Linux \
--consumption-plan-location westeurope  --runtime python \
--name <APP_NAME> --storage-account  <STORAGE_NAME>
```
> [!NOTE]
> Linux 和 Windows 應用程式無法裝載在相同的資源群組中。 如果您有名為 `myResourceGroup` 的現有資源群組，且其中包含 Windows 函式應用程式或 Web 應用程式，則必須使用不同的資源群組。

此命令也會在可用於監視及檢視記錄的同一個資源群組中，佈建相關聯的 Azure Application Insights 執行個體。

您現在已準備好將本機 Functions 專案發佈至 Azure 中的函式應用程式。

## <a name="deploy-the-function-app-project-to-azure"></a>將函式應用程式專案部署至 Azure

在 Azure 中建立函式應用程式之後，您可以使用 [`func azure functionapp publish`](functions-run-local.md#project-file-deployment) Core 工具命令來將專案程式碼部署至 Azure。 在這些範例中，請將 `<APP_NAME>` 取代為上一個步驟中您的應用程式名稱。

```command
func azure functionapp publish <APP_NAME> --build remote
```

`--build remote` 選項會透過部署套件中的檔案，從遠端在 Azure 中建立 Python 專案。 

您會看到類似下列的輸出，該輸出已截短，以提高可讀性：

```output
Getting site publishing info...
...

Preparing archive...
Uploading content...
Upload completed successfully.
Deployment completed successfully.
Syncing triggers...
Functions in myfunctionapp:
    HttpTrigger - [httpTrigger]
        Invoke url: https://myfunctionapp.azurewebsites.net/api/httptrigger?code=cCr8sAxfBiow548FBDLS1....
```

複製 `HttpTrigger` 的叫用 `Invoke url` 值，您現在可以使用它來測試 Azure 中的功能。 該 URL 包含一個 `code` 查詢字串值，它是您的函式金鑰。 此金鑰使其他人很難在 Azure 中呼叫 HTTP 觸發程序端點。

[!INCLUDE [functions-test-function-code](../../includes/functions-test-function-code.md)]

> [!NOTE]
> 若要針對已發佈的 Python 應用程式檢視近乎即時的記錄，建議您使用 [Application Insights 即時計量資料流](functions-monitoring.md#streaming-logs)

## <a name="next-steps"></a>後續步驟

您已使用 HTTP 觸發的函式建立 Python 函式專案，並在本機電腦上執行，然後部署到 Azure。 現在，請藉由下列作業擴充您的函式...

> [!div class="nextstepaction"]
> [新增 Azure 儲存體佇列輸出繫結](functions-add-output-binding-storage-queue-python.md)
