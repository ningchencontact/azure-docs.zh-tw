---
title: 在 Azure 中建立 HTTP 觸發的函式
description: 了解如何使用 Azure Functions Core Tools 和 Azure CLI 在 Azure 中建立您的第一個 Python 函式。
author: ggailey777
ms.author: glenga
ms.date: 09/11/2019
ms.topic: quickstart
ms.service: azure-functions
ms.custom: mvc
ms.devlang: python
manager: gwallace
ms.openlocfilehash: f2602e5a13f83090291656e7062c74c245bc6568
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/21/2019
ms.locfileid: "72693340"
---
# <a name="create-an-http-triggered-function-in-azure"></a>在 Azure 中建立 HTTP 觸發的函式

本文說明如何使用命令列工具建立在 Azure Functions 中執行的 Python 專案。 您也可以建立一個由 HTTP 要求所觸發的函式。 最後，您會在 Azure 中發行專案，使其以[無伺服器函式](functions-scale.md#consumption-plan)的形式執行。

本文是 Azure Functions 的兩個 Python 快速入門中的第一個。 當您完成本快速入門後，您可以[將 Azure 儲存體佇列輸出繫結新增至](functions-add-output-binding-storage-queue-python.md)您的函式。

## <a name="prerequisites"></a>必要條件

開始之前，您必須：

+ 安裝 [Python 3.6.8](https://www.python.org/downloads/)。 此 Python 版本已通過 Functions 的驗證。 不支援 3.7 和更新版本。

+ 安裝 [Azure Functions Core Tools](./functions-run-local.md#v2) 2.7.1575 版或更新版本。

+ 安裝 [Azure CLI](/cli/azure/install-azure-cli) 2.x 版或更新版本。

+ 擁有有效的 Azure 訂用帳戶。

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-and-activate-a-virtual-environment-optional"></a>建立並啟用虛擬環境 (選擇性)

您應使用 Python 3.6. x 環境，在本機開發 Python 功能。 執行下列命令來建立並啟用名為 `.venv` 的虛擬環境。

> [!NOTE]
> 如果 Python 未在您的 Linux 發行版本上安裝 venv，您可以使用下列命令來安裝它：
> ```command
> sudo apt-get install python3-venv

### <a name="bash"></a>Bash：

```bash
python -m venv .venv
source .venv/bin/activate
```

### <a name="powershell-or-a-windows-command-prompt"></a>PowerShell 或 Windows 命令提示字元：

```powershell
py -m venv .venv
.venv\scripts\activate
```

您現在已啟用虛擬環境，請在其中執行其餘的命令。 若要離開虛擬環境，請執行 `deactivate`。

## <a name="create-a-local-functions-project"></a>建立本機 Functions 專案

Functions 專案相當於 Azure 中的函式應用程式。 它可以有多個全都共用相同本機和裝載設定的函式。

1. 在虛擬環境中，執行下列命令：

    ```console
    func init MyFunctionProj
    ```

1. 選取 **python** 作為您的背景工作角色執行階段。

    此命令會建立 _MyFunctionProj_ 資料夾。 其中包含下列三個檔案：

    * local.settings.json  ：可在執行於本機時用來儲存應用程式設定和連接字串。 此檔案不會發行至 Azure。
    * requirements.txt  ：包含系統將在發佈至 Azure 時安裝的套件清單。
    * host.json  ：包含會對函式應用程式中的所有函式產生影響的全域設定選項。 此檔案會發行至 Azure。

1. 移至新的 MyFunctionProj  資料夾：

    ```console
    cd MyFunctionProj
    ```

## <a name="create-a-function"></a>建立函式

將函式新增至新專案。

1. 若要將函式新增至專案，請執行下列命令：

    ```console
    func new
    ```

1. 使用向下箭號來選取 [HTTP 觸發程式]  範本。

1. 當系統提示您輸入函式名稱時，請輸入 HttpTrigger  ，然後按 Enter。

這些命令會建立名為_HttpTrigger_ 的子資料夾。 其中包含下列檔案：

* *function.json*：定義函式、觸發程序和其他繫結的組態檔。 請注意在此檔案中，`scriptFile` 的值會指向包含此函式的檔案，而 `bindings` 陣列會定義引動過程觸發程序和繫結。

    每個繫結都需要方向、類型和唯一名稱。 HTTP 觸發程序具有 [`httpTrigger`](functions-bindings-http-webhook.md#trigger) 類型的輸入繫結，和 [`http`](functions-bindings-http-webhook.md#output) 類型的輸出繫結。

* *\_\_init\_\_.py*：HTTP 觸發函式的指令碼檔案。 請注意，此指令碼具有預設 `main()` 值。 來自觸發程序的 HTTP 資料會使用名為 `binding parameter` 的 `req` 傳遞至函數。 `req` (定義於 function.json 中) 是 [azure.functions.HttpRequest 類別](/python/api/azure-functions/azure.functions.httprequest)的執行個體。 

    傳回物件 (在 function.json  中定義為 `$return`)，是 [azure.functions.HttpResponse 類別](/python/api/azure-functions/azure.functions.httpresponse)的執行個體。 若要深入了解，請參閱 [Azure Functions HTTP 觸發程序和繫結](functions-bindings-http-webhook.md)。

## <a name="run-the-function-locally"></a>在本機執行函式

此函式會使用 Azure Functions 執行階段在本機執行。

1. 此命令會啟動函式應用程式：

    ```console
    func host start
    ```

    當 Azure Functions 主機啟動時，它會寫入類似下列輸出的內容。 輸出會在此截斷，以便更清楚地閱讀：

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

1. 從執行階段輸出複製 `HttpTrigger` 函式的 URL，並將它貼到瀏覽器的網址列。

1. 將查詢字串 `?name=<yourname>` 附加至此 URL 並執行要求。 下列螢幕擷取畫面會顯示本機函式傳回到瀏覽器的 GET 要求回應︰

    ![在瀏覽器中進行本機驗證](./media/functions-create-first-function-python/function-test-local-browser.png)

1. 選取 Ctrl+C 以關閉您的函式應用程式。

您現在已經在本機執行您的函式，您可以開始在 Azure 中建立函數應用程式與其他必要資源。

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-function-app-in-azure"></a>在 Azure 中建立函式應用程式

函式應用程式會提供環境來執行函式程式碼。 它可讓您將多個函式群組為邏輯單位，以方便您管理、部署和共用資源。

執行下列命令。 使用唯一的函式應用程式名稱取代 `<APP_NAME>`。 使用儲存體帳戶名稱取代 `<STORAGE_NAME>`。 `<APP_NAME>` 也是函式應用程式的預設 DNS 網域。 此名稱在 Azure 中的所有應用程式之間必須是唯一的。

> [!NOTE]
> 您無法在相同的資源群組中裝載 Linux 和 Windows 應用程式。 如果您有名為 `myResourceGroup` 的現有資源群組，且其中包含 Windows 函式應用程式或 Web 應用程式，則必須使用不同的資源群組。

```azurecli-interactive
az functionapp create --resource-group myResourceGroup --os-type Linux \
--consumption-plan-location westeurope  --runtime python \
--name <APP_NAME> --storage-account  <STORAGE_NAME>
```

上述命令也會在相同的資源群組中佈建相關聯的 Azure Application Insights 執行個體。 您可以使用此執行個體來監視函式應用程式及檢視記錄。

您現在已準備好將本機 Functions 專案發佈至 Azure 中的函式應用程式。

## <a name="deploy-the-function-app-project-to-azure"></a>將函式應用程式專案部署至 Azure

在 Azure 中建立函式應用程式之後，您可以使用 [func azure functionapp publish](functions-run-local.md#project-file-deployment) Core Tools 命令將專案程式碼部署至 Azure。 在此範例中，使用您的應用程式名稱取代 `<APP_NAME>`。

```console
func azure functionapp publish <APP_NAME> --build remote
```

`--build remote` 選項會透過部署套件中的檔案，從遠端在 Azure 中建立 Python 專案。 

您會看到類似於下列訊息的輸出。 輸出會在此截斷，以便更清楚地閱讀：

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

您可以複製您的 `HttpTrigger`的 `Invoke url` 值，並使用它在 Azure 中驗證您的函式。 URL 包含 `code` 查詢字串值，這是您的函式金鑰，可讓其他人難以在 Azure 中呼叫您的 HTTP 觸發程式端點。

[!INCLUDE [functions-test-function-code](../../includes/functions-test-function-code.md)]

> [!NOTE]
> 若要針對已發佈的 Python 應用程式檢視近乎即時的記錄，請使用 [Application Insights 即時計量資料流](functions-monitoring.md#streaming-logs)。

## <a name="next-steps"></a>後續步驟

您已使用 HTTP 觸發的函式建立 Python 函式專案，並在本機電腦上執行，然後部署到 Azure。 現在，請藉由下列作業擴充您的函式...

> [!div class="nextstepaction"]
> [新增 Azure 儲存體佇列輸出繫結](functions-add-output-binding-storage-queue-python.md)
