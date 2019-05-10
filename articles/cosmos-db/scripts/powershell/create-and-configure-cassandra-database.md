---
title: Azure PowerShell 指令碼 - 建立 Azure Cosmos DB Cassandra API 帳戶
description: Azure PowerShell 指令碼範例 - 建立 Azure Cosmos DB Cassandra API 帳戶
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.devlang: PowerShell
ms.topic: sample
ms.date: 08/08/2018
ms.reviewer: sngun
ms.openlocfilehash: c008abcb0fb18c83772678cb0dc34481dc1d7d75
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2019
ms.locfileid: "65079553"
---
# <a name="azure-cosmos-db-create-a-cassandra-api-account-using-powershell"></a>Azure Cosmos DB：使用 PowerShell 來建立 Cassandra API 帳戶

此範例 PowerShell 指令碼會建立 Azure Cosmos DB Cassandra API 帳戶。 

[!INCLUDE [sample-powershell-install](../../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>範例指令碼

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!code-powershell[main](../../../../powershell_scripts/cosmosdb/create-and-configure-cassandra-database/create-and-configure-cassandra-database.ps1?highlight=9,12-15,18,21-23,26-29,32-37 "Create an Azure Cosmos DB account")]

## <a name="clean-up-deployment"></a>清除部署

在執行過指令碼範例之後，您可以使用下列命令來移除資源群組和所有與其相關聯的資源。

```powershell
Remove-AzResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="script-explanation"></a>指令碼說明

此指令碼會使用下列命令。 下表中的每個命令都會連結至命令特定的文件。

| 命令 | 注意 |
|---|---|
| [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) | 建立用來存放所有資源的資源群組。 |
| [New-AzResource](https://docs.microsoft.com/powershell/module/az.resources/new-azresource) | 建立主機資料庫或彈性集區的邏輯伺服器。 |
| [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) | 刪除資源群組，包括所有的巢狀資源。 |
|||

## <a name="next-steps"></a>後續步驟

如需有關 Azure PowerShell 的詳細資訊，請參閱 [Azure PowerShell 文件](https://docs.microsoft.com/powershell/)。

您可以在 [Azure Cosmos DB PowerShell 指令碼](../../powershell-samples.md)中找到其他 Azure Cosmos DB PowerShell 指令碼範例。
