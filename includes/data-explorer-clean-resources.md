---
author: lucygoldbergmicrosoft
ms.service: data-explorer
ms.topic: include
ms.date: 11/28/2019
ms.author: lugoldbe
ms.openlocfilehash: 9598724db630db7fc48545b58f2b0c1cad464aa4
ms.sourcegitcommit: 3d4917ed58603ab59d1902c5d8388b954147fe50
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/02/2019
ms.locfileid: "74667860"
---
## <a name="clean-up-resources"></a>清除資源

不再需要 Azure 資源時，可藉由刪除資源群組來清除您所部署的資源。 

### <a name="clean-up-resources-using-the-azure-portal"></a>使用 Azure 入口網站清除資源

遵循[清除資源](../articles/data-explorer/create-cluster-database-portal.md#clean-up-resources)中的步驟，以刪除 Azure 入口網站中的資源。

### <a name="clean-up-resources-using-powershell"></a>使用 PowerShell 清除資源

如果 Cloud Shell 仍為開啟狀態，您就不需要複製/執行第一行（讀取主機）。

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name that you used in the last procedure"
$resourceGroupName = "${projectName}rg"

Remove-AzResourceGroup -ResourceGroupName $resourceGroupName

Write-Host "Press [ENTER] to continue ..."
```