---
title: 使用 Azure PowerShell 管理 Azure 資源管理員群組 |Microsoft Docs
description: 使用 Azure PowerShell 來管理您的 Azure Resource Manager 群組。
services: azure-resource-manager
documentationcenter: ''
author: mumian
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: jgao
ms.openlocfilehash: 3d6a102b794ca9c43e1dd18f923f6ce224596499
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/20/2019
ms.locfileid: "67296252"
---
# <a name="manage-azure-resource-manager-resource-groups-by-using-azure-powershell"></a>使用 Azure PowerShell 管理 Azure Resource Manager 資源群組

了解如何使用 Azure PowerShell [Azure Resource Manager](resource-group-overview.md)來管理您的 Azure 資源群組。 如需管理 Azure 資源，請參閱[使用 Azure PowerShell 管理 Azure 資源](./manage-resources-powershell.md)。

關於管理資源群組的其他文章：

- [使用 Azure 入口網站管理 Azure 資源群組](./manage-resources-portal.md)
- [使用 Azure CLI 管理 Azure 資源群組](./manage-resources-cli.md)

## <a name="what-is-a-resource-group"></a>什麼是資源群組

資源群組是存放 Azure 方案相關資源的容器。 資源群組可以包含方案的所有資源，或只包含您要以群組方式管理的資源。 您可決定如何根據對組織最有利的方式，將資源配置到資源群組。 一般而言，會新增共用相同生命週期的資源到相同資源群組，因此您可以以群組為單位輕鬆地部署、更新、刪除它們。

資源群組會儲存資源相關中繼資料。 因此，當您指定資源群組的位置時，您便是指定中繼資料的儲存位置。 基於相容性理由，您可能需要確保您的資料存放在特定區域中。

資源群組會儲存資源相關中繼資料。 當您指定的資源群組的位置時，您指定儲存中繼資料。

## <a name="create-resource-groups"></a>建立資源群組

下列 PowerShell 指令碼會建立資源群組，並顯示資源群組。

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location

Get-AzResourceGroup -Name $resourceGroupName
```

## <a name="list-resource-groups"></a>列出資源群組

下列 PowerShell 指令碼會列出您訂用帳戶下的資源群組。

```azurepowershell-interactive
Get-AzResourceGroup
```

若要取得某個資源群組：

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

Get-AzResourceGroup -Name $resourceGroupName
```

## <a name="delete-resource-groups"></a>刪除資源群組

下列 PowerShell 指令碼會刪除資源群組：

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

Remove-AzResourceGroup -Name $resourceGroupName
```

如需 Azure Resource Manager 如何排序資源的刪除作業的詳細資訊，請參閱[Azure Resource Manager 資源群組刪除](./resource-group-delete.md)。

## <a name="deploy-resources-to-an-existing-resource-group"></a>將資源部署至現有的資源群組

請參閱[將資源部署至現有的資源群組](./manage-resources-powershell.md#deploy-resources-to-an-existing-resource-group)。

若要驗證資源群組部署，請參閱[測試 AzResourceGroupDeployment](https://docs.microsoft.com/powershell/module/Az.Resources/Test-AzResourceGroupDeployment?view=azps-1.3.0)。

## <a name="deploy-a-resource-group-and-resources"></a>部署資源群組和資源

您可以建立資源群組，並將資源部署至該群組中，使用 Resource Manager 範本。 如需詳細資訊，請參閱[建立資源群組並部署資源](./deploy-to-subscription.md#create-resource-group-and-deploy-resources)。

## <a name="redeploy-when-deployment-fails"></a>部署失敗時重新部署

這項功能就是所謂*錯誤時回復*。 如需詳細資訊，請參閱 <<c0> [ 重新部署部署失敗時](./resource-group-template-deploy.md#redeploy-when-deployment-fails)。

## <a name="move-to-another-resource-group-or-subscription"></a>移至另一個資源群組或訂用帳戶

您可以移動的資源群組中，另一個資源群組。 如需詳細資訊，請參閱 [將資源移動到新的資源群組或訂用帳戶](./resource-group-move-resources.md#move-resources)。

## <a name="lock-resource-groups"></a>鎖定資源群組

鎖定可防止不小心刪除或修改重要資源，例如 Azure 訂用帳戶、 資源群組或資源組織中的其他使用者。 

下列指令碼會鎖定資源群組，因此無法刪除資源群組。

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

New-AzResourceLock -LockName LockGroup -LockLevel CanNotDelete -ResourceGroupName $resourceGroupName 
```

下列指令碼會取得資源群組中的所有鎖定：

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

Get-AzResourceLock -ResourceGroupName $resourceGroupName 
```

如需詳細資訊，請參閱[使用 Azure Resource Manager 來鎖定資源](resource-group-lock-resources.md)。

## <a name="tag-resource-groups"></a>標記資源群組

您可以將標籤套用至資源群組和資源，以便以邏輯方式組織您的資產。 如需資訊，請參閱[使用標記來組織您的 Azure 資源](./resource-group-using-tags.md#powershell)。

## <a name="export-resource-groups-to-templates"></a>將資源群組匯出範本

設定好您的資源群組之後，您可以檢視資源群組的 Resource Manager 範本。 匯出此範本有兩個優點︰

- 因為範本包含完整的基礎結構，請將自動化解決方案的未來部署。
- 藉由尋找在 JavaScript Object Notation (JSON) 表示您的解決方案，了解範本語法。

若要匯出的資源群組中的所有資源，請使用[都匯出 AzResourceGroup](/powershell/module/az.resources/Export-AzResourceGroup) cmdlet 和提供的資源群組名稱。

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

Export-AzResourceGroup -ResourceGroupName $resourceGroupName
```

它會將範本儲存為本機檔案中。

而不要匯出資源群組中的所有資源，您可以選取要匯出哪一個資源。

若要匯出一個資源，將該資源識別碼。

```azurepowershell-interactive
$resource = Get-AzResource `
  -ResourceGroupName <resource-group-name> `
  -ResourceName <resource-name> `
  -ResourceType <resource-type>
Export-AzResourceGroup `
  -ResourceGroupName <resource-group-name> `
  -Resource $resource.ResourceId
```

若要匯出多個資源，請傳遞陣列中的資源識別碼。

```azurepowershell-interactive
Export-AzResourceGroup `
  -ResourceGroupName <resource-group-name> `
  -Resource @($resource1.ResourceId, $resource2.ResourceId)
```

當匯出範本，您可以指定是否在範本中使用參數。 根據預設，資源名稱的參數會包含，但沒有預設值。 您必須在部署期間傳遞該參數值。

```json
"parameters": {
  "serverfarms_demoHostPlan_name": {
    "defaultValue": null,
    "type": "String"
  },
  "sites_webSite3bwt23ktvdo36_name": {
    "defaultValue": null,
    "type": "String"
  }
}
```

在資源中，參數用於的名稱。

```json
"resources": [
  {
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2016-09-01",
    "name": "[parameters('serverfarms_demoHostPlan_name')]",
    ...
  }
]
```

如果您使用`-IncludeParameterDefaultValue`參數匯出範本時，範本參數包含預設值設為目前的值，這個值。 您可以使用該預設值，或藉由傳入不同的值來覆寫預設值。

```json
"parameters": {
  "serverfarms_demoHostPlan_name": {
    "defaultValue": "demoHostPlan",
    "type": "String"
  },
  "sites_webSite3bwt23ktvdo36_name": {
    "defaultValue": "webSite3bwt23ktvdo36",
    "type": "String"
  }
}
```

如果您使用`-SkipResourceNameParameterization`時匯出的範本參數的資源名稱不包含在範本中的參數。 相反地，直接在其目前值的資源上設定的資源名稱。 您無法在部署期間自訂名稱。

```json
"resources": [
  {
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2016-09-01",
    "name": "demoHostPlan",
    ...
  }
]
```

如需詳細資訊，請參閱 <<c0> [ 單一和多重資源匯出至 Azure 入口網站中的範本](./export-template-portal.md)。

## <a name="manage-access-to-resource-groups"></a>管理資源群組的存取權

[角色型存取控制 (RBAC)](../role-based-access-control/overview.md) 是您對 Azure 中的資源存取進行管理的機制。 如需詳細資訊，請參閱 <<c0> [ 使用 RBAC 和 Azure PowerShell 管理存取](../role-based-access-control/role-assignments-powershell.md)。

## <a name="next-steps"></a>後續步驟

- 若要深入了解 Azure Resource Manager，請參閱[Azure Resource Manager 概觀](./resource-group-overview.md)。
- 若要深入了解 Resource Manager 範本語法，請參閱[了解的結構和 Azure Resource Manager 範本的語法](./resource-group-authoring-templates.md)。
- 若要了解如何開發的範本，請參閱[逐步教學課程](/azure/azure-resource-manager/)。
- 若要檢視 Azure Resource Manager 範本結構描述，請參閱[範本參考](/azure/templates/)。