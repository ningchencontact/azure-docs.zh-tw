---
title: "啟動/停止 Vm，在離峰時間方案 （預覽） |Microsoft 文件"
description: "此 VM 的管理解決方案會啟動和停止您的 Azure 資源管理員虛擬機器上的排程並主動從記錄分析會監視。"
services: automation
documentationCenter: 
authors: eslesar
manager: carmonm
editor: 
ms.assetid: 06c27f72-ac4c-4923-90a6-21f46db21883
ms.service: automation
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/18/2017
ms.author: magoedte
ms.openlocfilehash: 4424cbb83bdb31c60e15d62f9387b4050611a98d
ms.sourcegitcommit: 6f33adc568931edf91bfa96abbccf3719aa32041
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/22/2017
---
# <a name="startstop-vms-during-off-hours-solution-preview-in-azure-automation"></a>在離峰時間方案 （預覽） 在 Azure 自動化中啟動/停止 Vm

啟動/停止 Vm，在離峰時間方案開始和停止使用者定義的排程上的 Azure 虛擬機器，提供深入資訊，透過 Azure Log Analytics，並傳送選擇性的電子郵件使用[SendGrid](https://azuremarketplace.microsoft.com/marketplace/apps/SendGrid.SendGrid?tab=Overview)。 針對大多數的案例，它皆能同時支援 Azure Resource Manager 和傳統 VM。 

此解決方案可提供使用者想要減少其成本，藉由使用無伺服器、 低成本的資源的非集中式的 automation 選項。 使用此解決方案中，您可以：

* 排程來啟動和停止的 Vm。
* 排程來啟動和停止使用 Azure 標記 （不支援傳統的 Vm） 依遞增順序排列的 Vm。
* 低 CPU 使用率為基礎的自動停止 Vm。

## <a name="prerequisites"></a>必要條件

- Runbook 會使用 [Azure 執行身分帳戶](automation-offering-get-started.md#authentication-methods)。  執行身分帳戶是慣用的驗證方法，因為它會使用憑證驗證，而不使用密碼，可能會過期或經常變更。  

- 此解決方案會管理您的 Azure 自動化帳戶在相同訂用帳戶中的 Vm。  

- 這個解決方案只能部署到下列的 Azure 區域： 澳洲東南部、 加拿大中部、 印度中部、 美國東部、 日本東部、 東南亞、 英國南和西歐。  
    
    > [!NOTE]
    > 管理 VM 排程的 Runbook 能夠以任何區域中的 VM 為目標。  

- 若要傳送電子郵件通知，當啟動和停止 VM runbook 完成時，Azure marketplace 的入門訓練時選取**是**部署 SendGrid。 

    > [!IMPORTANT]
    > SendGrid 是第三方服務。 如需支援，請連絡[SendGrid](https://sendgrid.com/contact/)。  
    >
   
    使用 SendGrid 的限制如下：

    * 每位使用者每個訂閱一個 SendGrid 帳戶的最大值。
    * 每個訂閱的兩個 SendGrid 帳戶的最大值。

如果您已部署此解決方案的先前版本，您必須先刪除它從您的帳戶部署這個版本之前。  

## <a name="solution-components"></a>方案元件

此解決方案包括預先設定的 runbook、 排程和與記錄分析的整合，因此您可以啟動或關閉您的虛擬機器，以符合您的業務需求來自訂。 

### <a name="runbooks"></a>Runbook

下表列出部署至您的自動化帳戶的 runbook。  您不應該進行變更 runbook 的程式碼。 相反地，撰寫您自己的 runbook 的新功能。

> [!IMPORTANT]
> 請勿直接執行任何 runbook 「 子 」 附加至其名稱。
>

包含所有父 runbook *WhatIf*參數。 當設定為**True**， *WhatIf*支援詳述的確切行為 runbook 時，會執行，而不*WhatIf*參數及驗證正確的 Vm 正在進行目標。  Runbook 只會執行其已定義的動作時*WhatIf*參數設定為**False**。 

|**Runbook** | **參數** | **說明**|
| --- | --- | ---| 
|AutoStop_CreateAlert_Child | VMObject <br> AlertAction <br> WebHookURI | 只會從父 runbook 的呼叫。 AutoStop 案例中每個資源為基礎建立警示。| 
|AutoStop_CreateAlert_Parent | WhatIf：True 或 False <br> VMList | 在目標訂用帳戶或資源群組中的 VM 上建立或更新 Azure 警示規則。 <br> VMList： 以逗號分隔的 Vm 清單。  例如， *vm1 vm2 的情況，vm3*。| 
|AutoStop_Disable | None | 停用 AutoStop 警示與預設排程。| 
|AutoStop_StopVM_Child | WebHookData | 只會從父 runbook 的呼叫。 警示規則會呼叫此 runbook 停止 VM。|  
|Bootstrap_Main | None | 若要設定啟動程序組態，如 webhookURI，通常不是可存取 Azure 資源管理員從使用一次。 此 runbook 會自動移除部署成功之後。|  
|ScheduledStartStop_Child | VMName <br> Action：Stop 或 Start <br> resourceGroupName | 只會從父 runbook 的呼叫。 停止或啟動排程停止執行。|  
|ScheduledStartStop_Parent | Action：Stop 或 Start <br> WhatIf：True 或 False | 這會影響的訂用帳戶中的所有 Vm。 編輯**External_Start_ResourceGroupNames**和**External_Stop_ResourceGroupNames**只在這些目標資源群組上執行。 您也可以更新 **External_ExcludeVMNames** 變數來排除特定的 VM。 *WhatIf*行為中的相同其他 runbook。|  
|SequencedStartStop_Parent | Action：Stop 或 Start <br> WhatIf：True 或 False | 建立名為標記**SequenceStart**和**SequenceStop**要啟動/停止的序列活動的每個 VM 上。 標記值應該是正整數 （1、 2、 3） 對應至您要用來啟動或停止的順序。 *WhatIf*行為中的相同其他 runbook。 <br> **請注意**: Vm 必須在定義 External_Start_ResourceGroupNames、 External_Stop_ResourceGroupNames，以及 External_ExcludeVMNames Azure 自動化變數中的資源群組內。 它們必須適當標記動作才會生效。|

### <a name="variables"></a>變數

下表列出您的自動化帳戶中建立的變數。  您應該只會修改變數前面加上**外部**。 修改變數的前面加上**內部**會導致非預期的效果。  

|**Variable** | **說明**|
---------|------------|
|External_AutoStop_Condition | 條件運算子所需的設定之前觸發警示的條件。 可接受的值**GreaterThan**， **GreaterThanOrEqual**， **LessThan**，和**LessThanOrEqual**。|  
|External_AutoStop_Description | 若要停止 VM 的 CPU 百分比超出臨界值警示。|  
|External_AutoStop_MetricName | 設定 Azure 警示規則的效能標準的名稱。| 
|External_AutoStop_Threshold | 在變數中指定的 Azure 警示規則的臨界值*External_AutoStop_MetricName*。 百分比值的範圍為 1 至 100。|  
|External_AutoStop_TimeAggregationOperator | 時間彙總運算子，套用至選取的視窗大小，以評估條件。 可接受的值**平均**，**最小值**，**最大**，**總**，和**最後**。|  
|External_AutoStop_TimeWindow | Azure 會觸發警示的選定的度量分析期間視窗大小。 此參數接受時間範圍格式的輸入。 可能的值為 5 分鐘到 6 小時。|  
|External_EmailFromAddress | 指定電子郵件的寄件者。|  
|External_EmailSubject | 指定電子郵件的主旨列文字。|  
|External_EmailToAddress | 指定電子郵件的收件者。 使用逗點分隔名稱。|  
|External_ExcludeVMNames | 輸入要排除的 VM 名稱使用不含空格的逗號分隔名稱。|  
|External_IsSendEmail | 指定要傳送電子郵件通知完成時的選項。  針對是否傳送電子郵件，指定 **Yes** 或 **No**。  這個選項應該設為**否**如果您未在初始部署期間啟用電子郵件通知。|  
|External_Start_ResourceGroupNames | 指定一或多個資源群組，請使用逗號，針對 啟動動作分隔值。|  
|External_Stop_ResourceGroupNames | 指定一或多個資源群組，請使用逗號、 停止動作的目標分隔值。|  
|Internal_AutomationAccountName | 指定自動化帳戶的名稱。|  
|Internal_AutoSnooze_WebhookUri | 指定針對 AutoStop 案例呼叫的 Webhook URI。|  
|Internal_AzureSubscriptionId | 指定 Azure 訂用帳戶 id。|  
|Internal_ResourceGroupName | 指定的自動化帳戶資源群組名稱。|  
|Internal_SendGridAccountName | 指定 SendGrid 帳戶名稱。|  
|Internal_SendGridPassword | 指定 SendGrid 帳戶密碼。|  

<br>

在所有情況下， **External_Start_ResourceGroupNames**， **External_Stop_ResourceGroupNames**，和**External_ExcludeVMNames**變數所需為目標的 Vm，除了提供以逗號分隔清單的 Vm **AutoStop_CreateAlert_Parent** runbook。 也就是您的 Vm 必須位於目標資源群組，以啟動與停止發生的動作。 邏輯的運作方式有點類似 Azure 的原則，您可以訂用帳戶或資源群組為目標，讓動作新建立的 Vm 所繼承。 此方法可避免必須為每個 VM 維護個別的排程和管理啟動並停止在標尺。

### <a name="schedules"></a>排程

下表列出在您的自動化帳戶中建立的各個預設排程。  您可以修改它們，或建立自己的自訂排程。  依預設，每一種會停用除了**Scheduled_StartVM**和**Scheduled_StopVM**。

您不應該啟用所有排程，因為這樣可能產生重疊的排程動作。 最好判斷您要執行，並據此修改哪一個的最佳化。  如需進一步說明，請參閱＜概觀＞一節中的範例案例。   

|**排程名稱** | **頻率** | **說明**|
|--- | --- | ---|
|Schedule_AutoStop_CreateAlert_Parent | 每隔 8 小時 | 執行 AutoStop_CreateAlert_Parent runbook 來每 8 小時，接著停止 VM 為基礎的值 External_Start_ResourceGroupNames、 External_Stop_ResourceGroupNames，和 External_ExcludeVMNames Azure 自動化變數中。  或者，您可以使用 VMList 參數所指定 Vm 以逗號分隔的清單。|  
|Scheduled_StopVM | 使用者定義的每日 | 使用的參數執行 Scheduled_Parent runbook*停止*每天在指定的時間。  會自動停止所有 Vm 符合 asset 變數所定義的規則。 您應該啟用相關的排程，**排程 StartVM**。|  
|Scheduled_StartVM | 使用者定義的每日 | 使用的參數執行 Scheduled_Parent runbook*啟動*每天在指定的時間。  會自動啟動的所有 Vm 符合適當的變數所定義的規則。  您應該啟用相關的排程，**排程 StopVM**。|
|Sequenced-StopVM | 1:00 (UTC)，每個星期五 | 使用的參數執行 Sequenced_Parent runbook*停止*每星期五，在指定的時間。  循序 （遞增） 停止的標記的所有 Vm **SequenceStop**由適當的變數定義。  是指 Runbook 資產，變數和標記值的更多詳細資料 」 一節。  您應該啟用相關的排程，**循序 StartVM**。|
|Sequenced-StartVM | 下午 1:00 (UTC)，每個星期一 | 每星期一會在指定時間搭配參數 *Start* 執行 Sequenced_Parent Runbook。 循序 （遞減） 開頭的標記的所有 Vm **SequenceStart**由適當的變數定義。  是指 Runbook 資產，變數和標記值的更多詳細資料 」 一節。  您應該啟用相關的排程，**循序 StopVM**。|

<br>

## <a name="configuration"></a>組態

執行下列步驟，以在離峰時間方案中加入您的自動化帳戶的啟動/停止 Vm，然後設定 自訂解決方案的變數。

1. 在 Azure 入口網站中，按一下 [新增]。<br> ![Azure 入口網站](media/automation-solution-vm-management/azure-portal-01.png)<br>  
2. 在 [Marketplace] 窗格中，輸入關鍵字例如**啟動**或**啟動/停止**。 當您開始輸入時，清單會根據您輸入的文字進行篩選。 或者，您可以輸入來自方案的完整名稱的一或多個關鍵字，並再按 Enter 鍵。  從搜尋結果選取 [停機期間啟動/停止 VM [預覽]]。  
3. 在所選解決方案的 [停機期間啟動/停止 VM [預覽]] 窗格中，檢閱摘要資訊，然後按一下 [建立]。  
4. **將方案加入** 窗格隨即出現。 系統會提示您設定解決方案之前，您可以將它匯入自動化訂用帳戶。<br><br> ![VM 管理的新增方案刀鋒視窗](media/automation-solution-vm-management/azure-portal-add-solution-01.png)<br><br>
5.  在**將方案加入**窗格中，選取**工作區**。 選取連結至相同 Azure 訂用帳戶的自動化帳戶中的 OMS 工作區。 如果您沒有工作區中，選取**建立新的工作區**。 在**OMS 工作區** 窗格中，請執行下列動作： 
   - 指定新 [OMS 工作區] 的名稱。
   - 選取**訂用帳戶**連結到選取從下拉式清單中，如果選取預設值並不適用。
   - 如**資源群組**，您可以建立新的資源群組，或選取現有的我的最愛。  
   - 選取 [位置] 。  目前，唯一可用的位置是**澳洲東南部**，**加拿大中央**，**印度中部**，**美國東部**， **日本東部**，**東南亞**，**英國南**，和**西歐**。
   - 選取 **定價層**。  此解決方案提供兩個層級： 免費和付費 OMS。  免費層有限制收集的資料量，每日保留期限及 runbook 工作執行階段的分鐘數。  OMS 付費層不會限制每日可收集的資料量。  

        > [!NOTE]
        > 雖然每個 GB （獨立） 付費層會顯示為一個選項，它並不適用。  如果您選取它，然後再繼續您的訂用帳戶中建立此解決方案，它就會失敗。  當此解決方案正式發行時，將會解決這個問題。<br>這個解決方案只使用自動化的工作分鐘數和記錄檔擷取。  它不會新增到您的環境 OMS 的其他節點。  

6. 在提供必要的資訊之後**OMS 工作區**] 窗格中，按一下 [**建立**。  您可以追蹤其進度下的**通知**從功能表中，這會讓您以**將方案加入**窗格時完成。  
7. 在**將方案加入**窗格中，選取**自動化帳戶**。  如果您要建立新的 OMS 工作區，您需要同時建立新的自動化帳戶是有與其相關聯。  選取**建立自動化帳戶**，然後在**加入自動化帳戶** 窗格中，提供下列： 
  - 在 [名稱] 欄位中輸入自動化帳戶的名稱。

    所有其他選項會自動填入根據選定的 OMS 工作區。 無法修改這些選項。  Azure 執行身分帳戶是此方案內含 Runbook 的預設驗證方法。  按一下 之後**確定**、 組態選項會進行驗證，並在建立自動化帳戶。  您可以在功能表的 [通知] 底下追蹤其進度。 

    不然，您可以選取現有的自動化執行身分帳戶。  請注意您選取的帳戶不能已連結至另一個 OMS 工作區。 如果已經連結，您會收到訊息，而且需要選取不同的自動化執行身分帳戶，或建立新。<br><br> ![自動化帳戶已經連結至 OMS 工作區](media/automation-solution-vm-management/vm-management-solution-add-solution-blade-autoacct-warning.png)<br>

8. 最後，在**將方案加入**窗格中，選取**組態**。 **參數** 窗格隨即出現。<br><br> ![解決方案的 [參數] 窗格](media/automation-solution-vm-management/azure-portal-add-solution-02.png)<br><br>  在這裡，系統會提示您：  
   - 指定**目標資源群組名稱**。 這些是包含此解決方案所管理的 Vm 的資源群組名稱。  您可以輸入多個名稱，然後隔開每個使用逗號 （值不區分大小寫）。  如果您想要以訂用帳戶的所有資源群組中的 VM 為目標，則可使用萬用字元。
   - 指定**VM 排除清單 （字串）**。 這是一個或多個不同的虛擬機器的目標資源群組名稱。  您可以輸入多個名稱，然後隔開每個使用逗號 （值不區分大小寫）。  支援使用萬用字元。
   - 選取**排程**。 這是週期性的日期和時間啟動和停止目標資源群組中的 Vm。  根據預設，排程會設定為 UTC 時區為準。 選取不同的區域無法使用。  若要設定排程，以特定的時區設定方案之後，請參閱[修改啟動和關閉排程](#modifying-the-startup-and-shutdown-schedule)。
   - 若要從 SendGrid 接收**電子郵件通知**，請接受預設值 [是]，並提供有效的電子郵件地址。 如果您選取**否**，但您想要收到電子郵件通知，您可以更新，在日後決定**External_EmailToAddress**以逗號，分隔的有效電子郵件地址的變數，然後修改變數**External_IsSendEmail**值**是**。  

9. 您已經設定此解決方案需要初始設定之後，請選取**建立**。  驗證所有設定之後，方案會部署到您的訂用帳戶。  此程序可能需要幾秒鐘的時間才能完成，而且您可以追蹤其進度下的**通知**從功能表。 

## <a name="collection-frequency"></a>收集頻率

Automation 作業記錄檔和工作資料流資料會內嵌到記錄分析儲存機制每隔 5 分鐘。  

## <a name="using-the-solution"></a>使用解決方案

當您將新增 VM 的管理解決方案， **StartStopVMView** Azure 入口網站中的圖格加入至記錄分析工作區中的儀表板。  這個磚會顯示計數和解決方案的啟動與已順利完成 runbook 工作的圖形表示法。<br><br> ![VM 管理 StartStopVM 檢視圖格](media/automation-solution-vm-management/vm-management-solution-startstopvm-view-tile.png)  

在您的自動化帳戶可以存取並管理解決方案，藉由選取**工作區**選項。 在 記錄分析 頁面上，選取 **解決方案**從左窗格。  在**解決方案**頁面上，選取方案**開始停止 VM [工作區]**從清單中。<br><br> ![Log Analytics 中的 [解決方案] 清單](media/automation-solution-vm-management/azure-portal-select-solution-01.png)  

選取方案會顯示**開始停止 VM [工作區]**方案窗格。 這裡您可以檢閱的重要詳細資料這類**StartStopVM**磚。 記錄分析工作區中，如同此磚會顯示計數，已啟動並成功完成 runbook 工作的方案的圖形表示法。<br><br> ![自動更新管理解決方案頁面](media/automation-solution-vm-management/azure-portal-vmupdate-solution-01.png)  

從這裡，您可以執行進一步分析的工作記錄按一下環圈圖格。 方案儀表板會顯示作業記錄，並預先定義的記錄搜尋查詢。 切換到記錄分析進階入口網站，以搜尋會根據您的搜尋查詢。  

包含所有父 runbook *WhatIf*參數。 當設定為**True**，runbook 會執行時沒有實際的行為，詳列出此支援*WhatIf*參數，並且會驗證正確的 Vm 會做為目標。 Runbook 只能執行其已定義的動作時*WhatIf*參數設定為**False**。  


### <a name="scenario-1-daily-startstop-vms-across-a-subscription-or-target-resource-groups"></a>案例 1： 每日啟動/停止 Vm 訂用帳戶或目標資源群組 

這是您首次部署解決方案時的預設設定。  例如，您可以設定所有 Vm 停止訂用帳戶，當您離開工作在晚上，並啟動它們在早上，當您已回到辦公室。 當您設定排程**排程 StartVM**和**排程 StopVM**在部署期間，開始和停止目標的 Vm。
>[!NOTE]
>當您設定排程時間參數，時區會是目前的時區。 不過，它會儲存在 Azure 自動化中的 UTC 格式。  您不需要執行任何時區轉換，因為它會在部署期間處理。

您控制哪些 Vm 在範圍中是藉由設定下列變數： **External_Start_ResourceGroupNames**， **External_Stop_ResourceGroupNames**，和**External_ExcludeVMNames**。  

>[!NOTE]
> 值**目標資源群組名稱**同時做為值會儲存**External_Start_ResourceGroupNames**和**External_Stop_ResourceGroupNames**。 如需進一步的細微設定，您可以修改這些變數來以不同的資源群組為目標。  啟動動作，對於使用**External_Start_ResourceGroupNames**，並停止動作，使用**External_Stop_ResourceGroupNames**。 Vm 會自動新增至啟動和停止排程。

若要測試並驗證您的組態，請手動啟動**ScheduledStartStop_Parent** runbook 並將動作參數設定為**啟動**或**停止**和 WHATIF參數**True**。<br><br> ![設定 Runbook 的參數](media/automation-solution-vm-management/solution-startrunbook-parameters-01.png)


 預覽已排程的動作，並針對實際執行 Vm 實作前進行任何必要的變更。  您可以手動將 runbook 執行參數設為**False**，或者您也可以讓自動化排程**排程 StartVM**和**排程 StopVM**執行會自動遵循您指定的排程。

### <a name="scenario-2-sequence-the-startstop-vms-across-a-subscription-by-using-tags"></a>案例 2： 使用標記順序將開始/停止 Vm 訂用帳戶
在包含兩個或多個元件支援分散式工作負載的多個 Vm 上的環境，為了支援元件會啟動和停止的順序很重要。  您可以執行下列步驟來完成這項作業：

1. 新增**SequenceStart**和**SequenceStop**正整數值為目標中的 vm 具有標記**External_Start_ResourceGroupNames**和**External_Stop_ResourceGroupNames**變數。  啟動和停止動作會以遞增的順序執行。  若要深入了解如何標記 VM，請參閱[標記在 Azure 中的 Windows 虛擬機器](../virtual-machines/windows/tag.md)和[標記在 Azure 中 Linux 虛擬機器](../virtual-machines/linux/tag.md)。
2. 修改排程**循序 StartVM**和**循序 StopVM**的日期和時間，您的需求，並啟用排程。  

若要測試並驗證您的組態，請手動啟動**SequencedStartStop_Parent** runbook。 將動作參數設定為**啟動**或**停止**並使用 WHATIF 參數來**True**。<br><br> ![設定 Runbook 的參數](media/automation-solution-vm-management/solution-startrunbook-parameters-01.png)<br> 預覽動作，並針對實際執行 Vm 實作前進行任何必要的變更。  當準備好，請手動執行 runbook 的參數設定為**False**，或讓自動化排程**循序 StartVM**和**循序 StopVM**執行會自動遵循您指定的排程。  

### <a name="scenario-3-automate-startstop-vms-across-a-subscription-based-on-cpu-utilization"></a>案例 3： 自動化啟動/停止 Vm 根據 CPU 使用量的訂用帳戶
此解決方案可協助您管理您的訂用帳戶中執行虛擬機器，藉由評估不例如小時之後，使用非尖峰時段的 Azure Vm 及自動關閉它們如果處理器使用量符號 0x %的成本。  

根據預設，方案是預先設定，以評估是否平均使用率為 5%的百分比 CPU 計量或更少。  這由下列變數，預設值不符合您的需求時可以修改：

* External_AutoStop_MetricName
* External_AutoStop_Threshold
* External_AutoStop_TimeAggregationOperator
* External_AutoStop_TimeWindow

您可以啟用目標對象為訂用帳戶和資源群組動作或目標的 Vm，但非兩者的特定清單。  

#### <a name="target-the-stop-action-against-a-subscription-and-resource-group"></a>針對訂用帳戶和資源群組設定停止動作目標

1. 設定 **External_Stop_ResourceGroupNames** 和 **External_ExcludeVMNames** 變數來指定目標 VM。  
2. 啟用並更新 **Schedule_AutoStop_CreateAlert_Parent** 排程。
3. 執行**AutoStop_CreateAlert_Parent** runbook 使用 ACTION 參數設定為**啟動**並設定為使用 WHATIF 參數**True**預覽您的變更。

#### <a name="target-the-stop-action-by-vm-list"></a>以停止動作目標 VM 清單

1. 執行**AutoStop_CreateAlert_Parent** runbook 使用 ACTION 參數設定為**啟動**，新增以逗號分隔的清單中的 Vm *VMList*參數，並將其設定WHATIF 參數來**True**。 預覽您的變更。  
2. 設定**External_ExcludeVMNames**參數使用的 Vm (VM1，vm2 的情況，VM3) 以逗號分隔清單。
3. 此案例中就不會接受**External_Start_ResourceGroupNames**和**External_Stop_ResourceGroupnames**變數。  此案例中，您要建立您自己自動化的排程。 如需詳細資訊，請參閱[排程在 Azure 自動化 runbook](../automation/automation-schedules.md)。

既然您已停止 Vm 根據 CPU 使用量的排程，您需要啟用下列的排程，以啟動它們的其中一個。

* 目標訂用帳戶和資源群組的啟動動作。  請參閱中的步驟[案例 1](#scenario-1:-daily-stop/start-vms-across-a-subscription-or-target-resource-groups)以便進行測試，並且啟用**排程 StartVM**排程。
* 目標啟動訂用帳戶、 資源群組和標記的動作。  請參閱中的步驟[案例 2](#scenario-2:-sequence-the-stop/start-vms-across-a-subscription-using-tags)以便進行測試，並且啟用**循序 StartVM**排程。


### <a name="configuring-email-notifications"></a>設定電子郵件通知

若要部署方案之後，請設定電子郵件通知，請修改下列三個變數：

* External_EmailFromAddress： 指定寄件者的電子郵件地址。
* External_EmailToAddress： 指定以逗號分隔清單的電子郵件地址 (user@hotmail.com， user@outlook.com) 接收通知電子郵件。
* External_IsSendEmail： 設為**是**接收電子郵件。 若要停用電子郵件通知，請將值設定為**否**。   


### <a name="modifying-the-startup-and-shutdown-schedules"></a>修改啟動和關機排程

在此解決方案中管理啟動和關機排程的步驟，與[在 Azure 自動化中排程 Runbook](automation-schedules.md) 中所概述的步驟相同。     

## <a name="log-analytics-records"></a>Log Analytics 記錄

自動化在 OMS 儲存機制中建立兩種類型的記錄： 作業記錄和作業資料流。

### <a name="job-logs"></a>作業記錄檔

屬性 | 說明|
----------|----------|
呼叫者 |  起始作業的人員。  可能的值為電子郵件地址或排程作業的系統。|
類別 | 資料類型的分類。  對自動化來說，該值是 JobLogs。|
CorrelationId | Runbook 作業之相互關聯識別碼的 GUID。|
JobId | Runbook 作業之識別碼的 GUID。|
operationName | 指定在 Azure 中執行的作業類型。  對自動化來說，該值是 Job。|
ResourceId | 指定 Azure 中的資源類型。  對自動化來說，該值是與 Runbook 相關聯的自動化帳戶。|
ResourceGroup | 指定 Runbook 作業的資源群組名稱。|
ResourceProvider | 指定 Azure 服務，以提供您可部署及管理的資源。  對自動化來說，此值是 Azure 自動化。|
ResourceType | 指定 Azure 中的資源類型。  對自動化來說，該值是與 Runbook 相關聯的自動化帳戶。|
resultType | Runbook 作業的狀態。  可能的值包括：<br>- Started (已啟動)<br>- Stopped (已停止)<br>- Suspended (暫止)<br>- Failed (失敗)<br>- Succeeded|
resultDescription | 說明 Runbook 作業的結果狀態。  可能的值包括：<br>- Job is started (工作已啟動)<br>- Job Failed (工作失敗)<br>- Job Completed|
RunbookName | 指定 Runbook 的名稱。|
SourceSystem | 指定所提交資料的來源系統。  對於提供自動化功能，這個值會是 OpsManager|
StreamType | 指定事件的類型。 可能的值包括：<br>- Verbose<br>- Output<br>- Error<br>- Warning|
SubscriptionId | 指定作業的訂用帳戶 ID。
時間 | Runbook 作業的執行日期和時間。|


### <a name="job-streams"></a>作業串流

屬性 | 說明|
----------|----------|
呼叫者 |  起始作業的人員。  可能的值為電子郵件地址或排程作業的系統。|
類別 | 資料類型的分類。  對自動化來說，該值是 JobStreams。|
JobId | Runbook 作業之識別碼的 GUID。|
operationName | 指定在 Azure 中執行的作業類型。  對自動化來說，該值是 Job。|
ResourceGroup | 指定 Runbook 作業的資源群組名稱。|
ResourceId | 指定 Azure 中的資源識別碼。  對自動化來說，該值是與 Runbook 相關聯的自動化帳戶。|
ResourceProvider | 指定 Azure 服務，以提供您可部署及管理的資源。  對自動化來說，此值是 Azure 自動化。|
ResourceType | 指定 Azure 中的資源類型。  對自動化來說，該值是與 Runbook 相關聯的自動化帳戶。|
resultType | Runbook 作業在產生事件時的結果。 可能的值為：<br>- InProgres|
resultDescription | 包含來自 Runbook 的輸出串流。|
RunbookName | Runbook 的名稱。|
SourceSystem | 指定所提交資料的來源系統。  對於提供自動化功能，這個值會是 OpsManager。|
StreamType | 作業串流的類型。 可能的值包括：<br>-進度<br>- Output (輸出)<br>- Warning (警告)<br>- Error (錯誤)<br>- Debug (偵錯)<br>- Verbose|
時間 | Runbook 作業的執行日期和時間。|

當您執行任何傳回的類別目錄記錄的記錄搜尋**JobLogs**或**JobStreams**，您可以選取**JobLogs**或**JobStreams**檢視，可顯示一組圖格的彙總的搜尋傳回的更新。

## <a name="sample-log-searches"></a>記錄搜尋範例

下表提供此方案所收集之作業記錄的記錄檔搜尋範例。 

查詢 | 說明|
----------|----------|
尋找 runbook ScheduledStartStop_Parent 已順利完成的工作 | search Category == "JobLogs" &#124; where ( RunbookName_s == "ScheduledStartStop_Parent" ) &#124; where ( ResultType == "Completed" )  &#124; summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) &#124; sort by TimeGenerated desc|
尋找 runbook SequencedStartStop_Parent 已順利完成的工作 | search Category == "JobLogs" &#124; where ( RunbookName_s == "SequencedStartStop_Parent" ) &#124; where ( ResultType == "Completed" )  &#124; summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) &#124; sort by TimeGenerated desc

## <a name="removing-the-solution"></a>移除解決方案

如果您決定您不再需要使用的解決方案，您可以將它刪除自動化帳戶。  刪除方案時，只會移除 runbook。 它不會刪除排程或加入方案時所建立的變數。  您必須手動刪除，如果您不會使用這些其他 runbook 與資產。  

若要刪除解決方案，請執行下列步驟：

1.  從您的自動化帳戶，選取左窗格中的 [工作區]。  
2.  在 [解決方案] 頁面上，選取 [Start-Stop-VM[工作區]] 解決方案。  在**VMManagementSolution [工作區]**頁面上，從功能表中，選取**刪除**。<br><br> ![刪除 VM 管理解決方案](media/automation-solution-vm-management/vm-management-solution-delete.png)
3.  在**刪除方案**視窗中，確認您想要刪除的方案。
4.  在驗證資訊並刪除解決方案後，您可以在功能表的 [通知] 底下追蹤其進度。  您會回到**解決方案**頁面移除方案的程序啟動後。  

此程序並不會刪除自動化帳戶和 Log Analytics 工作區。  如果您不想要保留記錄分析工作區，您需要手動將它刪除。  這可以從 Azure 入口網站來完成：

1.    在 Azure 入口網站首頁畫面中，選取**記錄分析**。
2. 在**記錄分析** 窗格中，選取的工作區。
3. 選取**刪除**從工作區的 [設定] 窗格上的功能表。  
      
## <a name="next-steps"></a>後續步驟

- 若要深入了解如何建構不同的搜尋查詢，並檢閱自動化的作業記錄，記錄分析，請參閱[記錄中記錄分析搜尋](../log-analytics/log-analytics-log-searches.md)。
- 若要深入了解 Runbook 執行方式、如何監視 Runbook 作業，以及其他技術性詳細資料，請參閱[追蹤 Runbook 作業](automation-runbook-execution.md)。
- 若要了解記錄分析與資料收集來源的詳細資訊，請參閱[收集 Azure 儲存體中的資料記錄分析概觀](../log-analytics/log-analytics-azure-storage.md)。






   

