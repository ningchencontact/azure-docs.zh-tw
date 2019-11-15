---
title: 在 Azure 監視器中移動 Log Analytics 工作區 |Microsoft Docs
description: 瞭解如何將您的 Log Analytics 工作區移至另一個訂用帳戶或資源群組。
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/13/2019
ms.openlocfilehash: fd7ff7aa2275defba57aa24b5ef0b9adc78a5355
ms.sourcegitcommit: a170b69b592e6e7e5cc816dabc0246f97897cb0c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/14/2019
ms.locfileid: "74093783"
---
# <a name="move-a-log-analytics-workspace-to-different-subscription-or-resource-group"></a>將 Log Analytics 工作區移至不同的訂用帳戶或資源群組

在本文中，您將瞭解將 Log Analytics 工作區移至相同區域中的另一個資源群組或訂用帳戶的步驟。 您可以深入瞭解如何透過 Azure 入口網站、PowerShell、Azure CLI 或 REST API 來移動 Azure 資源。 [將資源移至新的資源群組或訂用](../../azure-resource-manager/resource-group-move-resources.md)帳戶。 

> [!IMPORTANT]
> 您無法將工作區移至不同的區域。

## <a name="verify-active-directory-tenant"></a>確認 Active Directory 租使用者
工作區來源和目的地訂用帳戶必須存在於相同的 Azure Active Directory 租使用者內。 使用 Azure PowerShell 來確認兩個訂用帳戶都具有相同的租使用者識別碼。

``` PowerShell
(Get-AzSubscription -SubscriptionName <your-source-subscription>).TenantId
(Get-AzSubscription -SubscriptionName <your-destination-subscription>).TenantId
```

## <a name="remove-solutions"></a>移除解決方案
已安裝在工作區中的受控解決方案將會隨著 Log Analytics 工作區移動作業一起移動。 不過，因為您必須從工作區移除連結到任何自動化帳戶，所以必須移除依賴該連結的解決方案。

必須移除的解決方案包括下列各項： 

- 更新管理
- 變更追蹤
- 於下班時間開始/停止 VM


### <a name="azure-portal"></a>Azure 入口網站
請使用下列程式，使用 Azure 入口網站移除解決方案：

1. 針對已安裝任何解決方案的資源群組開啟功能表。
2. 選取要移除的解決方案。
3. 按一下 [**刪除資源**]，然後按一下 [**刪除**] 確認要移除的資源。

![刪除解決方案](media/move-workspace/delete-solutions.png)

### <a name="powershell"></a>PowerShell

若要使用 PowerShell 移除解決方案，請使用[get-azresource](/powershell/module/az.resources/remove-azresource?view=azps-2.8.0) Cmdlet，如下列範例所示：

``` PowerShell
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "ChangeTracking(<workspace-name>)" -ResourceGroupName <resource-group-name>
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Updates(<workspace-name>)" -ResourceGroupName <resource-group-name>
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Start-Stop-VM(<workspace-name>)" -ResourceGroupName <resource-group-name>
```

## <a name="remove-alert-rules"></a>移除警示規則
對於**啟動/停止 vm**解決方案，您也必須移除解決方案所建立的警示規則。 請在 Azure 入口網站中使用下列程式來移除這些規則。

1. 開啟 [**監視**] 功能表，然後選取 [**警示**]。
2. 按一下 [**管理警示規則**]。
3. 選取下列三個警示規則，然後按一下 [**刪除**]。

   - AutoStop_VM_Child
   - ScheduledStartStop_Parent
   - SequencedStartStop_Parent

    ![刪除規則](media/move-workspace/delete-rules.png)

## <a name="unlink-automation-account"></a>取消連結自動化帳戶
使用下列程式，使用 Azure 入口網站將自動化帳戶從工作區取消連結：

1. 開啟 [**自動化帳戶**] 功能表，然後選取要移除的帳戶。
2. 在功能表的 [**相關資源**] 區段中，選取 [**連結的工作區**]。 
3. 按一下 [**取消連結工作區**]，將工作區從您的自動化帳戶取消連結。

    ![取消連結工作區](media/move-workspace/unlink-workspace.png)

## <a name="move-your-workspace"></a>移動您的工作區

### <a name="azure-portal"></a>Azure 入口網站
請使用下列程式，利用 Azure 入口網站來移動工作區：

1. 開啟 [ **Log Analytics 工作區**] 功能表，然後選取您的工作區。
2. 在 [**總覽**] 頁面中，按一下 [**資源群組**或**訂**用帳戶] 旁的 [**變更**]。
3. 隨即會開啟新的頁面，其中會列出與工作區相關的資源清單。 選取資源，以移至與工作區相同的目的地訂用帳戶和資源群組。 
4. 選取目的地**訂**用帳戶和**資源群組**。 如果您要將工作區移至相同訂用帳戶中的另一個資源群組，您將不會看到**訂**用帳戶選項。
5. 按一下 **[確定]** 以移動工作區和選取的資源。

    ![入口網站](media/move-workspace/portal.png)

### <a name="powershell"></a>PowerShell
若要使用 PowerShell 移動您的工作區，請使用[get-azresource](/powershell/module/AzureRM.Resources/Move-AzureRmResource) ，如下列範例所示：

``` PowerShell
Move-AzResource -ResourceId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MyResourceGroup01/providers/Microsoft.OperationalInsights/workspaces/MyWorkspace" -DestinationSubscriptionId "00000000-0000-0000-0000-000000000000" -DestinationResourceGroupName "MyResourceGroup02"
```



> [!IMPORTANT]
> 移動作業之後，應該重新設定已移除的解決方案和自動化帳戶連結，讓工作區回到先前的狀態。


## <a name="next-steps"></a>後續步驟
- 如需哪些資源支援移動的清單，請參閱[資源的移動作業支援](../../azure-resource-manager/move-support-resources.md)。
