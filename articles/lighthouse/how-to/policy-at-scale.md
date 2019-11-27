---
title: 大規模地將 Azure 原則部署至委派的訂閱
description: 瞭解 Azure 委派的資源管理如何讓您跨多個租使用者部署原則定義和原則指派。
ms.date: 11/8/2019
ms.topic: conceptual
ms.openlocfilehash: 3853e8fc163dfc662adc675dd3df1d15958d329a
ms.sourcegitcommit: 95931aa19a9a2f208dedc9733b22c4cdff38addc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2019
ms.locfileid: "74463863"
---
# <a name="deploy-azure-policy-to-delegated-subscriptions-at-scale"></a>大規模地將 Azure 原則部署至委派的訂閱

身為服務提供者，您可能已上架多個客戶租使用者以進行 Azure 委派的資源管理。 [Azure 燈塔](../overview.md)可讓服務提供者一次在多個租使用者上執行大規模作業，讓管理工作更有效率。

本主題說明如何使用[Azure 原則](https://docs.microsoft.com/azure/governance/policy/)，透過 PowerShell 命令來部署跨多個租使用者的原則定義和原則指派。 在此範例中，原則定義可確保只允許 HTTPS 流量來保護儲存體帳戶。

## <a name="use-azure-resource-graph-to-query-across-customer-tenants"></a>使用 Azure Resource Graph 在客戶租使用者之間進行查詢

您可以使用[Azure Resource Graph](https://docs.microsoft.com/azure/governance/resource-graph/)來查詢您所管理之客戶租使用者中的所有訂用帳戶。 在此範例中，我們將識別這些訂用帳戶中目前不需要 HTTPS 流量的任何儲存體帳戶。  

```powershell
$MspTenant = "insert your managing tenantId here"

$subs = Get-AzSubscription

$ManagedSubscriptions = Search-AzGraph -Query "ResourceContainers | where type == 'microsoft.resources/subscriptions' | where tenantId != '$($mspTenant)' | project name, subscriptionId, tenantId" -subscription $subs.subscriptionId

Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Storage/storageAccounts' | project name, location, subscriptionId, tenantId, properties.supportsHttpsTrafficOnly" -subscription $ManagedSubscriptions.subscriptionId | convertto-json
```

## <a name="deploy-a-policy-across-multiple-customer-tenants"></a>跨多個客戶租使用者部署原則

下列範例示範如何使用[Azure Resource Manager 範本](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/policy-enforce-https-storage/enforceHttpsStorage.json)，在多個客戶租使用者中部署跨委派訂用帳戶的原則定義和原則指派。 此原則定義會要求所有儲存體帳戶都使用 HTTPS 流量，以防止在沒有設定為不符合規範的情況下，建立不符合並標示現有儲存體帳戶的任何新儲存體帳戶。

```powershell
Write-Output "In total, there are $($ManagedSubscriptions.Count) delegated customer subscriptions to be managed"

foreach ($ManagedSub in $ManagedSubscriptions)
{
    Select-AzSubscription -SubscriptionId $ManagedSub.subscriptionId

    New-AzDeployment -Name mgmt `
                     -Location eastus `
                     -TemplateUri "https://raw.githubusercontent.com/Azure/Azure-Lighthouse-samples/master/Azure-Delegated-Resource-Management/templates/policy-enforce-https-storage/enforceHttpsStorage.json" `
                     -AsJob
}
```

## <a name="validate-the-policy-deployment"></a>驗證原則部署

部署 Azure Resource Manager 範本之後，您可以嘗試在其中一個委派的訂用帳戶中建立**enableHTTPstrafficonly 屬性**設定為**false**的儲存體帳戶，以確認已成功套用原則定義。 因為原則指派的緣故，所以您應該無法建立此儲存體帳戶。  

```powershell
New-AzStorageAccount -ResourceGroupName (New-AzResourceGroup -name policy-test -Location eastus -Force).ResourceGroupName `
                     -Name (get-random) `
                     -Location eastus `
                     -EnableHttpsTrafficOnly $false `
                     -SkuName Standard_LRS `
                     -Verbose                  
```

## <a name="clean-up-resources"></a>清除資源

當您完成時，請移除部署所建立的原則定義和指派。

```powershell
foreach ($ManagedSub in $ManagedSubscriptions)
{
    select-azsubscription -subscriptionId $ManagedSub.subscriptionId

    Remove-AzDeployment -Name mgmt -AsJob

    $Assignment = Get-AzPolicyAssignment | where-object {$_.Name -like "enforce-https-storage-assignment"}

    if ([string]::IsNullOrEmpty($Assignment))
    {
        Write-Output "Nothing to clean up - we're done"
    }
    else
    {

    Remove-AzPolicyAssignment -Name 'enforce-https-storage-assignment' -Scope "/subscriptions/$($ManagedSub.subscriptionId)" -Verbose

    Write-Output "Deployment has been deleted - we're done"
    }
}
```

## <a name="next-steps"></a>後續步驟

- 了解 [Azure 原則](https://docs.microsoft.com/azure/governance/policy/)。
- 了解[跨租用戶管理體驗](../concepts/cross-tenant-management-experience.md)。
