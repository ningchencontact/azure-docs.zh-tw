---
title: 鎖定 Azure 資源以防止變更 | Microsoft Docs
description: 透過將鎖定套用到所有使用者和角色，防止使用者更新或刪除重要的 Azure 資源。
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 53c57e8f-741c-4026-80e0-f4c02638c98b
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/21/2019
ms.author: tomfitz
ms.openlocfilehash: 83518825c91cdd727b3d4fb9ecc86d51dea8fc26
ms.sourcegitcommit: a4efc1d7fc4793bbff43b30ebb4275cd5c8fec77
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/21/2019
ms.locfileid: "56649164"
---
# <a name="lock-resources-to-prevent-unexpected-changes"></a>鎖定資源以防止非預期的變更 

身為系統管理員，您可能需要鎖定訂用帳戶、資源群組或資源，以防止組織中的其他使用者不小心刪除或修改重要資源。 您可以將鎖定層級設定為 **CanNotDelete** 或 **ReadOnly**。 在入口網站中，鎖定分別名為 [刪除] 和 [唯讀]。

* **CanNotDelete** 表示經過授權的使用者仍然可以讀取和修改資源，但無法刪除資源。 
* **ReadOnly** 表示經過授權的使用者可以讀取資源，但無法刪除或更新資源。 套用這個鎖定類似於限制所有經過授權使用者的權限是由「讀取者」角色所授與。 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="how-locks-are-applied"></a>如何套用鎖定

當您在父範圍套用鎖定時，該範圍內的所有資源都會都繼承相同的鎖定。 即使是之后添加的资源也会从父作用域继承该锁。 继承中限制性最强的锁优先执行。

不同於角色型存取控制，您可以使用管理鎖定來對所有使用者和角色套用限制。 若要了解如何为用户和角色设置权限，请参阅 [Azure 基于角色的访问控制](../role-based-access-control/role-assignments-portal.md)。

Resource Manager 鎖定只會套用於管理平面發生的作業，亦即要傳送至 `https://management.azure.com` 的作業。 鎖定並不會限制資源執行自己函式的方式。 限制資源的變更，但沒有限制資源的作業。 比方說，在 SQL Database 上的 ReadOnly 鎖定會防止您刪除或修改資料庫，但它不會阻止您建立、 更新或刪除資料庫中的資料。 已允許資料交易，因為這些作業不傳送至`https://management.azure.com`。

套用 **ReadOnly** 會導致無法預期的結果，因為有些看似讀取作業的作業實際上需要其他動作。 例如，將 **ReadOnly** 鎖定放置在儲存體帳戶上，會防止所有使用者列出金鑰。 清單金鑰作業是透過 POST 要求進行處理，因為傳回的金鑰可用於寫入作業。 至於其他範例，將 **ReadOnly** 鎖定放置在 App Service 資源，會防止 Visual Studio 伺服器總管顯示資源的檔案，因為該互動需要寫入存取權。

## <a name="who-can-create-or-delete-locks-in-your-organization"></a>誰可以建立或刪除您的組織中的鎖定
若要创建或删除管理锁，必须有权执行 `Microsoft.Authorization/*` 或 `Microsoft.Authorization/locks/*` 操作。 在内置角色中，只有“所有者”和“用户访问管理员”有权执行这些操作。

## <a name="portal"></a>入口網站
[!INCLUDE [resource-manager-lock-resources](../../includes/resource-manager-lock-resources.md)]

## <a name="template"></a>範本

當使用 Resource Manager 範本部署鎖定，您可以使用不同的值名稱和鎖定類型視範圍而定。

套用鎖定時**資源**，使用下列格式：

* name - `{resourceName}/Microsoft.Authorization/{lockName}`
* type - `{resourceProviderNamespace}/{resourceType}/providers/locks`

套用鎖定時**資源群組**或是**訂用帳戶**，使用下列格式：

* name - `{lockName}`
* type - `Microsoft.Authorization/locks`

下列範例示範建立應用程式服務方案的範本、網站和網站上的鎖定。 鎖定的資源類型為要鎖定之資源的資源類型和 **/providers/locks**。 鎖定名稱的建立方式是串連資源名稱與 **/Microsoft.Authorization/** 和鎖定的名稱。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "hostingPlanName": {
            "type": "string"
        }
    },
    "variables": {
        "siteName": "[concat('ExampleSite', uniqueString(resourceGroup().id))]"
    },
    "resources": [
        {
            "apiVersion": "2016-09-01",
            "type": "Microsoft.Web/serverfarms",
            "name": "[parameters('hostingPlanName')]",
            "location": "[resourceGroup().location]",
            "sku": {
                "tier": "Free",
                "name": "f1",
                "capacity": 0
            },
            "properties": {
                "targetWorkerCount": 1
            }
        },
        {
            "apiVersion": "2016-08-01",
            "name": "[variables('siteName')]",
            "type": "Microsoft.Web/sites",
            "location": "[resourceGroup().location]",
            "dependsOn": [
                "[resourceId('Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
            ],
            "properties": {
                "serverFarmId": "[parameters('hostingPlanName')]"
            }
        },
        {
            "type": "Microsoft.Web/sites/providers/locks",
            "apiVersion": "2016-09-01",
            "name": "[concat(variables('siteName'), '/Microsoft.Authorization/siteLock')]",
            "dependsOn": [
                "[resourceId('Microsoft.Web/sites', variables('siteName'))]"
            ],
            "properties": {
                "level": "CanNotDelete",
                "notes": "Site should not be deleted."
            }
        }
    ]
}
```

資源群組上設定鎖定的範例，請參閱 <<c0> [ 建立資源群組，並將其鎖定](https://github.com/Azure/azure-quickstart-templates/tree/master/subscription-level-deployments/create-rg-lock-role-assignment)。

## <a name="powershell"></a>PowerShell
您可以使用 Azure PowerShell 以 [New-AzResourceLock](/powershell/module/az.resources/new-azresourcelock) 命令鎖定已部署的資源。

若要鎖定資源，請提供資源的名稱、其資源類型，以及其資源群組名稱。

```azurepowershell-interactive
New-AzResourceLock -LockLevel CanNotDelete -LockName LockSite -ResourceName examplesite -ResourceType Microsoft.Web/sites -ResourceGroupName exampleresourcegroup
```

若要锁定某个资源组，请提供该资源组的名称。

```azurepowershell-interactive
New-AzResourceLock -LockName LockGroup -LockLevel CanNotDelete -ResourceGroupName exampleresourcegroup
```

若要取得鎖定的相關資訊，請使用 [Get-AzureRmResourceLock](/powershell/module/az.resources/get-azresourcelock)。 若要取得訂用帳戶中的所有鎖定，請使用︰

```azurepowershell-interactive
Get-AzResourceLock
```

若要取得資源的所有鎖定，請使用︰

```azurepowershell-interactive
Get-AzResourceLock -ResourceName examplesite -ResourceType Microsoft.Web/sites -ResourceGroupName exampleresourcegroup
```

若要取得資源群組的所有鎖定，請使用︰

```azurepowershell-interactive
Get-AzResourceLock -ResourceGroupName exampleresourcegroup
```

若要將鎖定刪除，請使用：

```azurepowershell-interactive
$lockId = (Get-AzResourceLock -ResourceGroupName exampleresourcegroup -ResourceName examplesite -ResourceType Microsoft.Web/sites).LockId
Remove-AzResourceLock -LockId $lockId
```

## <a name="azure-cli"></a>Azure CLI

您可使用 [az lock create](/cli/azure/lock#az-lock-create) 命令，透過 Azure CLI 來鎖定已部署的資源。

若要鎖定資源，請提供資源的名稱、其資源類型，以及其資源群組名稱。

```azurecli
az lock create --name LockSite --lock-type CanNotDelete --resource-group exampleresourcegroup --resource-name examplesite --resource-type Microsoft.Web/sites
```

若要鎖定資源群組，請提供資源群組的名稱。

```azurecli
az lock create --name LockGroup --lock-type CanNotDelete --resource-group exampleresourcegroup
```

若要取得鎖定的相關資訊，請使用 [az lock list](/cli/azure/lock#az-lock-list)。 若要获取订阅中的所有锁，请使用：

```azurecli
az lock list
```

若要取得資源的所有鎖定，請使用︰

```azurecli
az lock list --resource-group exampleresourcegroup --resource-name examplesite --namespace Microsoft.Web --resource-type sites --parent ""
```

若要取得資源群組的所有鎖定，請使用︰

```azurecli
az lock list --resource-group exampleresourcegroup
```

若要將鎖定刪除，請使用：

```azurecli
lockid=$(az lock show --name LockSite --resource-group exampleresourcegroup --resource-type Microsoft.Web/sites --resource-name examplesite --output tsv --query id)
az lock delete --ids $lockid
```

## <a name="rest-api"></a>REST API
您可以使用[管理鎖定的 REST API](https://docs.microsoft.com/rest/api/resources/managementlocks)，來鎖定已部署的資源。 此 REST API 可讓您建立及刪除鎖定，以及抓取現有鎖定的相關資訊。

若要建立鎖定，請執行：

    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/locks/{lock-name}?api-version={api-version}

範圍可以是訂用帳戶、資源群組或資源。 lock-name 是您想要命名鎖定的任何名稱。 對於 api-version，請使用**2016年-09-01**。

在要求中，包含指定鎖定屬性的 JSON 物件。

    {
      "properties": {
        "level": "CanNotDelete",
        "notes": "Optional text notes."
      }
    } 

## <a name="next-steps"></a>後續步驟
* 若要了解如何邏輯地組織您的資源，請參閱 [使用標記來組織您的資源](resource-group-using-tags.md)
* 您可以使用自訂原則，在訂用帳戶內套用限制和慣例。 如需詳細資訊，請參閱[何謂 Azure 原則？](../governance/policy/overview.md)。
* 如需關於企業如何使用 Resource Manager 有效地管理訂用帳戶的指引，請參閱 [Azure 企業 Scaffold - 規定的訂用帳戶治理](/azure/architecture/cloud-adoption-guide/subscription-governance)。

