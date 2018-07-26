---
title: 處理 Azure 中虛擬機器擴展集的維修通知 | Microsoft Docs
description: 檢視 Azure 中虛擬機器擴展集的維修通知，並開始自助式維護。
services: virtual-machine-scale-sets
documentationcenter: ''
author: shants123
editor: ''
tags: azure-service-management,azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/09/2018
ms.author: shants
ms.openlocfilehash: 4ce984686c2bb320d5d32771d31b81cdec1153af
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2018
ms.locfileid: "38506255"
---
# <a name="handling-planned-maintenance-notifications-for-virtual-machine-scale-sets"></a>處理虛擬機器擴展集的計劃性維護通知

為提升虛擬機器之主機基礎結構的可靠性、效能和安全性，Azure 會定期執行更新。 更新會變更，例如裝載環境的修補或將硬體升級與解除委任。 這些更新大多數都會在不影響託管虛擬機器的情況下執行。 不過，更新在某些情況下確實會造成影響：

- 如果維護不需要重新開機，Azure 會在主機更新時使用就地移轉來暫停 VM。 這些不需要重新開機的維護作業會逐個容錯網域套用，而且如果收到任何警告健康情況訊號，進度就會停止。

- 如果維護需要重新開機，您會在規劃維護時收到通知。 在這些情況下，您會獲得一段時間，供您在適合的時間自行開始維修。


預定進行的維修作業若需要重新開機，會排定在不同波段。 每一波段有不同的範圍 (區域)。

- 波段開始時會傳送通知給客戶。 根據預設，通知會傳送給訂用帳戶擁有者和共同擁有者。 您可以使用 Azure [活動記錄警示](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md)，對通知新增更多收件者和傳訊選項，例如電子郵件、SMS 和 Webhook。  
- 接獲通知時，即可使用「自助期間」。 在此期間，您可以找到此波段中包含的虛擬機器，並根據自己的排程需求主動開始維護。
- 在自助期間之後，「排定維護期間」隨即開始。 在此期間的某個時間點，Azure 會排定並將必要的維護套用於您的虛擬機器。 

有兩個期間的目標是要讓您在知道 Azure 何時將會自動開始維修的同時，有足夠時間開始維修，並將虛擬機器重新啟動。


您可以使用 Azure 入口網站、PowerShell、REST API 和 CLI 來查詢虛擬機器擴展集 VM 的維修期間，並開始自助式維護。

  
## <a name="should-you-start-maintenance-during-the-self-service-window"></a>您是否應該在自助期間開始維護？  

下列指導方針應該可以協助您決定是否要使用此功能，並且在自己的時間開始維護。

> [!NOTE] 
> 自助式維護可能不適用於所有的虛擬機器。 若要判斷主動式重新部署是否可供您的虛擬機器使用，請在維護狀態中尋找 [立即開始]。 雲端服務 (Web/背景工作角色) 和 Service Fabric 目前無法使用自助維護。


不建議將自助式維護用於採用**可用性設定組**的部署，因為這些都是高可用性安裝，在任何指定的時間，其中只有一個更新網域會受影響。 

- 讓 Azure 觸發維護工作。 針對需要重新開機的維護，請注意，維護將會逐個更新網域完成，更新網域不一定會循序接受維護，而且更新網域之間會暫停 30 分鐘。
- 如果擔心暫時無法使用某些容量 (1/更新網域計數)，在維護期間配置其他執行個體，即可輕鬆補足。
- 針對不需要重新開機的維護，系統會在容錯網域層級套用更新。 
    
在下列情況，**請勿**使用自助式維護： 

- 如果您經常關閉 VM (不論是手動、使用 DevTest Labs、使用自動關機，或依照排程)，都可能還原維護狀態，因而造成額外的停機時間。
- 在您知道將會在維護波段結束前被刪除的短期存留虛擬機器上。 
- 在具有大量狀態的工作負載中，其大量狀態儲存在想要於更新時進行維護的本機 (暫時) 磁碟中。 
- 在您時常調整虛擬機器大小的情況下，因為這可能會還原維護狀態。 
- 如果採用的排定事件能夠讓工作負載得以主動容錯移轉或順利關機，則在維護關機開始前 15 分鐘不應使用自助維護。

如果您打算在排定維護階段連續不中斷地執行您的虛擬機器，而且上述的反指標都不適用，則**使用**自助式維護。 

在下列情況下，最好使用自助式維護：

- 您需要向管理階層或一般客戶傳達確切的維護期間。 
- 您需要在指定的日期前完成維護作業。 
- 您需要控制維護序列 (例如多層式應用程式) 以保證安全復原。
- 在兩個更新網域 (UD) 之間，您需要超過 30 分鐘的虛擬機器復原時間。 若要控制更新網域之間的時間，您必須在虛擬機器上觸發維護 (一次一個更新網域 (UD))。

 
## <a name="view-virtual-machine-scale-sets-impacted-by-maintenance-in-the-portal"></a>在入口網站中檢視受維護影響的虛擬機器擴展集

一旦排定了一波計劃性維護，您就可以使用 Azure 入口網站，檢視受到即將到來之一波維護影響的虛擬機器擴展集清單。 

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 在左側導覽中，選取 [所有服務]，然後選擇 [虛擬機器擴展集]。
3. 在 [虛擬機器擴展集] 頁面上，選擇頂端的 [編輯資料行] 選項，以開啟可用資料行的清單。
4. 在 [可用資料行區段] 中，選取 [自助式維護] 項目，然後使用箭號按鈕以將其移至 [選取的資料行] 清單。 您可以將 [可用的資料行] 區段中的下拉式清單從 [全部] 切換到 [屬性]，以便更容易找到 [自助式維護] 項目。 擁有 [可用的資料行] 區段中的 [自助式維護] 項目後，請選取頁面底部的 [套用] 按鈕。 

完成上述步驟後，[自助式維護] 資料行會出現在虛擬機器擴展集的清單中。 每個虛擬機器擴展集可以具備下列自助式維護資料行的其中一個值：

| 值 | 說明 |
|-------|-------------|
| yes | 虛擬機器擴展集中至少有一個虛擬機器是在自助服務期間。 您可以在此自助服務期間隨時開始維護。 | 
| 否 | 受影響之虛擬機器擴展集中的自助服務期間沒有任何虛擬機器。 | 
| - | 您的虛擬機器擴展集不在這一波計劃性維護中。| 

## <a name="notification-and-alerts-in-the-portal"></a>入口網站中的通知和警示

Azure 會將電子郵件傳送至訂用帳戶擁有者和共同擁有者群組，來傳達計劃性維護排程。 您可以建立 Azure 活動記錄警示，將其他收件者和通道新增到這個通訊。 如需詳細資訊，請參閱 [使用 Azure 活動記錄監視訂用帳戶活動] (../monitoring-and-diagnostics/monitoring-overview-activity-logs.md)

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 在左側功能表中，選取 [監視]。 
3. 在 [監視 - 警示 (傳統)] 窗格中，按一下 [+ 新增活動記錄警示]。
4. 完成 [新增活動記錄警示] 頁面中的資訊，並確保在 [準則] 中設定以下內容：
   - **事件類別目錄**：服務健康狀態
   - **服務**：虛擬機器擴展集與虛擬機器
   - **類型**：計劃性維護 
    
若要進一步了解如何設定活動記錄警示，請參閱[建立活動記錄警示](../monitoring-and-diagnostics/monitoring-activity-log-alerts.md)
    
    
## <a name="start-maintenance-on-your-virtual-machine-scale-set-from-the-portal"></a>從入口網站開始維護您的虛擬機器擴展集

查閱虛擬機器擴展集概觀時，您將能夠看到其他與維護相關的詳細資料。 如果這一波計劃性維護中包含至少一部虛擬機器擴展集中的虛擬機器，則會在頁面頂端附近新增新的通知功能區。 您可以按一下通知功能區，以移至 [維修] 頁面，查看哪些虛擬機器執行個體受到計劃性維護的影響。 

您可以從該頁面中藉由勾選對應至受影響虛擬機器的方塊，然後按一下 [開始維修] 選項以開始維修。

開始維護之後，虛擬機器擴展集中受影響的虛擬機器將進行維護，暫時無法使用。 如果您錯過了自助服務期間，仍可以看到 Azure 將維護虛擬機器擴展集的期間。
 
## <a name="check-maintenance-status-using-powershell"></a>使用 PowerShell 檢查維修狀態

您可以使用 Azure Powershell 來查看虛擬機器擴展集排定何時進行維修。 使用 `-InstanceView` 參數時，可從 [Get-AzureRmVmss](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmss) Cmdlet 取得預定進行的維修作業的相關資訊。
 
只在有預定進行的維修作業時，才會傳回維修資訊。 如果沒有排定會影響虛擬機器執行個體的維護，則 Cmdlet 不會傳回任何維護資訊。 

```powershell
Get-AzureRmVmss -ResourceGroupName rgName -VMScaleSetName vmssName -InstanceId id -InstanceView
```

下列是 MaintenanceRedeployStatus 下傳回的內容： 
| 值 | 說明   |
|-------|---------------|
| IsCustomerInitiatedMaintenanceAllowed | 指出您目前是否可以在 VM 上開始維修 ||
| PreMaintenanceWindowStartTime         | 維修自助期間的開始，此時您可以在 VM 上起始維修 ||
| PreMaintenanceWindowEndTime           | 維修自助期間的結束，此時您可以在 VM 上起始維修 ||
| MaintenanceWindowStartTime            | 排定維護開始，此時 Azure 會在您的虛擬機器上起始維護 ||
| MaintenanceWindowEndTime              | 排定維護期間結束，此時 Azure 會在您的虛擬機器上停止維護 ||
| LastOperationResultCode               | 前次嘗試在 VM 上起始維修的結果 ||



### <a name="start-maintenance-on-your-vm-instance-using-powershell"></a>使用 PowerShell 在虛擬機器執行個體上開始維修

如果使用帶有 `-PerformMaintenance` 參數的 [Set-AzureRmVmss](/powershell/module/azurerm.compute/set-azurermvmss) Cmdlet，將 **IsCustomerInitiatedMaintenanceAllowed** 設為 True，則可以在虛擬機器上開始維修。

```powershell
Set-AzureRmVmss -ResourceGroupName rgName -VMScaleSetName vmssName -InstanceId id -PerformMaintenance 
```

## <a name="check-maintenance-status-using-cli"></a>使用 CLI 檢查維修狀態

您可以使用 [az vmss list-instances](/cli/azure/vmss?view=azure-cli-latest#az-vmss-list-instances) 來查看預定進行的維修作業資訊。
 
只在有預定進行的維修作業時，才會傳回維修資訊。 如果沒有會影響虛擬機器執行個體的排定維修，則命令不會傳回任何維修資訊。 

```azure-cli
az vmss list-instances -g rgName -n vmssName --expand instanceView
```

下列是針對每個虛擬機器執行個體在 MaintenanceRedeployStatus 下傳回的內容： 
| 值 | 說明   |
|-------|---------------|
| IsCustomerInitiatedMaintenanceAllowed | 指出您目前是否可以在 VM 上開始維修 ||
| PreMaintenanceWindowStartTime         | 維修自助期間的開始，此時您可以在 VM 上起始維修 ||
| PreMaintenanceWindowEndTime           | 維修自助期間的結束，此時您可以在 VM 上起始維修 ||
| MaintenanceWindowStartTime            | 排定維護開始，此時 Azure 會在您的虛擬機器上起始維護 ||
| MaintenanceWindowEndTime              | 排定維護期間結束，此時 Azure 會在您的虛擬機器上停止維護 ||
| LastOperationResultCode               | 前次嘗試在 VM 上起始維修的結果 ||


### <a name="start-maintenance-on-your-vm-instance-using-cli"></a>使用 CLI 在虛擬機器執行個體上開始維修

如果 `IsCustomerInitiatedMaintenanceAllowed` 設為 true，則下列呼叫會在虛擬機器執行個體上起始維修。

```azure-cli
az vmss perform-maintenance -g rgName -n vmssName --instance-ids id
```

## <a name="faq"></a>常見問題集


**問：為什麼需要立即重新啟動我的虛擬機器？**

**答：** 雖然對 Azure 平台的大部分更新與升級不會影響虛擬機器的可用性，但是有時候我們無法避免重新啟動裝載於 Azure 的虛擬機器。 我們已經累積數個變更，需要我們重新啟動伺服器，這會導致虛擬機器重新開機。

**問：如果我遵循建議針對高可用性使用可用性設定組，是否安全？**

**答：** 部署在可用性設定組或虛擬機器擴展集的虛擬機器，具有更新網域 (UD) 的概念。 執行維護時，Azure 會接受 UD 條件約束，並且不會從不同的 UD (在相同的可用性設定組內) 重新啟動虛擬機器。  Azure 也會等候至少 30 分鐘，再移至下一個虛擬機器群組。 

如需有關高可用性的詳細資訊，請參閱 [Azure 中虛擬機器的區域和可用性](../virtual-machines/windows/regions-and-availability.md)。

**問：我如何取得規劃維護的通知？**

**答：** 規劃的維護是從對一或多個 Azure 區域設定排程開始。 之後，電子郵件通知會傳送至訂用帳戶擁有者 (每個訂用帳戶一封電子郵件)。 可以使用「活動記錄警示」來設定此通知的其他通道和收件者。 如果您將虛擬機器部署到已排程規劃的維護之區域，您不會收到通知，但是需要檢查 VM 的維護狀態。

**問：我在入口網站、Powershell 或 CLI 中都看不到任何關於計劃性維護的指示。發生什麼狀況？**

**答：** 只有在計劃性維護期間將受到該維護波段影響的 VM 可以取得與計劃性維護相關的資訊。 也就是說，如果您看不到資料，可能是維護已完成 (或尚未啟動)，或者您的虛擬機器是裝載在更新的伺服器上。

**問：是否有方法可以確切知道我的虛擬機器何時會受到影響？**

**答：** 設定排程時，我們會定義數天的時間期間。 不過，此期間內伺服器 (和 VM) 的確切順序則未知。 想要知道其 VM 確切時間的客戶，可以使用[排定的事件](../virtual-machines/windows/scheduled-events.md)並且從虛擬機器內查詢，而後會在 VM 重新開機前 15 分鐘收到通知。

**問：重新啟動我的虛擬機器需要多久時間？**

**答：** 根據您的虛擬機器大小，在自助式維護期間重新啟動最長可能需要幾分鐘的時間。 在 Azure 於排定維護期間起始重新啟動期間，重新啟動通常需要大約 25 分鐘。 請注意，如果您使用雲端服務 (Web/背景工作角色)、虛擬機器擴展集或可用性設定組，在排定維護期間每個虛擬機器群組 (UD) 之間有 30 分鐘。 

**問：我在我的 VM 上看不到任何維護資訊。哪裡發生錯誤？**

**答：** 您在 VM 上看不到任何維護資訊有以下幾個原因：
   - 您使用標示為 Microsoft 內部的訂用帳戶。
   - 您的 VM 未排程進行維護。 可能是維護波段已經結束、取消或修改，所以您的 VM 不再受到它的影響。
   - 您尚未將 [維護] 資料行新增至您的虛擬機器清單檢視。 雖然我們已將此資料行新增至預設檢視，但是設定為查看非預設資料行的客戶必須將 [維護] 資料行手動新增至其 VM 清單檢視。

**問：我的 VM 第二次排程進行維護。原因為何？**

**答：** 有數個使用案例，您會在您已完成維護重新部署之後，看到 VM 排程進行維護：
   - 我們已取消維護，並且使用不同的裝載重新啟動它。 可能是我們偵測到發生錯誤的裝載，只是需要部署額外承載。
   - 因為硬體故障，您的 VM 已*服務修復*到另一個節點。
   - 您已經選擇停止 (解除配置)，然後重新啟動 VM。
   - 您已經為 VM 開啟**自動關機**。

## <a name="next-steps"></a>後續步驟

了解如何使用[排定的事件](../virtual-machines/windows/scheduled-events.md)從 VM 內註冊取得維修事件。
