---
title: 針對邏輯組織標記 Azure 資源 | Microsoft Docs
description: 示範如何套用標籤以針對計費及管理來組織 Azure 資源。
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: tomfitz
ms.openlocfilehash: e7763889ecf69231b7a4daf31e6899b33f3e2b36
ms.sourcegitcommit: fa5ce8924930f56bcac17f6c2a359c1a5b9660c9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/31/2019
ms.locfileid: "73199167"
---
# <a name="use-tags-to-organize-your-azure-resources"></a>使用標記來組織 Azure 資源

[!INCLUDE [resource-manager-governance-tags](../../includes/resource-manager-governance-tags.md)]

若要將標記套用到資源，使用者必須具有該資源類型的寫入權限。 若要將標記套用到所有資源類型，請使用[參與者](../role-based-access-control/built-in-roles.md#contributor)角色。 若只要將標記套用到一個資源類型，則使用適用於該資源的參與者角色。 例如，若要將標記套用到虛擬機器，可使用[虛擬機器參與者](../role-based-access-control/built-in-roles.md#virtual-machine-contributor)。

[!INCLUDE [Handle personal data](../../includes/gdpr-intro-sentence.md)]

## <a name="policies"></a>原則

您可以使用 [Azure 原則](../governance/policy/overview.md)來強制標記規則和慣例。 建立原則，就可以避免將資源部署到不符合貴組織預期標記的訂用帳戶。 您可以建立一個原則，以便在部署期間自動套用所需的標記，而不需手動套用標記或搜尋不符合規範的資源。 標籤現在也可以套用至具有新[修改](../governance/policy/concepts/effects.md#modify)效果和[補救](../governance/policy/how-to/remediate-resources.md)工作的現有資源。 下列區段會顯示標籤的範例原則。

[!INCLUDE [Tag policies](../../includes/azure-policy-samples-general-tags.md)]

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

若要查看*資源群組*的現有標籤，請使用：

```azurepowershell-interactive
(Get-AzResourceGroup -Name examplegroup).Tags
```

該指令碼會傳回下列格式︰

```powershell
Name                           Value
----                           -----
Dept                           IT
Environment                    Test
```

若要查看「具有指定之資源識別碼的資源」的現有標記，請使用：

```azurepowershell-interactive
(Get-AzResource -ResourceId /subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Storage/storageAccounts/<storage-name>).Tags
```

或者，若要查看「具有指定之名稱和資源群組的資源」的現有標記，請使用：

```azurepowershell-interactive
(Get-AzResource -ResourceName examplevnet -ResourceGroupName examplegroup).Tags
```

若要取得「具有特定標記的資源群組」，請使用：

```azurepowershell-interactive
(Get-AzResourceGroup -Tag @{ Dept="Finance" }).ResourceGroupName
```

若要取得「具有特定標記的資源」，請使用：

```azurepowershell-interactive
(Get-AzResource -Tag @{ Dept="Finance"}).Name
```

若要取得「具有特定標記名稱的資源」，請使用：

```azurepowershell-interactive
(Get-AzResource -TagName Dept).Name
```

每當您將標記套用到資源或資源群組時，即會覆寫該資源或資源群組上現有的標記。 因此，您必須視該資源或資源群組是否具備現有標籤來使用不同的方法。

若要將標籤新增至*不具現有標籤的資源群組*，請使用：

```azurepowershell-interactive
Set-AzResourceGroup -Name examplegroup -Tag @{ Dept="IT"; Environment="Test" }
```

若要將標記新增至「具有現有標記的資源群組」，請擷取現有標記、新增標記，然後重新套用標記：

```azurepowershell-interactive
$tags = (Get-AzResourceGroup -Name examplegroup).Tags
$tags.Add("Status", "Approved")
Set-AzResourceGroup -Tag $tags -Name examplegroup
```

若要將標籤新增至*不具現有標籤的資源*，請使用：

```azurepowershell-interactive
$r = Get-AzResource -ResourceName examplevnet -ResourceGroupName examplegroup
Set-AzResource -Tag @{ Dept="IT"; Environment="Test" } -ResourceId $r.ResourceId -Force
```

若要將標記新增至「沒有現有標記的資源」，請使用：

```azurepowershell-interactive
$r = Get-AzResource -ResourceName examplevnet -ResourceGroupName examplegroup
$r.Tags.Add("Status", "Approved")
Set-AzResource -Tag $r.Tags -ResourceId $r.ResourceId -Force
```

若要將所有標記從資源群組套用至其資源，而*不保留資源上的現有標記*，請使用下列腳本：

```azurepowershell-interactive
$groups = Get-AzResourceGroup
foreach ($g in $groups)
{
    Get-AzResource -ResourceGroupName $g.ResourceGroupName | ForEach-Object {Set-AzResource -ResourceId $_.ResourceId -Tag $g.Tags -Force }
}
```

若要將所有標記從資源群組套用至其資源，並*在不重複的資源上保留現有*的標籤，請使用下列腳本：

```azurepowershell-interactive
$group = Get-AzResourceGroup "examplegroup"
if ($null -ne $group.Tags) {
    $resources = Get-AzResource -ResourceGroupName $group.ResourceGroupName
    foreach ($r in $resources)
    {
        $resourcetags = (Get-AzResource -ResourceId $r.ResourceId).Tags
        if ($resourcetags)
        {
            foreach ($key in $group.Tags.Keys)
            {
                if (-not($resourcetags.ContainsKey($key)))
                {
                    $resourcetags.Add($key, $group.Tags[$key])
                }
            }
            Set-AzResource -Tag $resourcetags -ResourceId $r.ResourceId -Force
        }
        else
        {
            Set-AzResource -Tag $group.Tags -ResourceId $r.ResourceId -Force
        }
    }
}
```

若要移除所有標記，請傳遞空的雜湊表：

```azurepowershell-interactive
Set-AzResourceGroup -Tag @{} -Name examplegroup
```

## <a name="azure-cli"></a>Azure CLI

若要查看*資源群組*的現有標籤，請使用：

```azurecli
az group show -n examplegroup --query tags
```

該指令碼會傳回下列格式︰

```json
{
  "Dept"        : "IT",
  "Environment" : "Test"
}
```

或者，若要查看「具有指定之名稱、類型和資源群組的資源」的現有標籤，請使用：

```azurecli
az resource show -n examplevnet -g examplegroup --resource-type "Microsoft.Network/virtualNetworks" --query tags
```

對資源的集合執行迴圈時，您可能想要依據資源識別碼顯示資源。 本文稍後會示範完整範例。 若要查看「具有指定之資源識別碼的資源」的現有標記，請使用：

```azurecli
az resource show --id <resource-id> --query tags
```

若要取得「具有特定標籤的資源群組」，請使用 `az group list`：

```azurecli
az group list --tag Dept=IT
```

若要取得「具有特定標籤和值的所有資源」，請使用 `az resource list`：

```azurecli
az resource list --tag Dept=Finance
```

每當您將標記套用到資源或資源群組時，即會覆寫該資源或資源群組上現有的標記。 因此，您必須視該資源或資源群組是否具備現有標籤來使用不同的方法。

若要將標籤新增至*不具現有標籤的資源群組*，請使用：

```azurecli
az group update -n examplegroup --set tags.Environment=Test tags.Dept=IT
```

若要將標籤新增至*不具現有標籤的資源*，請使用：

```azurecli
az resource tag --tags Dept=IT Environment=Test -g examplegroup -n examplevnet --resource-type "Microsoft.Network/virtualNetworks"
```

若要將標籤新增至已經具有標籤的資源，請擷取現有的標籤、將該值重新格式化，然後重新套用現有和新的標籤：

```azurecli
jsonrtag=$(az resource show -g examplegroup -n examplevnet --resource-type "Microsoft.Network/virtualNetworks" --query tags)
rt=$(echo $jsonrtag | tr -d '"{},' | sed 's/: /=/g')
az resource tag --tags $rt Project=Redesign -g examplegroup -n examplevnet --resource-type "Microsoft.Network/virtualNetworks"
```

若要將所有標記從資源群組套用至其資源，而*不保留資源上的現有標記*，請使用下列腳本：

```azurecli
groups=$(az group list --query [].name --output tsv)
for rg in $groups
do
  jsontag=$(az group show -n $rg --query tags)
  t=$(echo $jsontag | tr -d '"{},' | sed 's/: /=/g')
  r=$(az resource list -g $rg --query [].id --output tsv)
  for resid in $r
  do
    az resource tag --tags $t --id $resid
  done
done
```

若要將所有標記從資源群組套用至其資源，並*在資源上保留現有的標記*，請使用下列腳本：

```azurecli
groups=$(az group list --query [].name --output tsv)
for rg in $groups
do
  jsontag=$(az group show -n $rg --query tags)
  t=$(echo $jsontag | tr -d '"{},' | sed 's/: /=/g')
  r=$(az resource list -g $rg --query [].id --output tsv)
  for resid in $r
  do
    jsonrtag=$(az resource show --id $resid --query tags)
    rt=$(echo $jsonrtag | tr -d '"{},' | sed 's/: /=/g')
    az resource tag --tags $t$rt --id $resid
  done
done
```

## <a name="templates"></a>範本

若要在部署期間標記資源，請將 `tags` 元素新增至您要部署的資源。 提供標籤名稱和值。

### <a name="apply-a-literal-value-to-the-tag-name"></a>將常值套用至標記名稱

下列範例說明含有兩個標籤 (`Dept` 和 `Environment`) 並設為常值的儲存體帳戶：

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "resources": [
        {
            "apiVersion": "2019-04-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat('storage', uniqueString(resourceGroup().id))]",
            "location": "[parameters('location')]",
            "tags": {
                "Dept": "Finance",
                "Environment": "Production"
            },
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {}
        }
    ]
}
```

若要將標記設定為 datetime 值，請使用[utcNow 函數](resource-group-template-functions-string.md#utcnow)。

### <a name="apply-an-object-to-the-tag-element"></a>將物件套用至標記元素

您可定義存放數個標籤的物件參數，並將該物件套用至標籤元素。 物件中的每個屬性會變成資源的個別標籤。 下列範例具有名為 `tagValues` 且套用至標籤元素的參數。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        },
        "tagValues": {
            "type": "object",
            "defaultValue": {
                "Dept": "Finance",
                "Environment": "Production"
            }
        }
    },
    "resources": [
        {
            "apiVersion": "2019-04-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat('storage', uniqueString(resourceGroup().id))]",
            "location": "[parameters('location')]",
            "tags": "[parameters('tagValues')]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {}
        }
    ]
}
```

### <a name="apply-a-json-string-to-the-tag-name"></a>將 JSON 字串套用至標記名稱

若要將多個值儲存於單一標籤，請套用代表這些值的 JSON 字串。 整個 JSON 字串會儲存成一個不能超過256個字元的標記。 下列範例具有名為 `CostCenter` 的單一標籤，其中包含 JSON 字串中的數個值︰  

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "resources": [
        {
            "apiVersion": "2019-04-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat('storage', uniqueString(resourceGroup().id))]",
            "location": "[parameters('location')]",
            "tags": {
                "CostCenter": "{\"Dept\":\"Finance\",\"Environment\":\"Production\"}"
            },
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {}
        }
    ]
}
```

### <a name="apply-tags-from-resource-group"></a>從資源群組套用標記

若要將標記從資源群組套用至資源，請使用[resourceGroup](resource-group-template-functions-resource.md#resourcegroup)函數。 取得標記值時，請使用 `tags.[tag-name]` 語法，而不是 `tags.tag-name` 語法，因為在點標記法中無法正確剖析某些字元。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "resources": [
        {
            "apiVersion": "2019-04-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat('storage', uniqueString(resourceGroup().id))]",
            "location": "[parameters('location')]",
            "tags": {
                "Dept": "[resourceGroup().tags['Dept']]",
                "Environment": "[resourceGroup().tags['Environment']]"
            },
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {}
        }
    ]
}
```

## <a name="portal"></a>入口網站

[!INCLUDE [resource-manager-tag-resource](../../includes/resource-manager-tag-resources.md)]

## <a name="rest-api"></a>REST API

Azure 入口網站和 PowerShell 在幕後都使用 [Resource Manager REST API](https://docs.microsoft.com/rest/api/resources/) 。 如果您需要將標籤整合到另一個環境中，則可以在資源識別碼上利用 **GET** 來取得標籤，並使用 **PATCH** 呼叫來更新一組標籤。

## <a name="tags-and-billing"></a>標記與計費

您可以使用標籤將您的計費資料分組。 例如，如果您針對不同組織執行多個 VM，請使用標籤依成本中心將使用量分組。 您也可以使用標籤來根據執行階段環境將成本分類。例如，在生產環境中執行之 VM 的計費使用量。

您可以透過 [Azure 資源使用狀況和 RateCard API](../billing/billing-usage-rate-card-overview.md) 或使用狀況逗號分隔值 (CSV) 檔案，擷取關於標記的資訊。 您可以從 [Azure 帳戶中心](https://account.azure.com/Subscriptions)或 Azure 入口網站下載使用量檔案。 如需詳細資訊，請參閱[下載或檢視您的 Azure 帳單發票和每日使用量資料](../billing/billing-download-azure-invoice-daily-usage-date.md)。 從「Azure 帳戶中心」下載使用量檔案時，請選取 [版本 2]。 針對支援在計費方面使用標籤的服務，標籤會顯示在 [標籤] 資料行中。

若為 REST API 作業，請參閱 [Azure 計費 REST API 參考](/rest/api/billing/)。

## <a name="next-steps"></a>後續步驟

* 並非所有資源類型都支援標記。 若要判斷您是否可以將標記套用至資源類型，請參閱 [Azure 資源的標記支援](tag-support.md)。
* 如需使用入口網站的簡介，請參閱[使用 Azure 入口網站來管理您的 Azure 資源](manage-resource-groups-portal.md)。  
