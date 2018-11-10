---
title: 在 Azure 中的 Linux 上建立您的第一個函式
description: 了解如何使用 Azure Functions Core Tools 和 Azure CLI 建立第一個在 Azure 中的 Linux 上裝載的函式。
services: functions
keywords: ''
author: ggailey777
ms.author: glenga
ms.date: 09/12/2018
ms.topic: quickstart
ms.service: azure-functions
ms.custom: mvc
ms.devlang: multiple
manager: jeconnoc
ms.openlocfilehash: 1045e0cc0d114bb8b35e6136a2054b3642eac7e8
ms.sourcegitcommit: 1d3353b95e0de04d4aec2d0d6f84ec45deaaf6ae
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2018
ms.locfileid: "50249860"
---
# <a name="create-your-first-function-hosted-on-linux-using-core-tools-and-the-azure-cli-preview"></a>使用 Core Tools 和 Azure CLI 建立第一個在 Linux 上裝載的函式 (預覽)

Azure Functions 可讓您在[無伺服器](https://azure.microsoft.com/overview/serverless-computing/) Linux 環境中執行程式碼，而不需要先建立 VM 或發佈 Web 應用程式。 Linux 裝載功能目前為預覽狀態，並且需要 [Functions 2.0 執行階段](functions-versions.md)。

本快速入門文章會逐步解說如何使用 Azure CLI 建立第一個在 Linux 上執行的函式應用程式。 您可以使用 [Azure Functions Core Tools](functions-run-local.md) 在本機建立函式程式碼，然後將其部署至 Azure。

下列步驟適用於 Mac、Windows 或 Linux 電腦。 本文說明如何以 JavaScript 或 C# 建立函式。

## <a name="prerequisites"></a>必要條件

在執行此範例之前，您必須具備下列項目︰

+ 安裝 [Azure Core Tools 2.x 版](functions-run-local.md#v2)。

+ 安裝 [Azure CLI]( /cli/azure/install-azure-cli)。 此文章需要 Azure CLI 2.0 版或更新版本。 執行 `az --version` 以尋找您擁有的版本。 您也可以使用 [Azure Cloud Shell](https://shell.azure.com/bash)。

+ 有效的 Azure 訂用帳戶。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-the-local-function-app-project"></a>建立本機函式應用程式專案

從命令列執行下列命令，以在目前本機目錄的 `MyFunctionProj` 資料夾中建立函式應用程式專案。 系統也會在 `MyFunctionProj` 中建立 GitHub 存放庫。

```bash
func init MyFunctionProj
```

當出現提示時，請使用方向鍵從下列語言選項中選取背景工作角色執行階段：

+ `dotnet`：建立 .NET 類別庫專案 (.csproj)。
+ `node`：建立 JavaScript 專案。

```output
Writing .gitignore
Writing host.json
Writing local.settings.json
Initialized empty Git repository in C:/functions/MyFunctionProj/.git/
```

[!INCLUDE [functions-create-function-core-tools](../../includes/functions-create-function-core-tools.md)]

[!INCLUDE [functions-update-function-code](../../includes/functions-update-function-code.md)]

[!INCLUDE [functions-run-function-test-local](../../includes/functions-run-function-test-local.md)]

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-linux-function-app-in-azure"></a>在 Azure 中建立 Linux 函式應用程式

您必須擁有函式應用程式以便在 Linux 上主控函式的執行。 函式應用程式可提供無伺服器環境讓您執行函式程式碼。 它可讓您將多個函式群組為邏輯單位，以方便您管理、部署和共用資源。 使用 [az functionapp create](/cli/azure/functionapp#az_functionapp_create) 命令，建立在 Linux 上執行的函式應用程式。

在下列命令中，請使用唯一函式應用程式名稱來替代您看到的 `<app_name>` 預留位置，並使用儲存體帳戶名稱來替代 `<storage_name>`。 `<app_name>` 也是函式應用程式的預設 DNS 網域。 此名稱在 Azure 中的所有應用程式之間必須是唯一的。

```azurecli
az functionapp create --name <app_name> --storage-account  <storage_name>  --resource-group myResourceGroup \
--location "westus" --is-linux
```

> [!NOTE]
> 如果您有現有的資源群組名為 `myResourceGroup`，且該群組中含有任何非 Linux App Service 應用程式，您就必須使用不同的資源群組。 您無法將 Windows 和 Linux 應用程式裝載在相同的資源群組中。  

建立函式應用程式之後，您會看到下列訊息：

```output
Your serverless Linux function app 'myfunctionapp' has been successfully created.
To active this function app, publish your app content using Azure Functions Core Tools or the Azure portal.
```

現在，您可以將專案發佈至 Azure 中的新函式應用程式。

[!INCLUDE [functions-publish-project](../../includes/functions-publish-project.md)]

[!INCLUDE [functions-test-function-code](../../includes/functions-test-function-code.md)]

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

## <a name="next-steps"></a>後續步驟

本文說明如何在預設的 Azure App Service 容器中裝載函式應用程式。 您也可以在 Linux 上將函式裝載到自己的的自訂容器中。

> [!div class="nextstepaction"] 
> [在使用自訂映像的 Linux 上建立函式](functions-create-function-linux-custom-image.md)
