---
title: 在 Azure CLI 建立您的第一個函式
description: 了解如何使用 Azure CLI 與 Azure Functions Core Tools 建立您的第一個適用於無伺服器執行的 Azure 函式。
author: ggailey777
ms.author: glenga
ms.assetid: 674a01a7-fd34-4775-8b69-893182742ae0
ms.date: 11/13/2018
ms.topic: quickstart
ms.service: azure-functions
ms.custom: mvc
manager: gwallace
ms.openlocfilehash: 1d0c6edf250e49fe966388253617181ed2b3dff1
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/15/2019
ms.locfileid: "72329646"
---
# <a name="quickstart-create-your-first-function-from-the-command-line-using-azure-cli"></a>快速入門：使用 Azure CLI 從命令列建立您的第一個函式

此快速入門主題會逐步引導您從命令列或終端機建立您的第一個函式。 您會使用 Azure CLI 來建立函數應用程式，此應用程式是主控函式的[無伺服器](https://azure.microsoft.com/solutions/serverless/)基礎結構。 函式程式碼專案是使用 [Azure Functions Core Tools](functions-run-local.md) (也用來將函式應用程式專案部署到 Azure) 從範本產生的。

您可以使用 Mac、Windows 或 Linux 電腦，依照下面步驟操作。

## <a name="prerequisites"></a>必要條件

在執行此範例之前，您必須具備下列項目︰

+ 安裝 [Azure Functions Core Tools](./functions-run-local.md#v2) 2.6.666 版或更新版本。

+ 安裝 [Azure CLI](/cli/azure/install-azure-cli)。 此文章需要 Azure CLI 2.0 版或更新版本。 執行 `az --version` 以尋找您擁有的版本。 您也可以使用 [Azure Cloud Shell](https://shell.azure.com/bash)。

+ 有效的 Azure 訂用帳戶。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [functions-create-function-app-cli](../../includes/functions-create-function-app-cli.md)]

## <a name="enable-extension-bundles"></a>啟用延伸模組搭售方案

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

[!INCLUDE [functions-create-function-core-tools](../../includes/functions-create-function-core-tools.md)]

[!INCLUDE [functions-run-function-test-local](../../includes/functions-run-function-test-local.md)]

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-function-app"></a>建立函數應用程式

您必須擁有函式應用程式以便主控函式的執行。 函式應用程式會提供環境來讓您的函式程式碼進行無伺服器執行。 它可讓您將多個函式群組為邏輯單位，以方便您管理、部署和共用資源。 使用 [az functionapp create](/cli/azure/functionapp#az-functionapp-create) 命令來建立函式應用程式。 

在下列命令中，使用唯一函式應用程式名稱來替代您看見 `<APP_NAME>` 預留位置的地方，並使用儲存體帳戶名稱來替代 `<STORAGE_NAME>`。 `<APP_NAME>` 會作為函式應用程式的預設 DNS 網域，所以此名稱在 Azure 的所有應用程式中都必須是唯一的名稱。 您也應該從 `dotnet` (C#) 或 `node` (JavaScript)，設定函式應用程式的 `<language>` 執行階段。

```azurecli-interactive
az functionapp create --resource-group myResourceGroup --consumption-plan-location westeurope \
--name <APP_NAME> --storage-account  <STORAGE_NAME> --runtime <language>
```

設定 _consumption-plan-location_ 參數，即表示函數應用程式是裝載於取用主控方案。 在這個無伺服器方案中，會根據您的函式，視需要動態新增資源，且只有在函式執行時才需要付費。 如需詳細資訊，請參閱[選擇正確的主控方案](functions-scale.md)。

建立函式應用程式後，Azure CLI 會顯示類似下列範例的資訊：

```json
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
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

[!INCLUDE [functions-publish-project](../../includes/functions-publish-project.md)]

[!INCLUDE [functions-test-function-code](../../includes/functions-test-function-code.md)]

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

[!INCLUDE [functions-quickstart-next-steps-cli](../../includes/functions-quickstart-next-steps-cli.md)]

