---
title: 將您的 Azure 自動化帳戶移至另一個訂用帳戶
description: 本文說明如何將您的自動化帳戶移至另一個訂用帳戶
services: automation
ms.service: automation
ms.subservice: process-automation
author: bobbytreed
ms.author: robreed
ms.date: 03/11/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 8187e4c6f2c7dc721c178bad50b6c3ada2a65367
ms.sourcegitcommit: a52f17307cc36640426dac20b92136a163c799d0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/01/2019
ms.locfileid: "68717234"
---
# <a name="move-your-azure-automation-account-to-another-subscription"></a>將您的 Azure 自動化帳戶移至另一個訂用帳戶

Azure 讓您能夠將一些資源移到新的資源群組或訂用帳戶。 您可以透過 Azure 入口網站、PowerShell、Azure CLI 或 REST API 來移動資源。 若要深入瞭解此程式, 請參閱[將資源移至新的資源群組或訂用](../../azure-resource-manager/resource-group-move-resources.md)帳戶。

Azure 自動化帳戶是可以移動的其中一個資源。 在本文中, 您將瞭解將自動化帳戶移至另一個資源或訂用帳戶的步驟。

移動自動化帳戶的高階步驟如下:

1. 移除您的解決方案。
2. 取消連結您的工作區。
3. 移動自動化帳戶。
4. 刪除並重新建立執行身分帳戶。
5. 重新啟用您的解決方案。

## <a name="remove-solutions"></a>移除解決方案

若要取消您的工作區與自動化帳戶的連結, 必須從您的工作區中移除這些解決方案:
- **變更追蹤和清查**
- **更新管理**
- **于下班時間啟動/停止 Vm**

在您的資源群組中, 尋找每個解決方案, 然後選取 [**刪除**]。 在 [**刪除資源**] 頁面上, 確認要移除的資源, 然後選取 [**刪除**]。

![從 Azure 入口網站刪除解決方案](../media/move-account/delete-solutions.png)

您可以使用[get-azurermresource](/powershell/module/azurerm.resources/remove-azurermresource) Cmdlet 來完成相同的工作, 如下列範例所示:

```azurepowershell-interactive
$workspaceName = <myWorkspaceName>
$resourceGroupName = <myResourceGroup>
Remove-AzureRmResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "ChangeTracking($workspaceName)" -ResourceGroupName $resourceGroupName
Remove-AzureRmResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Updates($workspaceName)" -ResourceGroupName $resourceGroupName
Remove-AzureRmResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Start-Stop-VM($workspaceName)" -ResourceGroupName $resourceGroupName
```

### <a name="additional-steps-for-startstop-vms"></a>啟動/停止 Vm 的其他步驟

對於**啟動/停止 vm**解決方案, 您也必須移除解決方案所建立的警示規則。

在 Azure 入口網站中, 移至您的資源群組, 然後選取 **監視** >  **警示** >  **管理警示規則**。

![顯示管理警示規則選取範圍的警示頁面](../media/move-account/alert-rules.png)

在 [**規則**] 頁面上, 您應該會看到該資源群組中所設定的警示清單。 **啟動/停止 vm**解決方案會建立三個警示規則:

* AutoStop_VM_Child
* ScheduledStartStop_Parent
* SequencedStartStop_Parent

選取這三個警示規則, 然後選取 [**刪除**]。 此動作將會移除這些警示規則。

![要求確認刪除所選規則的規則頁面](../media/move-account/delete-rules.png)

> [!NOTE]
> 如果您在 [**規則**] 頁面上看不到任何警示規則, 請將**狀態**變更為 [顯示**已停用**的警示], 因為您可能已停用它們。

移除警示規則時, 請移除為 [**啟動/停止 vm** ] 解決方案通知所建立的動作群組。

在 Azure 入口網站中, 選取 **監視** >  **警示** >  **管理動作群組**。

選取 **StartStop_VM_Notification**從清單中。 在 動作群組 頁面中，選取**刪除**。

![動作群組 頁面上，選取 刪除](../media/move-account/delete-action-group.png)

同樣地，您可以使用 PowerShell 搭配刪除動作群組 [Remove-AzureRmActionGroup](/powershell/module/azurerm.insights/remove-azurermactiongroup) cmdlet，如下列範例所示：

```azurepowershell-interactive
Remove-AzureRmActionGroup -ResourceGroupName <myResourceGroup> -Name StartStop_VM_Notification
```

## <a name="unlink-your-workspace"></a>取消連結您的工作區

在 Azure 入口網站中, 選取 **自動化帳戶** > **相關資源** >  **連結的工作區**。 選取 [**取消連結工作區**], 將工作區從您的自動化帳戶取消連結。

![將工作區與自動化帳戶取消連結](../media/move-account/unlink-workspace.png)

## <a name="move-your-automation-account"></a>移動您的自動化帳戶

移除先前的專案之後, 您可以繼續移除您的自動化帳戶及其 runbook。 在 Azure 入口網站中, 流覽至您的自動化帳戶的資源群組。 選取 [**移** > **至另一個訂**用帳戶]。

![[資源群組] 頁面, 移至另一個訂用帳戶](../media/move-account/move-resources.png)

選取您想要移動的資源群組中的資源。 請確定您包含**自動化帳戶**、 **Runbook**和**Log Analytics 工作區**資源。

移動完成後, 需要進行其他步驟, 才能讓所有專案都能正常執行。

## <a name="re-create-run-as-accounts"></a>重新建立執行身分帳戶

[執行身分帳戶](../manage-runas-account.md)會在 Azure Active Directory 中建立服務主體, 以向 Azure 資源進行驗證。 當您變更訂閱時, 自動化帳戶不再使用現有的執行身分帳戶。

前往新訂用帳戶中的自動化帳戶, 然後在 [**帳戶設定**] 底下選取 [**執行身分帳戶**]。 您會看到 [執行身分帳戶] 現在顯示為 [未完成]。

![執行身分帳戶不完整](../media/move-account/run-as-accounts.png)

選取每個執行身分帳戶。 在 [**屬性**] 頁面上, 選取 [**刪除**] 以刪除執行身分帳戶。

> [!NOTE]
> 如果您沒有建立或查看執行身分帳戶的許可權, 您會看到下列訊息:`You do not have permissions to create an Azure Run As account (service principal) and grant the Contributor role to the service principal.`若要瞭解設定執行身分帳戶所需的許可權, 請參閱[設定執行身分帳戶所需的許可權](../manage-runas-account.md#permissions)。

刪除執行身分帳戶之後, 請選取 [ **Azure 執行身分帳戶**] 底下的 [**建立**]。 在 [**新增 Azure 執行身分帳戶**] 頁面上, 選取 [**建立**] 以建立執行身分帳戶和服務主體。 使用**Azure 傳統執行身分帳戶**重複上述步驟。

## <a name="enable-solutions"></a>啟用解決方案

重新建立執行身分帳戶之後, 您會重新啟用在移動前移除的解決方案。 若要開啟**變更追蹤和清查**和**更新管理**, 請在您的自動化帳戶中選取各自的功能。 選擇您所移動的 Log Analytics 工作區, 然後選取 [**啟用**]。

![在移動的自動化帳戶中重新啟用解決方案](../media/move-account/reenable-solutions.png)

當您已連線到現有的 Log Analytics 工作區時, 將會顯示使用您的解決方案所上架的機器。

若要開啟 [在離峰期間**啟動/停止 vm** ] 解決方案, 您必須重新部署解決方案。 在 [**相關資源**] 底下, 選取 [**啟動/停止 vm**  > ] **[深入瞭解], 並啟用解決方案** > [**建立**] 以開始部署。

在 [**新增解決方案**] 頁面上, 選擇您的 Log Analytics 工作區和自動化帳戶。

![[新增方案] 功能表](../media/move-account/add-solution-vm.png)

如需設定解決方案的詳細指示, 請參閱 Azure 自動化中的在[離峰期間啟動/停止 vm 解決方案](../automation-solution-vm-management.md)。

## <a name="post-move-verification"></a>移動後驗證

當移動完成時, 請檢查下列應驗證的工作清單:

|功能|測試|疑難排解連結|
|---|---|---|
|runbook|Runbook 可以成功執行並聯機至 Azure 資源。|[對 Runbook 進行疑難排解](../troubleshoot/runbooks.md)
|原始檔控制|您可以在原始檔控制存放庫上執行手動同步處理。|[原始檔控制整合](../source-control-integration.md)|
|變更追蹤和清查|確認您看到來自電腦的目前清查資料。|[針對變更追蹤進行疑難排解](../troubleshoot/change-tracking.md)|
|更新管理|確認您看到您的電腦, 且其狀況良好。</br>執行測試軟體更新部署。|[針對更新管理進行疑難排解](../troubleshoot/update-management.md)|
|共用資源|確認您看到所有共用的資源, 例如[認證](../shared-resources/credentials.md)、[變數](../shared-resources/variables.md)等。|

## <a name="next-steps"></a>後續步驟

若要深入瞭解如何在 Azure 中移動資源, 請參閱[在 azure 中移動資源](../../azure-resource-manager/move-support-resources.md)。
