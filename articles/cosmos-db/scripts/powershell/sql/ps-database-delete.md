---
title: Azure PowerShell 指令碼-刪除 Azure Cosmos 帳戶中的資料庫
description: Azure PowerShell 指令碼範例-刪除 Azure Cosmos 帳戶中的資料庫
author: markjbrown
ms.service: cosmos-db
ms.topic: samples
ms.date: 05/08/2019
ms.author: mjbrown
ms.openlocfilehash: a8ecdb7456b13b21f814a3c2eda924aa9b3f48fa
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2019
ms.locfileid: "65077721"
---
# <a name="delete-a-database-in-an-azure-cosmos-account-in-an-azure-cosmos-account-using-powershell"></a>刪除 Azure Cosmos 帳戶中使用 PowerShell 的 Azure Cosmos 帳戶資料庫

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>範例指令碼

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/sql/ps-database-delete.ps1 "Delete a database in an Azure Cosmos account")]

## <a name="clean-up-deployment"></a>清除部署

运行脚本示例后，可以使用以下命令删除资源组以及与其关联的所有资源。

```powershell
Remove-AzResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="script-explanation"></a>指令碼說明

此指令碼會使用下列命令。 下表中的每個命令都會連結至命令特定的文件。

| 命令 | 注意 |
|---|---|
|**Azure 資源**| |
| [New-AzResource](https://docs.microsoft.com/powershell/module/az.resources/new-azresource) | 建立資源。 |
| [Get-AzResource](https://docs.microsoft.com/powershell/module/az.resources/get-azresource) | 取得資源。 |
| [Set-AzResource](https://docs.microsoft.com/powershell/module/az.resources/set-azresource) | 更新資源。 |
| [Invoke-AzResourceAction](https://docs.microsoft.com/powershell/module/az.resources/invoke-azresourceaction) | 叫用資源的動作。 |
| [Remove-AzResource](https://docs.microsoft.com/powershell/module/az.resources/remove-azresource) | 刪除資源。 |
|**Azure 資源群組**| |
| [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) | 建立用來存放所有資源的資源群組。 |
| [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) | 刪除資源群組，包括所有的巢狀資源。 |
|||

## <a name="next-steps"></a>後續步驟

如需有關 Azure PowerShell 的詳細資訊，請參閱 [Azure PowerShell 文件](https://docs.microsoft.com/powershell/)。

您可以在 [Azure Cosmos DB PowerShell 指令碼](../../../powershell-samples.md)中找到其他 Azure Cosmos DB PowerShell 指令碼範例。