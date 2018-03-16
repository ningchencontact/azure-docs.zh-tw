---
title: "建立連線至 Azure Cosmos DB 的 Azure 函式 | Microsoft Docs"
description: "Azure CLI 指令碼範例 - 建立連線至 Azure Cosmos DB 的 Azure 函式"
services: functions
documentationcenter: functions
author: ggailey777
manager: cfowler
editor: 
tags: functions
ms.assetid: 
ms.service: functions
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: 
ms.date: 01/22/2018
ms.author: glenga
ms.custom: mvc
ms.openlocfilehash: 092e0681b0491fc1c54c19e234aafdac6d428fd1
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/09/2018
---
# <a name="create-an-azure-function-that-connects-to-an-azure-cosmos-db"></a>建立連線至 Azure Cosmos DB 的 Azure 函式

這個 Azure Functions 範例指令碼會建立函式應用程式，並將函式連線至 Azure Cosmos DB 資料庫。 包含連線的所建立應用程式設定可以搭配 [Azure Cosmos DB 觸發程序或繫結](..\functions-bindings-cosmosdb.md)使用。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

如果您在本機使用 CLI，請確定您是執行 Azure CLI 2.0 版或更新版本。 若要尋找版本，請執行 `az --version`。 如果您需要安裝或升級，請參閱[安裝 Azure CLI 2.0](/cli/azure/install-azure-cli)。 

## <a name="sample-script"></a>範例指令碼

這個範例會建立 Azure 函數應用程式，並在應用程式設定中新增 Cosmos DB 端點和存取金鑰。

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/create-function-app-connect-to-cosmos-db/create-function-app-connect-to-cosmos-db.sh "Create an Azure Function that connects to an Azure Cosmos DB")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>指令碼說明

此指令碼會使用下列命令：下表中的每個命令都會連結至命令特定的文件。

| 命令 | 注意 |
|---|---|
| [az login](https://docs.microsoft.com/cli/azure/reference-index#az_login) | 登入 Azure。 |
| [az group create](https://docs.microsoft.com/cli/azure/group#az_group_create) | 指定位置建立資源群組 |
| [az storage accounts create](https://docs.microsoft.com/cli/azure/storage/account) | 建立儲存體帳戶 |
| [az functionapp create](https://docs.microsoft.com/cli/azure/functionapp#az_functionapp_create) | 建立新的函式應用程式 |
| [az cosmosdb create](https://docs.microsoft.com/cli/azure/cosmosdb#az_cosmosdb_create) | 建立 cosmosdb 資料庫 |
| [az group delete](https://docs.microsoft.com/cli/azure/group#az_group_delete) | 清除 |

## <a name="next-steps"></a>後續步驟

如需 Azure CLI 的詳細資訊，請參閱 [Azure CLI 文件](https://docs.microsoft.com/cli/azure)。

您可以在 [Azure Functions 文件](../functions-cli-samples.md)中找到其他 Azure Functions CLI 指令碼範例。




