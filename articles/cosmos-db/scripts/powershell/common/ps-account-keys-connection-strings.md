---
title: Azure PowerShell 指令碼-帳戶金鑰和連接字串的 Azure Cosmos 帳戶的作業
description: Azure PowerShell 指令碼範例-Azure Cosmos 帳戶的帳戶金鑰和連接字串作業
author: markjbrown
ms.service: cosmos-db
ms.topic: samples
ms.date: 05/20/2019
ms.author: mjbrown
ms.openlocfilehash: a7f86bfee0ee2472260b622dc709dc437e53414c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66247855"
---
# <a name="connection-string-and-account-key-operations-for-an-azure-cosmos-account-using-powershell"></a>連接字串和帳戶金鑰的作業使用 PowerShell 的 Azure Cosmos 帳戶

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>範例指令碼

此範例需要存在於資源群組和帳戶。 使用現有的 PowerShell 來建立範例，以佈建帳戶時，第一次。

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/common/ps-account-keys-connection-strings.ps1 "Connection strings and account keys for Azure Cosmos account")]

## <a name="clean-up-deployment"></a>清除部署

在執行過指令碼範例之後，您可以使用下列命令來移除資源群組和所有與其相關聯的資源。

```powershell
Remove-AzResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="script-explanation"></a>指令碼說明

此指令碼會使用下列命令。 下表中的每個命令都會連結至命令特定的文件。

| 命令 | 注意 |
|---|---|
|**Azure 資源**| |
| [Invoke-AzResourceAction](https://docs.microsoft.com/powershell/module/az.resources/invoke-azresourceaction) | 叫用資源的動作。 |
|**Azure 資源群組**| |
| [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) | 刪除資源群組，包括所有的巢狀資源。 |
|||

## <a name="next-steps"></a>後續步驟

如需有關 Azure PowerShell 的詳細資訊，請參閱 [Azure PowerShell 文件](https://docs.microsoft.com/powershell/)。

您可以在 [Azure Cosmos DB PowerShell 指令碼](../../../powershell-samples.md)中找到其他 Azure Cosmos DB PowerShell 指令碼範例。