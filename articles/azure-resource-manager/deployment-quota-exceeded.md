---
title: 已超過 Azure 部署配額
description: 描述如何解決資源群組歷程記錄中有超過800個部署的錯誤。
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: troubleshooting
ms.date: 10/01/2019
ms.author: tomfitz
ms.openlocfilehash: f06aff74e2cf800d44115f34921825122b09a9e7
ms.sourcegitcommit: a19f4b35a0123256e76f2789cd5083921ac73daf
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/02/2019
ms.locfileid: "71719431"
---
# <a name="resolve-error-when-deployment-count-exceeds-800"></a>解決部署計數超過800的錯誤

在其部署歷程記錄中，每個資源群組都限制為800個部署。 本文說明當部署失敗時所收到的錯誤，因為它會超過允許的800部署。 若要解決此錯誤，請從資源群組歷程記錄中刪除部署。 從歷程記錄中刪除部署，並不會影響任何已部署的資源。

## <a name="symptom"></a>徵兆

在部署期間，您會收到錯誤，指出目前的部署將會超過800部署的配額。

## <a name="solution"></a>方案

### <a name="azure-cli"></a>Azure CLI

使用[az group deployment delete](/cli/azure/group/deployment#az-group-deployment-delete)命令，從歷程記錄中刪除部署。

```azurecli-interactive
az group deployment delete --resource-group exampleGroup --name deploymentName
```

您可以使用下列命令取得部署歷程記錄中的目前計數：

```azurecli-interactive
az group deployment list --resource-group exampleGroup --query "length(@)"
```

### <a name="azure-powershell"></a>Azure PowerShell

使用[new-azresourcegroupdeployment](/powershell/module/az.resources/remove-azresourcegroupdeployment)命令，從歷程記錄中刪除部署。

```azurepowershell-interactive
Remove-AzResourceGroupDeployment -ResourceGroupName exampleGroup -Name deploymentName
```

您可以使用下列命令取得部署歷程記錄中的目前計數：

```azurepowershell-interactive
(Get-AzResourceGroupDeployment -ResourceGroupName exampleGroup).Count
```

