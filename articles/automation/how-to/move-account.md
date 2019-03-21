---
title: 將您的 Azure 自動化帳戶移至另一個訂用帳戶
description: 本文說明如何將您的自動化帳戶移至另一個訂用帳戶
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/11/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 59d433bfb888eaa41cc8f66bdf3ad28c16efbe5c
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/20/2019
ms.locfileid: "58225955"
---
# <a name="move-your-azure-automation-account-to-another-subscription"></a>將您的 Azure 自動化帳戶移至另一個訂用帳戶

Azure 會提供將某些資源移至新的資源群組或訂用帳戶的能力。 您可以將透過 Azure 入口網站、 PowerShell、 Azure CLI 或 REST API 的資源移動。 若要深入了解此程序，請參閱[將資源移動到新的資源群組或訂用帳戶](../../azure-resource-manager/resource-group-move-resources.md)。 

Azure 自動化帳戶可以是其中一個可以移動的資源。 在本文中，您將學習如何將自動化帳戶移至另一個資源或訂用帳戶。

若要移動您的自動化帳戶的高階步驟如下：

1. 移除您的解決方案。
2. 取消連結您的工作區。
3. 移動自動化帳戶。
4. 刪除並重新建立執行身分帳戶。
5. 重新啟用您的解決方案。

## <a name="remove-solutions"></a>移除解決方案

若要取消連結您的工作區，從您的自動化帳戶，必須從您的工作區中移除這些解決方案：
- **變更追蹤和清查**
- **更新管理** 
- **停機期間啟動/停止 Vm** 

在資源群組中，尋找每個解決方案並選取**刪除**。 在 **刪除資源**頁面上，確認要移除，然後選取的資源**刪除**。

![從 Azure 入口網站中刪除解決方案](../media/move-account/delete-solutions.png)

您可以完成相同的工作與[Remove-azurermresource](/powershell/module/azurerm.resources/remove-azurermresource) cmdlet，在下列範例所示：

```azurepowershell-interactive
$workspaceName = <myWorkspaceName>
$resourceGroupName = <myResourceGroup>
Remove-AzureRmResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "ChangeTracking($workspaceName)" -ResourceGroupName $resourceGroupName
Remove-AzureRmResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Updates($workspaceName)" -ResourceGroupName $resourceGroupName
Remove-AzureRmResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Start-Stop-VM($workspaceName)" -ResourceGroupName $resourceGroupName
```

### <a name="additional-steps-for-startstop-vms"></a>其他步驟，才能啟動/停止 Vm

針對**啟動/停止 Vm**解決方案中，您也必須移除解決方案所建立的警示規則。

在 Azure 入口網站中，移至您的資源群組，然後選取**監視** > **警示** > **管理警示規則**。

![警示頁面顯示選取的管理警示規則](../media/move-account/alert-rules.png)

在 [**規則**] 頁面上，您應該會看到該資源群組中設定警示的清單。 **啟動/停止 Vm**解決方案也會建立三個警示規則：

* AutoStop_VM_Child
* ScheduledStartStop_Parent
* SequencedStartStop_Parent

選取這三個警示規則，然後按**刪除**。 此動作會移除這些警示規則。

![要求確認刪除所選規則的 [規則] 頁面](../media/move-account/delete-rules.png)

> [!NOTE]
> 如果您沒有看到任何警示規則上**規則**頁面上，變更**狀態**以顯示**停用**發出警示通知，因為您可能已停用它們。

當警示規則會遭到移除時，移除針對所建立的動作群組**啟動/停止 Vm**解決方案通知。

在 Azure 入口網站中，選取**監視器** > **警示** > **管理動作群組**。

選取  **StartStop_VM_Notification**從清單中。 在 [動作群組] 頁面中，選取**刪除**。

![動作群組] 頁面上，選取 [刪除](../media/move-account/delete-action-group.png)

同樣地，您可以使用 PowerShell 搭配刪除動作群組[Remove-azurermactiongroup](/powershell/module/azurerm.insights/remove-azurermactiongroup) cmdlet，如下列範例所示：

```azurepowershell-interactive
Remove-AzureRmActionGroup -ResourceGroupName <myResourceGroup> -Name StartStop_VM_Notification
```

## <a name="unlink-your-workspace"></a>取消連結您的工作區

在 Azure 入口網站中，選取**自動化帳戶** > **相關資源** > **連結工作區**。 選取 **取消連結工作區**取消連結您的自動化帳戶的工作區。

![取消連結工作區從自動化帳戶](../media/move-account/unlink-workspace.png)

## <a name="move-your-automation-account"></a>移動您的自動化帳戶

移除之前的項目之後, 您可以繼續移除您的自動化帳戶和其 runbook。 在 Azure 入口網站中，瀏覽至您的自動化帳戶的資源群組。 選取 **移動** > **移至另一個訂用帳戶**。

![資源群組頁面上，移至另一個訂用帳戶](../media/move-account/move-resources.png)

選取您想要移動的資源群組中的資源。 請確定您包含您**自動化帳戶**， **Runbook**，並**Log Analytics 工作區**資源。

移動完成之後，有其他工作的每個項目所需的步驟。

## <a name="re-create-run-as-accounts"></a>重新建立執行身分帳戶

[執行身分帳戶](../manage-runas-account.md)來向 Azure 資源的 Azure Active Directory 中建立服務主體。 當您變更訂用帳戶時，「 自動化 」 帳戶不會再使用現有的執行身分帳戶。

請移至您的自動化帳戶中新的訂用帳戶，然後選取**執行身分帳戶**下方**帳戶設定**。 您會看到，執行身分帳戶顯示未完成現在。

![執行身分帳戶不完整](../media/move-account/run-as-accounts.png)

選取每個執行身分帳戶。 在 **屬性**頁面上，選取**刪除**刪除執行身分帳戶。

> [!NOTE]
> 如果您沒有權限可建立或檢視的執行身分帳戶，您會看到下列訊息：`You do not have permissions to create an Azure Run As account (service principal) and grant the Contributor role to the service principal.` 若要深入了解設定執行身分帳戶所需的權限，請參閱[設定執行身分帳戶所需的權限](../manage-runas-account.md#permissions)。

刪除執行身分帳戶之後，選取**Create**下方**Azure 執行身分帳戶**。 在 **新增 Azure 執行身分帳戶**頁面上，選取**建立**來建立執行身分帳戶和服務主體。 重複使用先前的步驟**Azure 傳統執行身分帳戶**。

## <a name="enable-solutions"></a>啟用解決方案

重新建立執行身分帳戶之後，您將會重新啟用您在移動之前所移除的解決方案。 若要開啟**變更追蹤和清查**並**更新管理**，選取您的自動化帳戶中的個別功能。 選擇 移至 Log Analytics 工作區，然後選取**啟用**。

![重新啟用移動自動化帳戶中的解決方案](../media/move-account/reenable-solutions.png)

當您已連接現有的 Log Analytics 工作區時，將會顯示您的解決方案上架的機器。

若要開啟**啟動/停止 Vm**期間在離峰的解決方案，您將需要重新部署解決方案。 底下**相關的資源**，選取**啟動/停止 Vm** > **進一步了解並啟用此解決方案** > **建立**以開始部署。

在 **將方案加入**頁面上，選擇您的 Log Analytics 工作區和自動化帳戶。  

![新增方案 功能表](../media/move-account/add-solution-vm.png)

如需設定解決方案的詳細指示，請參閱[在 Azure 自動化中的在離峰解決方案期間啟動/停止 Vm](../automation-solution-vm-management.md)。

## <a name="post-move-verification"></a>移動後驗證

當移動完成時，請檢查下列清單必須經過驗證的工作：

|功能|測試|疑難排解連結|
|---|---|---|
|Runbook|Runbook 可以順利執行並連接到 Azure 資源。|[對 Runbook 進行疑難排解](../troubleshoot/runbooks.md)
| 原始檔控制|您可以在您的原始檔控制存放庫上執行手動同步處理。|[原始檔控制整合](../source-control-integration.md)|
|變更追蹤和清查|請確認您看到目前的存貨資料從您的機器。|[疑難排解 變更追蹤](../troubleshoot/change-tracking.md)|
|更新管理|請確認您會看到您的機器，而且它們都狀況良好。</br>執行測試的軟體更新部署。|[更新管理疑難排解](../troubleshoot/update-management.md)|

## <a name="next-steps"></a>後續步驟

若要深入了解在 Azure 中移動資源，請參閱[將資源移入 Azure](../../azure-resource-manager/move-support-resources.md)。
