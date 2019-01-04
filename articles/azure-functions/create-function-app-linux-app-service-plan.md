---
title: 在 Azure App Service 方案中建立 Linux 上的函式應用程式
description: 了解如何使用 Azure CLI，在 App Service 方案中建立執行於 Linux 上的函式應用程式。
services: functions
keywords: ''
author: ggailey777
ms.author: glenga
ms.date: 11/28/2018
ms.topic: conceptual
ms.service: azure-functions
ms.custom: mvc
ms.devlang: azure-cli
manager: jeconnoc
ms.openlocfilehash: d9800ff3fc82636c5cae12167738667ec84326ee
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/04/2018
ms.locfileid: "52854592"
---
# <a name="create-a-function-app-on-linux-in-an-azure-app-service-plan-preview"></a>在 Azure App Service 方案中建立 Linux 上的函式應用程式 (預覽)

Azure Functions 可讓您在 Linux 上預設的 Azure App Service 容器中裝載函式。 本文會逐步引導您了解如何使用 Azure CLI，在執行於 [App Service 方案](functions-scale.md#app-service-plan)的 Azure 中建立 Linux 裝載的函式應用程式。 您也可以[自備自訂容器](functions-create-function-linux-custom-image.md)。 Linux 裝載目前為預覽狀態。

在 App Service 方案中，您必須負責調整您的函式應用程式。 若要利用 Azure Functions 的無伺服器功能，您也可以將函式裝載在[取用方案](functions-scale.md#consumption-plan)中的 Linux 上。

您可以使用 Mac、Windows 或 Linux 電腦，依照下面步驟操作。

## <a name="prerequisites"></a>必要條件

若要完成本快速入門，您需要：

+ 有效的 Azure 訂用帳戶。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果您選擇在本機安裝和使用 CLI，本主題需要 Azure CLI 2.0.21 版或更新版本。 執行 `az --version` 以尋找您擁有的版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI 2.0]( /cli/azure/install-azure-cli)。 

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-linux-app-service-plan"></a>建立 Linux App Service 方案

[!INCLUDE [app-service-plan-no-h](../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

## <a name="create-a-function-app-on-linux"></a>在 Linux 上建立函式應用程式

您必須擁有函式應用程式以便在 Linux 上主控函式的執行。 函式應用程式會提供環境來讓您的函式程式碼進行執行。 它可讓您將多個函式群組為邏輯單位，以方便您管理、部署和共用資源。 使用 [az functionapp create](/cli/azure/functionapp#az-functionapp-create) 命令與 Linux App Service 方案來建立函式應用程式。

在下列命令中，使用唯一函式應用程式名稱來替代您看見 `<app_name>` 預留位置的地方，並使用儲存體帳戶名稱來替代 `<storage_name>`。 `<app_name>` 會作為函式應用程式的預設 DNS 網域，所以此名稱在 Azure 的所有應用程式中都必須是唯一的名稱。 您也應該從 `dotnet` (C#)、`node` (JavaScript) 或 `python`，設定函式應用程式的 `<language>` 執行階段。

```azurecli-interactive
az functionapp create --resource-group myResourceGroup --plan myAppServicePlan \
--name <app_name> --storage-account  <storage_name> --runtime <language>
```

建立及部署函式應用程式後，Azure CLI 會顯示類似下列範例的資訊：

```json
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 1536,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "quickstart.azurewebsites.net",
  "enabled": true,
  "enabledHostNames": [
    "quickstart.azurewebsites.net",
    "quickstart.scm.azurewebsites.net"
  ],
   ....
    // Remaining output has been truncated for readability.
}
```

因為 `myAppServicePlan` 是 Linux 方案，內建 Docker 映像會用來建立容器 (在 Linux 上執行函式應用程式)。

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources-simple.md)]

## <a name="next-steps"></a>後續步驟

本文說明如何在 Azure 中建立 Linux 裝載的函式應用程式。 您現在可以[部署函式專案](https://docs.microsoft.com/cli/azure/functionapp/deployment/source?view=azure-cli-latest)到此函式應用程式。 您可以使用 Azure Functions Core Tools 在本機電腦上[建立 Functions 專案](functions-run-local.md#create-a-local-functions-project)，並將它部署到新的 Linux 函式應用程式。  

> [!div class="nextstepaction"] 
> [撰寫 Azure Functions 並在本機進行測試](functions-run-local.md)
