---
title: 從 Log Analytics 警示呼叫 Azure 自動化 Runbook
description: 本文提供如何從 Azure 中的 Log Analytics 警示叫用自動化 Runbook 的概觀。
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 5a3b14bd8409226772d210f60dadd525960f7890
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/16/2018
---
# <a name="call-an-azure-automation-runbook-from-a-log-analytics-alert"></a>從 Log Analytics 警示呼叫 Azure 自動化 Runbook

您可以在 Azure Log Analytics 中設定警示，以在結果符合您的準則時建立警示記錄。 然後，該警示可以在嘗試自動補救問題時，自動執行 Azure 自動化 Runbook。 

例如，警示可能表示處理器使用率出現長時間的突然增加。 或者可能表示對商務應用程式功能非常重要的應用程式程序失敗。 然後，Runbook 可以在 Windows 事件記錄中寫入對應的事件。  

有兩個選項可用來在警示設定中呼叫 Runbook：

* 使用 Webhook。
   * 如果您的 Log Analytics 工作區並未連結到自動化帳戶，這是唯一可用的選項。
   * 如果您已有連結至 Log Analytics 工作區的自動化帳戶，還是可以使用此選項。  

* 直接選取 Runbook。
   * 只有在 Log Analytics 工作區連結至自動化帳戶時，此選項才可供使用。

## <a name="calling-a-runbook-by-using-a-webhook"></a>使用 Webhook 呼叫 Runbook

您可以使用 Webhook 在 Azure 自動化中，透過單一 HTTP 要求啟動特定的 Runbook。 在設定 [Log Analytics 警示](../log-analytics/log-analytics-alerts.md#alert-rules)以使用 Webhook 呼叫 Runbook 作為警示動作前，您必須為透過此方法呼叫的 Runbook [建立 Webhook](automation-webhooks.md#creating-a-webhook)。 請記得記錄 Webhook URL，如此您就可以在設定警示規則時加以參考。   

## <a name="calling-a-runbook-directly"></a>直接呼叫 Runbook

您可以在 Log Analytics 工作區中安裝及設定「自動化及控制」供應項目。 當您為警示設定 Runbook 動作選項時，您可以從 [選取 Runbook] 下拉式清單檢視所有 Runbook，並選取您想要回應警示而執行的特定 Runbook。 選取的 Runbook 可以在 Azure 工作區中或在混合式 Runbook 背景工作上執行。 

使用 Runbook 選項建立警示之後，系統會為 Runbook 建立一個 Webhook。 如果您移至自動化帳戶並開啟所選 Runbook 的 Webhook 窗格，您可以看到 Webhook。 

如果您刪除警示，將不會刪除 Webhook。 這不成問題。 如果 Webhook 變成被遺棄的項目，您最終要手動刪除該項目，才能維護組織的自動化帳戶。  

## <a name="characteristics-of-a-runbook"></a>Runbook 的特性

您必須先了解這兩種從 Log Analytics 警示呼叫 Runbook 的方法所具有的特性，再設定警示規則。 

警示資料會以 JSON 格式儲存在名為 **SearchResults** 的單一屬性中。 此格式適用於具有標準承載的 Runbook 和 Webhook 動作。 對於具有自訂承載的 Webhook 動作 (在 **RequestBody** 中包含 **IncludeSearchResults:True**)，此屬性為 **SearchResults**。

您必須擁有名為 **WebhookData** (也就是**物件**類型) 的 Runbook 輸入參數。 它可以是強制性或選擇性。 警示會使用此輸入參數將搜尋結果傳遞給 Runbook。

```powershell
param  
    (  
    [Parameter (Mandatory=$true)]  
    [object] $WebhookData  
    )
```
您還必須有將 **WebhookData** 轉換成 PowerShell 物件的程式碼。

```powershell
$SearchResult = (ConvertFrom-Json $WebhookData.RequestBody).SearchResult.value
```

**$SearchResult** 是物件的陣列。 每個物件都包含某個搜尋結果的值欄位。


## <a name="example-walkthrough"></a>範例逐步解說

下列圖形化 Runbook 範例示範此程序的運作方式。 它會啟動 Windows 服務。

![用於啟動 Windows 服務的圖形化 Runbook](media/automation-invoke-runbook-from-omsla-alert/automation-runbook-restartservice.png)

Runbook 有一個 [物件] 類型且名為 **WebhookData** 的輸入參數。 它包含從警示傳遞的 Webhook 資料 (含有 **SearchResult**)。

![Runbook 輸入參數](media/automation-invoke-runbook-from-omsla-alert/automation-runbook-restartservice-inputparameter.png)

在此範例中，我們在 Log Analytics 中建立了兩個自訂欄位：**SvcDisplayName_CF** 和 **SvcState_CF**。 這些欄位會從系統事件記錄中寫入的事件，擷取服務顯示名稱和服務狀態 (也就是，執行中或已停止)。 接著，我們使用下列搜尋查詢建立了警示規則，以便偵測列印多工緩衝處理器服務在 Windows 系統上停止的時間：

`Type=Event SvcDisplayName_CF="Print Spooler" SvcState_CF="stopped"` 

它可以是任何您想使用的服務。 在此範例中，我們會參考其中一個隨附於 Windows 作業系統中預先存在的服務。 警示動作會設定為執行此範例中使用的 Runbook，並在混合式 Runbook 背景工作上執行，而會在目標系統上啟用此兩種設定。   

Runbook 程式碼活動 [從 LA 取得服務名稱] 會將 JSON 格式的字串轉換成物件型別，並依項目 **SvcDisplayName_CF** 進行篩選。 它會擷取 Windows 服務的顯示名稱，並將此值傳遞給下一個活動，該活動會先確認服務已停止，然後再嘗試重新啟動它。 **SvcDisplayName_CF** 是在 Log Analytics 中建立的[自訂欄位](../log-analytics/log-analytics-custom-fields.md)，用來示範此範例。

```powershell
$SearchResult = (ConvertFrom-Json $WebhookData.RequestBody).SearchResult.value
$SearchResult.SvcDisplayName_CF  
```

服務停止時，Log Analytics 中的警示規則會偵測相符項目、觸發 Runbook 並將警示內容傳送至 Runbook。 Runbook 會嘗試確認服務已停止。 因此，Runbook 會嘗試重新啟動服務，確認它已正確地啟動，然後顯示結果。     

或者，如果您的自動化帳戶並未連結至 Log Analytics 工作區，您可以使用 Webhook 動作來設定警示規則。 Webhook 動作會觸發 Runbook。 它也會遵循先前所述的指導方針來設定 Runbook，以轉換 JSON 格式的字串並依 **SearchResult** 進行篩選。    

>[!NOTE]
> 如果您的工作區已升級為[新的 Log Analytics 查詢語言](../log-analytics/log-analytics-log-search-upgrade.md)，則 Webhook 承載已變更。 如需格式的詳細資訊，請參閱 [Azure Log Analytics REST API](https://aka.ms/loganalyticsapiresponse)。

## <a name="next-steps"></a>後續步驟

* 若要深入了解 Log Analytics 的警示以及如何建立，請參閱 [Log Analytics 中的警示](../log-analytics/log-analytics-alerts.md)。

* 若要了解如何使用 Webhook 來觸發 Runbook，請參閱 [Azure 自動化 Webhook](automation-webhooks.md)。
