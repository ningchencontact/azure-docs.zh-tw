---
title: 在 Linux 上使用自訂映像建立 Azure Functions
description: 了解如何建立在自訂 Linux 映像上執行的 Azure Functions。
author: ggailey777
ms.author: glenga
ms.date: 09/27/2019
ms.topic: tutorial
ms.service: azure-functions
ms.custom: mvc
manager: gwallace
ms.openlocfilehash: d4a72edbe762afd2a94962c1440357ce3ad46862
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/15/2019
ms.locfileid: "72329539"
---
# <a name="create-a-function-on-linux-using-a-custom-image"></a>在 Linux 上使用自訂映像建立函式

Azure Functions 可讓您在 Linux 的自訂容器中裝載函式。 您也可以[在預設的 Azure App Service 容器上裝載](functions-create-first-azure-function-azure-cli-linux.md)。 此功能需要 [Functions 2.x 執行階段](functions-versions.md)。

在本教學課程中，您將了解如何以自訂 Docker 映像的形式將函式部署到 Azure。 當您需要自訂內建容器映像時，此模式相當有用。 當您的函式需要特定的語言版本，或需要內建映像未提供的特定相依性或設定時，您可能會想使用自訂映像。 在 [Azure Functions 基底映像存放庫](https://hub.docker.com/_/microsoft-azure-functions-base)中可找到針對 Azure Functions 支援的基底映像。 

本教學課程會引導您使用 Azure Functions Core Tools 在自訂 Linux 映像中建立建立函式。 您會將此映像發佈到 Azure 中使用 Azure CLI 建立的函式應用程式。 稍後，您會更新此函式以連線到 Azure 佇列儲存體。 您也會啟用。  

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 使用 Core Tools 建立函式應用程式和 Dockerfile。
> * 使用 Docker 建置自訂映像。
> * 將自訂映像發佈到容器登錄中。
> * 建立 Azure 儲存體帳戶。
> * 建立進階主控方案。
> * 從 Docker Hub 部署函式應用程式。
> * 將應用程式設定加入函式應用程式。
> * 啟用持續部署。
> * 啟用容器的 SSH 連線。
> * 新增佇列儲存體輸出繫結。 
> * 新增 Application Insights 監視功能。

下列步驟適用於 Mac、Windows 或 Linux 電腦。 

## <a name="prerequisites"></a>必要條件

在執行此範例之前，您必須具備下列項目︰

* 安裝 [Azure Core Tools 2.x 版](functions-run-local.md#v2)。

* 安裝 [Azure CLI]( /cli/azure/install-azure-cli)。 此文章需要 Azure CLI 2.0 版或更新版本。 執行 `az --version` 以尋找您擁有的版本。  
您也可以使用 [Azure Cloud Shell](https://shell.azure.com/bash)。

* 有效的 Azure 訂用帳戶。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [functions-cloud-shell-note](../../includes/functions-cloud-shell-note.md)]

## <a name="create-the-local-project"></a>建立本機專案

從命令列執行下列命令，以在目前本機目錄的 `MyFunctionProj` 資料夾中建立函式應用程式專案。 針對 Python 專案，您[必須在虛擬環境中執行](functions-create-first-function-python.md#create-and-activate-a-virtual-environment-optional)。

```bash
func init MyFunctionProj --docker
```

當您加入 `--docker` 選項時，系統就會為專案產生 dockerfile。 此檔案會用來建立自訂容器以執行專案。 所用基礎映像取決於選擇的背景工作角色執行階段語言。  

當出現提示時，請從下列語言中選擇背景工作角色執行階段：

* `dotnet`：建立 .NET Core 類別庫專案 (.csproj)。
* `node`：建立 JavaScript 專案。
* `python`：建立 Python 專案。  

使用下列命令來瀏覽至新的 `MyFunctionProj` 專案資料夾。

```bash
cd MyFunctionProj
```

[!INCLUDE [functions-create-function-core-tools](../../includes/functions-create-function-core-tools.md)]

[!INCLUDE [functions-run-function-test-local](../../includes/functions-run-function-test-local.md)]

## <a name="build-from-the-docker-file"></a>從 Docker 檔案建置

請看一下專案根資料夾中的 Dockerfile  。 此檔案描述在 Linux 上執行函式應用程式所需的環境。 下列 Dockerfile 範例會建立容器，用來在 JavaScript (Node.js) 背景工作角色執行階段上執行函式應用程式： 

```Dockerfile
FROM mcr.microsoft.com/azure-functions/node:2.0

ENV AzureWebJobsScriptRoot=/home/site/wwwroot
COPY . /home/site/wwwroot
```

> [!NOTE]
> 在 [Azure Functions 基底映像頁面](https://hub.docker.com/_/microsoft-azure-functions-base)中可找到針對 Azure Functions 支援的完整基底映像清單。

### <a name="run-the-build-command"></a>執行 `build` 命令

在根資料夾中，執行 [docker build](https://docs.docker.com/engine/reference/commandline/build/) 命令，然後提供名稱、`mydockerimage` 和標記 (`v1.0.0`)。 將 `<docker-id>` 取代為 Docker Hub 帳戶識別碼。 此命令會建置容器的 Docker 映像。

```bash
docker build --tag <docker-id>/mydockerimage:v1.0.0 .
```

當命令完成時，您就能在本機執行新容器。

### <a name="run-the-image-locally"></a>在本機執行映像
要確認建立的映像是否正常運作的方式，是在本機容器中執行 Docker 映像。 發出 [docker run](https://docs.docker.com/engine/reference/commandline/run/) 命令，並將映像的名稱和標記傳遞給它。 請務必使用 `-p` 引數來指定連接埠。

```bash
docker run -p 8080:80 -it <docker-ID>/mydockerimage:v1.0.0
```

在本機 Docker 容器中執行自訂映像，瀏覽至 <http://localhost:8080> 以確認函式應用程式和容器是否正常運作。

![在本機執行函式應用程式。](./media/functions-create-function-linux-custom-image/run-image-local-success.png)

> [!NOTE]
> 此時，當您嘗試呼叫特定的 HTTP 函式時，您會收到 HTTP 401 錯誤回應。 這是因為您的函式會在本機容器中執行，如同在 Azure 中一樣，這表示需要函式金鑰。 由於容器尚未發佈至函式應用程式，因此沒有可用的函式金鑰。 您稍後會看到當您使用核心工具來發佈容器時，會向您顯示函式金鑰。 如果您想要測試在本機容器中執行的函式，您可以將[授權金鑰](functions-bindings-http-webhook.md#authorization-keys)變更為 `anonymous`。 

在容器中驗證過函式應用程式容器之後，停止執行。 現在，您可以將自訂映像推送到 Docker Hub 帳戶。

## <a name="push-to-docker-hub"></a>推送至 Docker Hub

登錄是裝載映像並提供服務映像和容器服務的應用程式。 您必須將映像推送到登錄，才能共用您的映像。 Docker Hub 是 Docker 映像的登錄，可讓您裝載自己的公用或私人存放庫。

推送映像之前，必須先使用 [docker login](https://docs.docker.com/engine/reference/commandline/login/) 命令登入 Docker Hub。 在命令提示字元中，以您的帳戶名稱取代 `<docker-id>`，並於主控台輸入您的密碼。 如需其他 Docker Hub 密碼選項，請參閱 [docker login 命令文件](https://docs.docker.com/engine/reference/commandline/login/) \(英文\)。

```bash
docker login --username <docker-id>
```

「登入成功」訊息會確認您已登入。 登入之後，使用 [docker push](https://docs.docker.com/engine/reference/commandline/push/) 命令將映像推送到 Docker Hub。

```bash
docker push <docker-id>/mydockerimage:v1.0.0
```

推送成功之後，您就可以使用此映像作為 Azure 中新函數應用程式的部署來源。

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-premium-plan"></a>建立進階方案

針對[專用 (App Service) 方案](functions-scale.md#app-service-plan)和[進階方案](functions-premium-plan.md#features)上支援的自訂 Functions 容器進行 Linux 主控。 本教學課程使用可視需要進行調整的進階方案。 若要深入了解裝載，請參閱 [Azure Functions 裝載方案比較](functions-scale.md)。

下列範例會在 [彈性進階 1]  定價層 (`--sku EP1`)、在美國西部區域 (`-location WestUS`)，以及在 Linux 容器 (`--is-linux`) 中建立名為 `myPremiumPlan` 的進階方案。

```azurecli-interactive
az functionapp plan create --resource-group myResourceGroup --name myPremiumPlan \
--location WestUS --number-of-workers 1 --sku EP1 --is-linux
```

## <a name="create-an-app-from-the-image"></a>從映像建立應用程式

函式應用程式可管理主控方案中函式的執行。 使用 [az functionapp create](/cli/azure/functionapp#az-functionapp-create) 命令從 Docker Hub 映像建立函式應用程式。

在下列命令中，使用唯一函式應用程式名稱來替代您看見 `<app_name>` 預留位置的地方，並使用儲存體帳戶名稱來替代 `<storage_name>`。 `<app_name>` 會作為函式應用程式的預設 DNS 網域，所以此名稱在 Azure 的所有應用程式中都必須是唯一的名稱。 如先前所述，`<docker-id>` 是您的 Docker 帳戶名稱。

```azurecli-interactive
az functionapp create --name <app_name> --storage-account  <storage_name>  --resource-group myResourceGroup \
--plan myPremiumPlan --deployment-container-image-name <docker-id>/mydockerimage:v1.0.0
```

_deployment-container-image-name_ 參數表示裝載於 Docker Hub 上用於建立函式應用程式的映像。 使用 [az functionapp config container show](/cli/azure/functionapp/config/container#az-functionapp-config-container-show) 命令來檢視部署所用映像的相關資訊。 使用 [az functionapp config container set](/cli/azure/functionapp/config/container#az-functionapp-config-container-set) 命令，從不同映像進行部署。

## <a name="configure-the-function-app"></a>設定函式應用程式

此函式需要連接字串以連接到預設儲存體帳戶。 當您將自訂映像發佈至私人容器帳戶時，應使用 [ENV 指令](https://docs.docker.com/engine/reference/builder/#env) \(英文\) 或類似指令將 Dockerfile 中的這些應用程式設定改設為環境變數。

在本例中，`<storage_name>` 是您建立的儲存體帳戶名稱。 使用 [az storage account show-connection-string](/cli/azure/storage/account) 命令取得連接字串。 使用 [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set) 命令，在函式應用程式中新增這些應用程式設定。

```azurecli-interactive
storageConnectionString=$(az storage account show-connection-string \
--resource-group myResourceGroup --name <storage_name> \
--query connectionString --output tsv)

az functionapp config appsettings set --name <app_name> \
--resource-group myResourceGroup \
--settings AzureWebJobsDashboard=$storageConnectionString \
AzureWebJobsStorage=$storageConnectionString
```

> [!NOTE]
> 如果您的容器屬私人性質，您也必須設定下列應用程式設定  
> - DOCKER_REGISTRY_SERVER_USERNAME  
> - DOCKER_REGISTRY_SERVER_PASSWORD  
>
> 您必須先停止函式應用程式，然後再將其啟動，好讓這些值可供挑選

## <a name="verify-your-functions"></a>驗證您的函式

<!-- we should replace this with a CLI or API-based approach, when we get something better than REST -->

呼叫端點時，您所建立的 HTTP 觸發函式需要[函式金鑰](functions-bindings-http-webhook.md#authorization-keys)。 目前，取得函式 URL 的最簡單方式 (包括金鑰) 是從 [Azure 入口網站]取得。 

> [!TIP]
> 您也可以使用[金鑰管理 API](https://github.com/Azure/azure-functions-host/wiki/Key-management-API) \(英文\) 來取得函式金鑰，這會要求您提供[持有人權杖以進行驗證](/cli/azure/account#az-account-get-access-token)。

在 [Azure 入口網站]中，於頁面頂端的 [搜尋]  方塊中輸入函數應用程式名稱，然後選取 [應用程式服務]  資源，以找出您的新函數應用程式。

選取 [MyHttpTrigger]  函式、選取 [</> 取得函數 URL]   > [預設 (函式金鑰)]   > [複製]  。

![從 Azure 入口網站複製函式 URL](./media/functions-create-function-linux-custom-image/functions-portal-get-url-key.png)

在此 URL 中，函式金鑰是 `code` 查詢參數。 

> [!NOTE]  
> 因為您的函數應用程式會部署為容器，所以，您無法在入口網站中對函式程式碼進行變更。 您必須改為更新本機容器中的專案，並將它重新發佈至 Azure。

將函式 URL 貼入瀏覽器的網址列中。 將查詢字串值 `&name=<yourname>` 新增至此 URL 的結尾，並按鍵盤上的 `Enter` 鍵執行要求。 您應該會看到函式傳回的回應顯示在瀏覽器中。

下列範例會顯示瀏覽器中的回應：

![瀏覽器中的函式回應。](./media/functions-create-function-linux-custom-image/function-app-browser-testing.png)

要求 URL 預設會包含所需金鑰，以便透過 HTTP 存取您的函式。 

## <a name="enable-continuous-deployment"></a>啟用持續部署

使用容器的優點之一是支援持續部署。 Functions 可讓您在登錄中更新容器時，自動部署更新。 使用 [az functionapp deployment container config](/cli/azure/functionapp/deployment/container#az-functionapp-deployment-container-config) 命令啟用持續部署。

```azurecli-interactive
az functionapp deployment container config --enable-cd \
--query CI_CD_URL --output tsv \
--name <app_name> --resource-group myResourceGroup
```

啟用持續部署之後，此命令會傳回部署 Webhook URL。 您也可以使用 [az functionapp deployment container show-cd-url](/cli/azure/functionapp/deployment/container#az-functionapp-deployment-container-show-cd-url) 命令來傳回此 URL。 

複製部署 URL 並瀏覽至您的 DockerHub 存放庫，選擇 [Webhook]  索引標籤，輸入 Webhook 的 [Webhook 名稱]  ，在 [Webhook URL]  中貼上您的 URL，然後選擇加號 ( **+** )。

![在 DockerHub 存放庫中新增 Webhook](./media/functions-create-function-linux-custom-image/dockerhub-set-continuous-webhook.png)  

設定 Webhook 之後，任何對 DockerHub 中連結映像的更新都會導致函式應用程式下載並安裝最新的映像。

## <a name="enable-ssh-connections"></a>啟用 SSH 連線

SSH 可讓容器和用戶端之間進行安全通訊。 啟用 SSH 之後，您就能使用 App Service 進階工具 (Kudu) 連線到您的容器。 為了讓您可以輕鬆地使用 SSH 連線到容器，Functions 會提供已經啟用 SSH 的基底映像。 

### <a name="change-the-base-image"></a>變更基底映像

在您的 dockerfile 中，將字串 `-appservice` 附加至 `FROM` 指令中的基底映像，針對 JavaScript 專案，其看起來如下。

```docker
FROM mcr.microsoft.com/azure-functions/node:2.0-appservice
```

這兩個基底映像中的差異可讓 SSH 連線到您的容器。 [這個應用程式服務教學課程](../app-service/containers/tutorial-custom-docker-image.md#enable-ssh-connections)會詳細說明這些差異。

### <a name="rebuild-and-redeploy-the-image"></a>重建和重新部署映像

在根資料夾中，再次執行 [docker build](https://docs.docker.com/engine/reference/commandline/build/) \(英文\) 命令，和之前一樣，以您的 Docker Hub 帳戶識別碼取代 `<docker-id>`。 

```bash
docker build --tag <docker-id>/mydockerimage:v1.0.0 .
```

將更新的映像推送回 Docker Hub。

```bash
docker push <docker-id>/mydockerimage:v1.0.0
```

更新的映像會重新部署至您的函數應用程式。

### <a name="connect-to-your-container-in-azure"></a>連線到您在 Azure 中的容器

在瀏覽器中，瀏覽至您函數應用程式容器的下列進階工具 (Kudu) `scm.` 端點，並以您的函數應用程式名稱取代 `<app_name>`。

```
https://<app_name>.scm.azurewebsites.net/
```

登入您的 Azure 帳戶，然後選取 [SSH]  索引標籤，以在容器中建立 SSH 連線。

建立連線之後，執行 `top` 命令來檢視目前執行中的程序。 

![在 SSH 工作階段中執行的 Linux top 命令。](media/functions-create-function-linux-custom-image/linux-custom-kudu-ssh-top.png)

## <a name="write-to-queue-storage"></a>寫入至佇列儲存體

Functions 可讓您直接將 Azure 服務和其他資源連線至函式，而不需自行撰寫整合程式碼。 這些*繫結*同時代表輸入和輸出，會宣告於函式定義內。 繫結中的資料會提供給函式作為參數。 「觸發程序」  是一種特殊的輸入繫結。 雖然函式只有一個觸發程序，但可以有多個輸入和輸出繫結。 若要深入了解，請參閱 [Azure Functions 觸發程序和繫結概念](functions-triggers-bindings.md)。

本節說明如何將您的函式與 Azure 儲存體佇列整合。 您新增至此函式的輸出繫結，會將資料從 HTTP 要求寫入至佇列中的訊息。

### <a name="download-the-function-app-settings"></a>下載函式應用程式設定

[!INCLUDE [functions-app-settings-download-local-cli](../../includes/functions-app-settings-download-local-cli.md)]

### <a name="enable-extension-bundles"></a>啟用延伸模組搭售方案

由於您是使用佇列儲存體輸出繫結，您必須先安裝儲存體繫結擴充才能執行專案。 


# <a name="javascript--pythontabnodejspython"></a>[JavaScript / Python](#tab/nodejs+python)

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

# <a name="ctabcsharp"></a>[C\#](#tab/csharp)

除了 HTTP 和計時器觸發程序之外，繫結皆會以擴充套件的形式實作。 在終端機視窗中執行下列 [dotnet add package](/dotnet/core/tools/dotnet-add-package) 命令來將儲存體套件新增至您的專案。

```bash
dotnet add package Microsoft.Azure.WebJobs.Extensions.Storage --version 3.0.4
```

> [!TIP]
> 使用 Visual Studio 時，您也可以使用 NuGet 套件管理員來新增此套件。

---

現在，您可以將儲存體輸出繫結新增至您的專案。

### <a name="add-an-output-binding"></a>新增輸出繫結

在函式中，每一種繫結都需要在 function.json 檔案中定義 `direction`、`type` 和唯一的 `name`。 定義這些屬性的方式會取決於您函式應用程式的語言。

# <a name="javascript--pythontabnodejspython"></a>[JavaScript / Python](#tab/nodejs+python)

[!INCLUDE [functions-add-output-binding-json](../../includes/functions-add-output-binding-json.md)]

# <a name="ctabcsharp"></a>[C\#](#tab/csharp)

[!INCLUDE [functions-add-storage-binding-csharp-library](../../includes/functions-add-storage-binding-csharp-library.md)]

---

### <a name="add-code-that-uses-the-output-binding"></a>新增會使用輸出繫結的程式碼

在定義繫結之後，您即可使用繫結的 `name` 來以函式簽章中屬性的形式存取它。 藉由使用輸出繫結，您無須使用 Azure 儲存體 SDK 程式碼來進行驗證、取得佇列參考或寫入資料。 Functions 執行階段和佇列輸出繫結會為您進行這些工作。

# <a name="javascripttabnodejs"></a>[JavaScript](#tab/nodejs)

[!INCLUDE [functions-add-output-binding-js](../../includes/functions-add-output-binding-js.md)]

# <a name="pythontabpython"></a>[Python](#tab/python)

[!INCLUDE [functions-add-output-binding-python](../../includes/functions-add-output-binding-python.md)]

# <a name="ctabcsharp"></a>[C\#](#tab/csharp)

[!INCLUDE [functions-add-storage-binding-csharp-library-code](../../includes/functions-add-storage-binding-csharp-library-code.md)]

---

### <a name="update-the-hosted-container"></a>更新裝載的容器

在根資料夾中，再次執行 [docker build](https://docs.docker.com/engine/reference/commandline/build/) \(英文\) 命令，這次會將標記中的版本更新為 `v1.0.2`。 和之前一樣，以您的 Docker Hub 帳戶識別碼取代 `<docker-id>`。 

```bash
docker build --tag <docker-id>/mydockerimage:v1.0.0 .
```

將更新的映像推送回存放庫。

```bash
docker push <docker-id>/mydockerimage:v1.0.0
```

### <a name="verify-the-updates-in-azure"></a>確認 Azure 中的更新

從瀏覽器中使用和之前一樣的 URL 來觸發您的函式。 您應該會看到相同的回應。 不過，這次會將您以 `name` 參數傳遞的字串寫入至 `outqueue` 儲存體佇列。

[!INCLUDE [functions-storage-account-set-cli](../../includes/functions-storage-account-set-cli.md)]

[!INCLUDE [functions-query-storage-cli](../../includes/functions-query-storage-cli.md)]

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

## <a name="next-steps"></a>後續步驟

既然您已成功將自訂容器部署至 Azure 中的函數應用程式，請考慮閱讀有關下列主題的詳細資訊：

+ [監視函式](functions-monitoring.md)
+ [調整和裝載選項](functions-scale.md)
+ [以 Kubernetes 為基礎的無伺服器裝載](functions-kubernetes-keda.md)

[Azure 入口網站]: https://portal.azure.com
