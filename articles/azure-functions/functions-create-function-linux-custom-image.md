---
title: 在 Linux 上使用自訂映像建立 Azure Functions
description: 了解如何建立在自訂 Linux 映像上執行的 Azure Functions。
services: functions
keywords: ''
author: ggailey777
ms.author: glenga
ms.date: 06/25/2019
ms.topic: tutorial
ms.service: azure-functions
ms.custom: mvc
ms.devlang: azure-cli
manager: jeconnoc
ms.openlocfilehash: 66effb095edf7927a38fca53b2ff317e9bad468e
ms.sourcegitcommit: 08d3a5827065d04a2dc62371e605d4d89cf6564f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/29/2019
ms.locfileid: "68619620"
---
# <a name="create-a-function-on-linux-using-a-custom-image"></a>在 Linux 上使用自訂映像建立函式

Azure Functions 可讓您在 Linux 的自訂容器中裝載函式。 您也可以[在預設的 Azure App Service 容器上裝載](functions-create-first-azure-function-azure-cli-linux.md)。 此功能需要 [Functions 2.x 執行階段](functions-versions.md)。

在本教學課程中，您將了解如何以自訂 Docker 映像的形式將函式部署到 Azure。 當您需要自訂內建容器映像時，此模式相當有用。 當您的函式需要特定的語言版本，或需要內建映像未提供的特定相依性或設定時，您可能會想使用自訂映像。 在 [Azure Functions 基底映像存放庫](https://hub.docker.com/_/microsoft-azure-functions-base)中可找到針對 Azure Functions 支援的基底映像。 [Python 支援](functions-reference-python.md)目前處於預覽階段。

本教學課程會引導您使用 Azure Functions Core Tools 在自訂 Linux 映像中建立建立函式。 您會將此映像發佈到 Azure 中使用 Azure CLI 建立的函式應用程式。 稍後，您會更新此函式以連線到 Azure 佇列儲存體。 您也會啟用。  

在本教學課程中，您了解如何：

> [!div class="checklist"]
> * 使用 Core Tools 建立函式應用程式和 Dockerfile。
> * 使用 Docker 建置自訂映像。
> * 將自訂映像發佈到容器登錄中。
> * 建立 Azure 儲存體帳戶。
> * 建立進階主控方案。
> * 從 Docker Hub 部署函式應用程式。
> * 將應用程式設定加入函式應用程式。
> * 啟用持續部署。
> * 新增 Application Insights 監視功能。

下列步驟適用於 Mac、Windows 或 Linux 電腦。 

## <a name="prerequisites"></a>必要條件

在執行此範例之前，您必須具備下列項目︰

* 安裝 [Azure Core Tools 2.x 版](functions-run-local.md#v2)。

* 安裝 [Azure CLI]( /cli/azure/install-azure-cli)。 此文章需要 Azure CLI 2.0 版或更新版本。 執行 `az --version` 以尋找您擁有的版本。  
您也可以使用 [Azure Cloud Shell](https://shell.azure.com/bash)。

* 有效的 Azure 訂用帳戶。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-the-local-function-app-project"></a>建立本機函式應用程式專案

從命令列執行下列命令，以在目前本機目錄的 `MyFunctionProj` 資料夾中建立函式應用程式專案。

```bash
func init MyFunctionProj --docker
```

當您加入 `--docker` 選項時，系統就會為專案產生 dockerfile。 此檔案會用來建立自訂容器以執行專案。 所用基礎映像取決於選擇的背景工作角色執行階段語言。  

當出現提示時，請從下列語言中選擇背景工作角色執行階段：

* `dotnet`：建立 .NET Core 類別庫專案 (.csproj)。
* `node`：建立 JavaScript 專案。
* `python`：建立 Python 專案。

[!INCLUDE [functions-python-preview-note](../../includes/functions-python-preview-note.md)]

當命令執行時，您會看到如下輸出：

```output
Writing .gitignore
Writing host.json
Writing local.settings.json
Writing Dockerfile
```

使用下列命令來瀏覽至新的 `MyFunctionProj` 專案資料夾。

```bash
cd MyFunctionProj
```

[!INCLUDE [functions-create-function-core-tools](../../includes/functions-create-function-core-tools.md)]

[!INCLUDE [functions-run-function-test-local](../../includes/functions-run-function-test-local.md)]

## <a name="build-the-image-from-the-docker-file"></a>從 Docker 檔案建立映像

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

當命令執行時，您會看到如下輸出的內容，這是本案例針對 JavaScript 背景工作角色執行階段執行的結果：

```bash
Sending build context to Docker daemon  17.41kB
Step 1/3 : FROM mcr.microsoft.com/azure-functions/node:2.0
2.0: Pulling from azure-functions/node
802b00ed6f79: Pull complete
44580ea7a636: Pull complete
73eebe8d57f9: Pull complete
3d82a67477c2: Pull complete
8bd51cd50290: Pull complete
7bd755353966: Pull complete
Digest: sha256:480e969821e9befe7c61dda353f63298f2c4b109e13032df5518e92540ea1d08
Status: Downloaded newer image for mcr.microsoft.com/azure-functions/node:2.0
 ---> 7c71671b838f
Step 2/3 : ENV AzureWebJobsScriptRoot=/home/site/wwwroot
 ---> Running in ed1e5809f0b7
Removing intermediate container ed1e5809f0b7
 ---> 39d9c341368a
Step 3/3 : COPY . /home/site/wwwroot
 ---> 5e196215935a
Successfully built 5e196215935a
Successfully tagged <docker-id>/mydockerimage:v1.0.0
```

### <a name="test-the-image-locally"></a>在本機測試映像
要確認建立的映像是否正常運作的方式，是在本機容器中執行 Docker 映像。 發出 [docker run](https://docs.docker.com/engine/reference/commandline/run/) 命令，並將映像的名稱和標記傳遞給它。 請務必使用 `-p` 引數來指定連接埠。

```bash
docker run -p 8080:80 -it <docker-ID>/mydockerimage:v1.0.0
```

在本機 Docker 容器中執行自訂映像，瀏覽至 <http://localhost:8080> 以確認函式應用程式和容器是否正常運作。

![在本機測試函式應用程式。](./media/functions-create-function-linux-custom-image/run-image-local-success.png)

您可以選擇性地再次測試您的函式，這次請在本機容器中使用下列 URL:

`http://localhost:8080/api/myhttptrigger?name=<yourname>`

在容器中驗證過函式應用程式容器之後，停止執行。 現在，您可以將自訂映像推送到 Docker Hub 帳戶。

## <a name="push-the-custom-image-to-docker-hub"></a>將自訂映像推送至 Docker Hub

登錄是裝載映像並提供服務映像和容器服務的應用程式。 您必須將映像推送到登錄，才能共用您的映像。 Docker Hub 是 Docker 映像的登錄，可讓您裝載自己的公用或私人存放庫。

推送映像之前，必須先使用 [docker login](https://docs.docker.com/engine/reference/commandline/login/) 命令登入 Docker Hub。 在命令提示字元中，以您的帳戶名稱取代 `<docker-id>`，並於主控台輸入您的密碼。 如需其他 Docker Hub 密碼選項，請參閱 [docker login 命令文件](https://docs.docker.com/engine/reference/commandline/login/) \(英文\)。

```bash
docker login --username <docker-id>
```

「登入成功」訊息會確認您已登入。 登入之後，使用 [docker push](https://docs.docker.com/engine/reference/commandline/push/) 命令將映像推送到 Docker Hub。

```bash
docker push <docker-id>/mydockerimage:v1.0.0
```

檢查命令的輸出來確認已成功推送。

```bash
The push refers to a repository [docker.io/<docker-id>/mydockerimage:v1.0.0]
24d81eb139bf: Pushed
fd9e998161c9: Mounted from <docker-id>/mydockerimage
e7796c35add2: Mounted from <docker-id>/mydockerimage
ae9a05b85848: Mounted from <docker-id>/mydockerimage
45c86e20670d: Mounted from <docker-id>/mydockerimage
v1.0.0: digest: sha256:be080d80770df71234eb893fbe4d... size: 1796
```

現在，您可以使用此映像作為 Azure 中的新函式應用程式的部署來源。

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-premium-plan"></a>建立進階方案

針對[專用 (App Service) 方案](functions-scale.md#app-service-plan)和[進階方案](functions-premium-plan.md#features)上支援的自訂 Functions 容器進行 Linux 主控。 本教學課程使用可視需要進行調整的進階方案。 若要深入了解裝載，請參閱 [Azure Functions 裝載方案比較](functions-scale.md)。

下列範例會在 [彈性進階 1]  定價層 (`--sku EP1`)、在美國西部區域 (`-location WestUS`)，以及在 Linux 容器 (`--is-linux`) 中建立名為 `myPremiumPlan` 的進階方案。

```azurecli-interactive
az functionapp plan create --resource-group myResourceGroup --name myPremiumPlan \
--location WestUS --number-of-workers 1 --sku EP1 --is-linux
```

## <a name="create-and-deploy-the-custom-image"></a>建立和部署自訂映像

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

您現在可以在 Azure 中測試在 Linux 上執行的函式。

[!INCLUDE [functions-test-function-code](../../includes/functions-test-function-code.md)]

## <a name="enable-continuous-deployment"></a>啟用持續部署

使用容器的優點之一，就是能夠在登錄中更新容器時自動部署更新。 使用 [az functionapp deployment container config](/cli/azure/functionapp/deployment/container#az-functionapp-deployment-container-config) 命令啟用持續部署。

```azurecli-interactive
az functionapp deployment container config --enable-cd \
--query CI_CD_URL --output tsv \
--name <app_name> --resource-group myResourceGroup
```

啟用持續部署之後，此命令會傳回部署 Webhook URL。 您也可以使用 [az functionapp deployment container show-cd-url](/cli/azure/functionapp/deployment/container#az-functionapp-deployment-container-show-cd-url) 命令來傳回此 URL。 

複製部署 URL 並瀏覽至您的 DockerHub 存放庫，選擇 [Webhook]  索引標籤，輸入 Webhook 的 [Webhook 名稱]  ，在 [Webhook URL]  中貼上您的 URL，然後選擇加號 ( **+** )。

![在 DockerHub 存放庫中新增 Webhook](media/functions-create-function-linux-custom-image/dockerhub-set-continuous-webhook.png)  

設定 Webhook 之後，任何對 DockerHub 中連結映像的更新都會導致函式應用程式下載並安裝最新的映像。

## <a name="enable-application-insights"></a>啟用 Application Insights

若要監視您的函式的執行情況，建議您整合您的函式應用程式與 Azure Application Insights。 當您在 Azure 入口網站中建立函式應用程式時，系統會依預設為您完成這項整合。 不過，當您使用 Azure CLI 建立函式應用程式時，則不會在 Azure 中完成您的函式應用程式整合。

若要為您的函式應用程式啟用 Application Insights：

[!INCLUDE [functions-connect-new-app-insights.md](../../includes/functions-connect-new-app-insights.md)]

若要深入了解，請參閱[監視 Azure Functions](functions-monitoring.md)。

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何：

> [!div class="checklist"]
> * 使用 Core Tools 建立函式應用程式和 Dockerfile。
> * 使用 Docker 建置自訂映像。
> * 將自訂映像發佈到容器登錄中。
> * 建立 Azure 儲存體帳戶。
> * 建立 Linux 進階方案。
> * 從 Docker Hub 部署函式應用程式。
> * 將應用程式設定加入函式應用程式。
> * 啟用持續部署。
> * 新增 Application Insights 監視功能。

> [!div class="nextstepaction"] 
> [深入了解將函式部署至 Azure 的選項](functions-deployment-technologies.md)
