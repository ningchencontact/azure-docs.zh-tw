---
title: Azure CLI 指令碼 - 建立高可用性的容錯移轉原則 | Microsoft Docs
description: Azure CLI 指令碼範例 - 建立高可用性的容錯移轉原則
services: cosmos-db
documentationcenter: cosmosdb
author: SnehaGunda
manager: kfile
tags: azure-service-management
ms.service: cosmos-db
ms.custom: mvc
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: cosmosdb
ms.workload: database
ms.date: 06/02/2017
ms.author: sngun
ms.openlocfilehash: 94d91c59aef569b952799ede45222914af698bc4
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/05/2018
ms.locfileid: "34795511"
---
# <a name="create-a-failover-policy-for-high-availability-using-the-azure-cli"></a>使用 Azure CLI 建立高可用性的容錯移轉原則

此範例 CLI 指令碼會建立 Azure Cosmos DB 帳戶，然後將它設定為高可用性。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

如果您選擇在本機安裝和使用 CLI，本主題會要求您執行 Azure CLI 2.0 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI 2.0]( /cli/azure/install-azure-cli)。 

## <a name="sample-script"></a>範例指令碼

[!code-azurecli-interactive[main](../../../cli_scripts/cosmosdb/high-availability-cosmosdb-configure-failover/high-availability-cosmosdb-configure-failover.sh?highlight=23-27 "Create an Azure Cosmos DB failover policy")]

## <a name="clean-up-deployment"></a>清除部署

在執行過指令碼範例之後，您可以使用下列命令來移除資源群組和所有與其相關聯的資源。

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>指令碼說明

此指令碼會使用下列命令。 下表中的每個命令都會連結至命令特定的文件。

| 命令 | 注意 |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | 建立用來存放所有資源的資源群組。 |
| [az cosmosdb create](/cli/azure/sql/server#az_sql_server_create) | 建立 Azure Cosmos DB 帳戶。 |
| [az cosmosdb update](/cli/azure/cosmosdb#az_cosmosdb_update) | 更新 Azure Cosmos DB 帳戶。 |
| [az group delete](/cli/azure/resource#az_resource_delete) | 刪除資源群組，包括所有的巢狀資源。 |

## <a name="next-steps"></a>後續步驟

如需 Azure CLI 的詳細資訊，請參閱 [Azure CLI 文件](https://docs.microsoft.com/cli/azure)。

您可以在 [Azure Cosmos DB CLI 文件](../cli-samples.md)中找到其他 Azure Cosmos DB CLI 指令碼範例。
