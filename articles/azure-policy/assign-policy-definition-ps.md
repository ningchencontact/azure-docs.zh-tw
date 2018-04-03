---
title: 快速入門 - 使用 PowerShell 建立原則指派，以識別 Azure 環境中不符合規範的資源 | Microsoft Docs
description: 在本快速入門中，您會使用 PowerShell 建立 Azure 原則指派，以識別不符合規範的資源。
services: azure-policy
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 3/14/2018
ms.topic: quickstart
ms.service: azure-policy
ms.custom: mvc
ms.openlocfilehash: 45c5ccd0f891a5592eee7400de108c5097f75286
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/23/2018
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-using-the-azure-rm-powershell-module"></a>快速入門：使用 Azure RM PowerShell 模組建立原則指派，以識別不符合規範的資源

了解 Azure 中合規性的第一個步驟是識別您資源的狀態。 在本快速入門中，您會建立原則指派，以識別未使用受控磁碟的虛擬機器。 完成時，您會識別出「不符合」原則指派的虛擬機器。

AzureRM PowerShell 模組用於從命令列或在指令碼中建立和管理 Azure 資源。 本指南說明如何使用 AzureRM 建立原則指派。 此原則可識別 Azure 環境中不符合規範的資源。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/) 。

## <a name="prerequisites"></a>先決條件

- 開始之前，請確定已安裝最新版 PowerShell。 如需詳細資訊，請參閱[如何安裝和設定 Azure PowerShell](/powershell/azureps-cmdlets-docs)。
- 將您的 AzureRM PowerShell 模組更新為最新版本。 如果您需要安裝或升級，請參閱[安裝 Azure PowerShell 模組](/powershell/azure/install-azurerm-ps)。

## <a name="create-a-policy-assignment"></a>建立原則指派

在本快速入門中，您會建立一個原則指派，並且指派 Audit Virtual Machines without Managed Disks (稽核沒有受控磁碟的虛擬機器) 定義。 此原則定義會識別與原則定義中設定之條件不相符的資源。

執行下列命令以建立新的原則指派：

```powershell
$rg = Get-AzureRmResourceGroup -Name "<resourceGroupName>"

$definition = Get-AzureRmPolicyDefinition -Name "Audit Virtual Machines without Managed Disks"

New-AzureRMPolicyAssignment -Name Audit Virtual Machines without Managed Disks Assignment -Scope $rg.ResourceId -PolicyDefinition $definition -Sku @{Name='A1';Tier='Standard'}

```

上述命令會使用下列資訊：

- **名稱** - 原則指派的顯示名稱。 在此案例中，您會使用 Audit Virtual Machines without Managed Disks (稽核沒有受控磁碟指派的虛擬機器)。
- **定義** – 原則定義，這是您用來建立指派的根基。 在此案例中，即為原則定義 – *Audit Virtual Machines without Managed Disks* (稽核沒有受控磁碟的虛擬機器)。
- **範圍** – 範圍會決定在哪些資源或資源群組上強制執行原則指派。 範圍從訂用帳戶到資源群組。 請務必將 &lt;scope&gt; 取代為您的資源群組。
- **Sku** – 此命令會建立具有標準層的原則指派。 標準層可讓您達到大規模管理、合規性評估和補救。 如需定價層的其他詳細資訊，請參閱 [Azure 原則定價](https://azure.microsoft.com/pricing/details/azure-policy)。


您現在可以識別不相容的資源，以了解環境的相容性狀態。

## <a name="identify-non-compliant-resources"></a>識別不相容的資源

使用下列資訊來識別不符合您所建立之原則指派的資源。 執行下列命令：

```powershell
$policyAssignment = Get-AzureRmPolicyAssignment | where {$_.properties.displayName -eq "Audit Virtual Machines without Managed Disks"}
```

```powershell
$policyAssignment.PolicyAssignmentId
```

如需有關原則指派識別碼的詳細資訊，請參閱 [Get-AzureRMPolicyAssignment](/powershell/module/azurerm.resources/get-azurermpolicyassignment)。

接下來，執行下列命令，以取得不合規資源的資源識別碼，而這些識別碼會輸出到 JSON 檔案中：

```powershell
armclient post "/subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2017-12-12-preview&$filter=IsCompliant eq false and PolicyAssignmentId eq '<policyAssignmentID>'&$apply=groupby((ResourceId))" > <json file to direct the output with the resource IDs into>
```
您的結果類似下列範例：


```
{
"@odata.context":"https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest",
"@odata.count": 3,
"value": [
{
    "@odata.id": null,
    "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
      "ResourceId": "/subscriptions/<subscriptionId>/resourcegroups/<rgname>/providers/microsoft.compute/virtualmachines/<virtualmachineId>"
    },
    {
      "@odata.id": null,
      "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
      "ResourceId": "/subscriptions/<subscriptionId>/resourcegroups/<rgname>/providers/microsoft.compute/virtualmachines/<virtualmachine2Id>"
         },
{
      "@odata.id": null,
      "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
      "ResourceId": "/subscriptions/<subscriptionName>/resourcegroups/<rgname>/providers/microsoft.compute/virtualmachines/<virtualmachine3ID>"
         }

]
}
```

結果類似於您通常在 Azure 入口網站檢視中看到列在 [不符合規範的資源] 之下的內容。


## <a name="clean-up-resources"></a>清除資源

此集合中的後續指南是以本快速入門為基礎。 如果您打算繼續進行其他教學課程，請勿清除在此快速入門中建立的資源。 如果您不打算繼續，您可藉由執行以下命令來刪除您所建立的指派：

```powershell
Remove-AzureRmPolicyAssignment -Name "Audit Virtual Machines without Managed Disks Assignment" -Scope /subscriptions/<subscriptionID>/<resourceGroupName>
```

## <a name="next-steps"></a>後續步驟

在這個快速入門中，您指派原則定義以識別 Azure 環境中的不相容資源。

若要深入了解如何指派原則並確保所建立的**未來**資源是符合規範，請繼續進行教學課程：

> [!div class="nextstepaction"]
> [建立及管理原則](./create-manage-policy.md)
