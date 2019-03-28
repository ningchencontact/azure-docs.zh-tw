---
title: 在 Azure 中建立您的第一個 Python 函式
description: 了解如何使用 Azure Functions Core Tools 和 Azure CLI 在 Azure 中建立您的第一個 Python 函式。
services: functions
keywords: ''
author: ggailey777
ms.author: glenga
ms.date: 08/29/2018
ms.topic: quickstart
ms.service: azure-functions
ms.custom: mvc
ms.devlang: python
manager: jeconnoc
ms.openlocfilehash: af684a4fcc3a70326c1a57cb10a39204b4fd12dc
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2019
ms.locfileid: "58438745"
---
# <a name="create-your-first-python-function-in-azure-preview"></a>在 Azure 中建立您的第一個 Python 函式 (預覽)

[!INCLUDE [functions-python-preview-note](../../includes/functions-python-preview-note.md)]

本快速入門文章會逐步解說如何使用 Azure CLI 建立第一個在 Linux 上執行的[無伺服器](https://azure.com/serverless) Python 函式應用程式。 您可以使用 [Azure Functions Core Tools](functions-run-local.md) 在本機建立函式程式碼，然後將其部署至 Azure。 若要深入了解在 Linux 上執行函式應用程式的預覽版本注意事項，請參閱[這篇在 Linux 上的 Functions 文章](https://aka.ms/funclinux)。

下列步驟適用於 Mac、Windows 或 Linux 電腦。

## <a name="prerequisites"></a>必要條件

若要在本機建置和測試，您將需要：

+ 安裝 [Python 3.6](https://www.python.org/downloads/)。

+ 安裝 [Azure Functions Core Tools](functions-run-local.md#v2) 2.2.70 版或更新版本 (需要 .NET Core 2.x SDK)。

若要在 Azure 中發佈和執行：

+ 安裝 [Azure CLI]( /cli/azure/install-azure-cli) 2.x 版或更新版本。

+ 您需要作用中的 Azure 訂用帳戶。
  [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-and-activate-a-virtual-environment"></a>建立並啟用虛擬環境

若要建立 Functions 專案，就必須在 Python 3.6 虛擬環境中工作。 執行下列命令來建立並啟用名為 `.env` 的虛擬環境。

```bash
# In Bash
python3.6 -m venv .env
source .env/bin/activate

# In PowerShell
py -3.6 -m venv .env
.env\scripts\activate
```

## <a name="create-a-local-functions-project"></a>建立本機的 Functions 專案

您現在可以建立本機 Functions 專案。 此目錄相當於 Azure 中的函式應用程式。 它可以包含多個函式，並共用相同的本機和裝載設定。

在終端機視窗或命令提示字元中，執行下列命令：

```bash
func init MyFunctionProj
```

挑選 [Python] 作為所需的執行階段。

```output
Select a worker runtime:
1. dotnet
2. node
3. python
```

您會看到類似下列輸出的畫面。

```output
Installing wheel package
Installing azure-functions package
Installing azure-functions-worker package
Running pip freeze
Writing .gitignore
Writing host.json
Writing local.settings.json
Writing /MyFunctionProj/.vscode/extensions.json
```

隨即會建立名為 MyFunctionProj 的新資料夾。 若要繼續，請將目錄變更到這個資料夾。

```bash
cd MyFunctionProj
```

## <a name="create-a-function"></a>建立函式

若要建立函式，請執行下列命令：

```bash
func new
```

選擇 `HTTP Trigger` 作為範本，並提供 `HttpTrigger` 的**函式名稱**。

```output
Select a template:
1. Blob trigger
2. Cosmos DB trigger
3. Event Grid trigger
4. Event Hub trigger
5. HTTP trigger
6. Queue trigger
7. Service Bus Queue trigger
8. Service Bus Topic trigger
9. Timer trigger

Choose option: 5
Function name: HttpTrigger
```

您會看到類似下列輸出的畫面。

```output
Writing /MyFunctionProj/HttpTrigger/sample.dat
Writing /MyFunctionProj/HttpTrigger/__init__.py
Writing /MyFunctionProj/HttpTrigger/function.json
The function "HttpTrigger" was created successfully from the "HTTP trigger" template.
```

隨即會建立名為 HttpTrigger 的子資料夾。 這包含 `__init__.py` (這是主要的指令碼檔案) 和 `function.json` 檔案 (描述函式所使用的觸發程序和繫結)。 若要深入了解程式設計模型，請參閱 [Azure Functions Python 開發人員指南](functions-reference-python.md)。

## <a name="run-the-function-locally"></a>在本機執行函式

使用下列命令在本機執行 Functions 主機。

```bash
func host start
```

Functions 主機啟動時會輸出 HTTP 觸發函式的 URL。 (請注意，為了方便閱讀，完整的輸出已遭截斷)。

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
Now listening on: http://0.0.0.0:7071
Application started. Press Ctrl+C to shut down.
...

Http Functions:

        HttpTrigger: http://localhost:7071/api/HttpTrigger
```

從輸出複製函式的 URL，並將它貼到瀏覽器的網址列。 將查詢字串 `?name=<yourname>` 附加至此 URL 並執行要求。

    http://localhost:7071/api/HttpTrigger?name=<yourname>

下列螢幕擷取畫面會顯示函式從瀏覽器觸發時所產生的回應：

![test](./media/functions-create-first-function-python/function-test-local-browser.png)

您現在已準備好建立函式應用程式和其他必要資源，以便發佈至 Azure。

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-linux-function-app-in-azure"></a>在 Azure 中建立 Linux 函式應用程式

函式應用程式會提供環境來執行函式程式碼。 它可讓您將多個函式群組為邏輯單位，以方便您管理、部署和共用資源。 使用 [az functionapp create](/cli/azure/functionapp) 命令，建立**在 Linux 上執行的 Python 函式應用程式**。

執行下列命令，並請使用唯一函式應用程式名稱來替代 `<app_name>` 預留位置，並使用儲存體帳戶名稱來替代 `<storage_name>`。 `<app_name>` 也是函式應用程式的預設 DNS 網域。 此名稱在 Azure 中的所有應用程式之間必須是唯一的。

```azurecli-interactive
az functionapp create --resource-group myResourceGroup --os-type Linux \
--consumption-plan-location westeurope  --runtime python \
--name <app_name> --storage-account  <storage_name>
```

> [!NOTE]
> 如果您有現有的資源群組名為 `myResourceGroup`，且該群組中含有任何非 Linux App Service 應用程式，您就必須使用不同的資源群組。 您無法將 Windows 和 Linux 應用程式裝載在相同的資源群組中。  

建立函式應用程式之後，您會看到下列訊息：

```output
Your serverless Linux function app 'myfunctionapp' has been successfully created.
To active this function app, publish your app content using Azure Functions Core Tools or the Azure portal.
```

您現在已準備好將本機 Functions 專案發佈至 Azure 中的函式應用程式。

## <a name="deploy-the-function-app-project-to-azure"></a>將函式應用程式專案部署至 Azure

使用 Azure Functions Core Tools 執行下列命令。 使用上一個步驟的應用程式名稱取代 `<app_name>`。

```bash
func azure functionapp publish <app_name>
```

您會看到類似下列的輸出，該輸出已截短，以提高可讀性。

```output
Getting site publishing info...

...

Preparing archive...
Uploading content...
Upload completed successfully.
Deployment completed successfully.
Syncing triggers...
```

[!INCLUDE [functions-test-function-code](../../includes/functions-test-function-code.md)]

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

## <a name="next-steps"></a>後續步驟

深入了解如何使用 Python 開發 Azure Functions。

> [!div class="nextstepaction"]
> [Azure Functions Python 開發人員指南](functions-reference-python.md)
> [Azure Functions 觸發程序和繫結](functions-triggers-bindings.md)
