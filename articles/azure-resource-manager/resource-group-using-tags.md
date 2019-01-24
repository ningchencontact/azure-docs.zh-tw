---
title: 針對邏輯組織標記 Azure 資源 | Microsoft Docs
description: 示範如何套用標籤以針對計費及管理來組織 Azure 資源。
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 003a78e5-2ff8-4685-93b4-e94d6fb8ed5b
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: AzurePortal
ms.devlang: na
ms.topic: conceptual
ms.date: 11/20/2018
ms.author: tomfitz
ms.openlocfilehash: 27ba79e9168e098717e91e5a7179b5bc419ef86c
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/22/2019
ms.locfileid: "54438400"
---
# <a name="use-tags-to-organize-your-azure-resources"></a>使用標記來組織 Azure 資源

[!INCLUDE [resource-manager-governance-tags](../../includes/resource-manager-governance-tags.md)]

若要將標記套用到資源，使用者必須具有該資源類型的寫入權限。 若要將標記套用到所有資源類型，請使用[參與者](../role-based-access-control/built-in-roles.md#contributor)角色。 若只要將標記套用到一個資源類型，則使用適用於該資源的參與者角色。 例如，若要將標記套用到虛擬機器，可使用[虛擬機器參與者](../role-based-access-control/built-in-roles.md#virtual-machine-contributor)。

[!INCLUDE [Handle personal data](../../includes/gdpr-intro-sentence.md)]

## <a name="powershell"></a>PowerShell

本文的範例需要 Azure PowerShell 6.0 版或更新版本。 如果您的版本不是 6.0 版或更新版本，請[更新您的版本](/powershell/azure/azurerm/install-azurerm-ps)。

若要查看*資源群組*的現有標籤，請使用：

```azurepowershell-interactive
(Get-AzureRmResourceGroup -Name examplegroup).Tags
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
(Get-AzureRmResource -ResourceId /subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Storage/storageAccounts/<storage-name>).Tags
```

或者，若要查看「具有指定之名稱和資源群組的資源」的現有標記，請使用：

```azurepowershell-interactive
(Get-AzureRmResource -ResourceName examplevnet -ResourceGroupName examplegroup).Tags
```

若要取得「具有特定標記的資源群組」，請使用：

```azurepowershell-interactive
(Get-AzureRmResourceGroup -Tag @{ Dept="Finance" }).ResourceGroupName
```

若要取得「具有特定標記的資源」，請使用：

```azurepowershell-interactive
(Get-AzureRmResource -Tag @{ Dept="Finance"}).Name
```

若要取得「具有特定標記名稱的資源」，請使用：

```azurepowershell-interactive
(Get-AzureRmResource -TagName Dept).Name
```

每當您將標記套用到資源或資源群組時，即會覆寫該資源或資源群組上現有的標記。 因此，您必須視該資源或資源群組是否具備現有標籤來使用不同的方法。

若要將標籤新增至*不具現有標籤的資源群組*，請使用：

```azurepowershell-interactive
Set-AzureRmResourceGroup -Name examplegroup -Tag @{ Dept="IT"; Environment="Test" }
```

若要將標記新增至「具有現有標記的資源群組」，請擷取現有標記、新增標記，然後重新套用標記：

```azurepowershell-interactive
$tags = (Get-AzureRmResourceGroup -Name examplegroup).Tags
$tags.Add("Status", "Approved")
Set-AzureRmResourceGroup -Tag $tags -Name examplegroup
```

若要將標籤新增至*不具現有標籤的資源*，請使用：

```azurepowershell-interactive
$r = Get-AzureRmResource -ResourceName examplevnet -ResourceGroupName examplegroup
Set-AzureRmResource -Tag @{ Dept="IT"; Environment="Test" } -ResourceId $r.ResourceId -Force
```

若要將標記新增至「沒有現有標記的資源」，請使用：

```azurepowershell-interactive
$r = Get-AzureRmResource -ResourceName examplevnet -ResourceGroupName examplegroup
$r.Tags.Add("Status", "Approved")
Set-AzureRmResource -Tag $r.Tags -ResourceId $r.ResourceId -Force
```

若要將所有標籤從資源群組套用至其資源，而*不在資源上保留現有的標籤*，請使用下列指令碼︰

```azurepowershell-interactive
$groups = Get-AzureRmResourceGroup
foreach ($g in $groups)
{
    Get-AzureRmResource -ResourceGroupName $g.ResourceGroupName | ForEach-Object {Set-AzureRmResource -ResourceId $_.ResourceId -Tag $g.Tags -Force }
}
```

若要將所有標籤從資源群組套用至其資源，並*在資源上保留所有非重複的現有標籤*，請使用下列指令碼︰

```azurepowershell-interactive
$group = Get-AzureRmResourceGroup "examplegroup"
if ($null -ne $group.Tags) {
    $resources = Get-AzureRmResource -ResourceGroupName $group.ResourceGroupName
    foreach ($r in $resources)
    {
        $resourcetags = (Get-AzureRmResource -ResourceId $r.ResourceId).Tags
        if ($resourcetags)
        {
            foreach ($key in $group.Tags.Keys)
            {
                if (-not($resourcetags.ContainsKey($key)))
                {
                    $resourcetags.Add($key, $group.Tags[$key])
                }
            }
            Set-AzureRmResource -Tag $resourcetags -ResourceId $r.ResourceId -Force
        }
        else
        {
            Set-AzureRmResource -Tag $group.Tags -ResourceId $r.ResourceId -Force
        }
    }
}
```

若要移除所有標記，請傳遞空的雜湊表：

```azurepowershell-interactive
Set-AzureRmResourceGroup -Tag @{} -Name examplegroup
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

若要將所有標籤從資源群組套用至其資源，而*不在資源上保留現有的標籤*，請使用下列指令碼︰

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

若要將所有標籤從資源群組套用至其資源，並*在資源上保留現有的標籤*，請使用下列指令碼：

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

[!INCLUDE [resource-manager-tags-in-templates](../../includes/resource-manager-tags-in-templates.md)]

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
* 您可以使用自訂原則，在訂用帳戶內套用限制和慣例。 您所定義的原則可能需要所有資源都具有特定標籤的值。 如需詳細資訊，請參閱[何謂 Azure 原則？](../azure-policy/azure-policy-introduction.md)
* 如需使用入口網站的簡介，請參閱[使用 Azure 入口網站來管理您的 Azure 資源](resource-group-portal.md)。  
