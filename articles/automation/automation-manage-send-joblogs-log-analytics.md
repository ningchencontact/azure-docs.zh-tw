---
title: "將 Azure 自動化作業資料轉送到 OMS Log Analytics | Microsoft Docs"
description: "這篇文章示範如何將工作狀態和 Runbook 工作資料流傳送到 Microsoft Operations Management Suite Log Analytics，以提供額外的深入解析和管理。"
services: automation
documentationcenter: 
author: georgewallace
manager: carmonm
editor: tysonn
ms.assetid: c12724c6-01a9-4b55-80ae-d8b7b99bd436
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/31/2017
ms.author: magoedte
ms.openlocfilehash: 0319a7b9248dec9d7cdabba9c18a25463d94284b
ms.sourcegitcommit: 0e4491b7fdd9ca4408d5f2d41be42a09164db775
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/14/2017
---
# <a name="forward-job-status-and-job-streams-from-automation-to-log-analytics-oms"></a>從「自動化」將工作狀態和工作資料流轉送到 Log Analytics (OMS)
「自動化」可以將 Runebook 工作狀態和工作資料流傳送到您的 Microsoft Operations Management Suite (OMS) Log Analytics 工作區。 作業記錄和作業串流會顯示於 Azure 入口網站中，或是使用 PowerShell，針對個別作業，而這可讓您執行簡單的調查。 現在透過 Log Analytics，您可以：

* 自動化工作上取得深入資訊。
* 電子郵件或警示根據您的 runbook 工作狀態 （例如，失敗或擱置） 觸發程序。
* 撰寫您的工作資料流之間的進階的查詢。
* 在自動化帳戶之間進行關聯作業。
* 經過一段時間中視覺化您的工作記錄。

## <a name="prerequisites-and-deployment-considerations"></a>先決條件和部署考量
若要開始將自動化記錄傳送到 Log Analytics，您必須擁有：

* 2016 年 11 月或更新版本的 [Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/) (v2.3.0)。
* Log Analytics 工作區。 如需詳細資訊，請參閱[開始使用 Log Analytics](../log-analytics/log-analytics-get-started.md)。 
* Azure 自動化帳戶資源識別碼。


若要為您的 Azure 自動化帳戶中尋找 ResourceId:

```powershell-interactive
# Find the ResourceId for the Automation Account
Find-AzureRmResource -ResourceType "Microsoft.Automation/automationAccounts"
```

若要尋找 ResourceId 為記錄分析工作區，請執行下列 PowerShell:

```powershell-interactive
# Find the ResourceId for the Log Analytics workspace
Find-AzureRmResource -ResourceType "Microsoft.OperationalInsights/workspaces"
```

如果您有多個自動化帳戶或工作區，在上述命令中，輸出中的尋找*名稱*您需要設定，並將複製的值*ResourceId*。

如果您需要尋找自動化帳戶的 Name，可在 Azure 入口網站中，從 [自動化帳戶] 刀鋒視窗選取您的自動化帳戶，然後選取 [所有設定]。 在 [所有設定] 刀鋒視窗中，選取 [帳戶設定] 之下的 [屬性]。  在 [屬性] 刀鋒視窗中，您可以記下這些值。<br> ![自動化帳戶屬性](media/automation-manage-send-joblogs-log-analytics/automation-account-properties.png)。

## <a name="set-up-integration-with-log-analytics"></a>設定與 Log Analytics 整合

1. 在您的電腦上，從 [開始] 畫面啟動 **Windows PowerShell**。
2. 執行下列 PowerShell，並編輯的值`[your resource id]`和`[resource id of the log analytics workspace]`上一個步驟中的值。

   ```powershell-interactive
   $workspaceId = "[resource id of the log analytics workspace]"
   $automationAccountId = "[resource id of your automation account]"

   Set-AzureRmDiagnosticSetting -ResourceId $automationAccountId -WorkspaceId $workspaceId -Enabled $true
   ```

之後執行這個指令碼，您會看到新 JobLogs 或 JobStreams 正在寫入的 10 分鐘內的記錄分析中的記錄。

若要查看記錄，請在 Log Analytics 記錄搜尋中執行下列查詢：`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION""`

### <a name="verify-configuration"></a>驗證組態
若要確認您的自動化帳戶將記錄傳送到記錄分析工作區，請檢查使用下列 PowerShell 正確自動化帳戶上設定診斷：

```powershell-interactive
Get-AzureRmDiagnosticSetting -ResourceId $automationAccountId
```

在輸出中，確定：
+ 下*記錄*，值*啟用*是*True*。
+ 值*工作區識別碼*設定為記錄分析工作區的資源識別碼。

## <a name="log-analytics-records"></a>Log Analytics 記錄

從 Azure 自動化的診斷記錄分析中建立兩種類型的記錄，而會標記為**AzureDiagnostics**。 下列查詢會使用記錄分析的升級後的查詢語言。 舊版的查詢語言和新的 Azure Log Analytics 查詢語言之間的常用查詢的詳細資訊請造訪[至新 Azure 記錄分析查詢語言技舊版](https://docs.loganalytics.io/docs/Learn/References/Legacy-to-new-to-Azure-Log-Analytics-Language)

### <a name="job-logs"></a>作業記錄檔
| 屬性 | 說明 |
| --- | --- |
| TimeGenerated |Runbook 作業的執行日期和時間。 |
| RunbookName_s |Runbook 的名稱。 |
| Caller_s |起始作業的人員。 可能的值為電子郵件地址或排程作業的系統。 |
| Tenant_g | 識別呼叫端租用戶的 GUID。 |
| JobId_g |Runbook 作業之識別碼的 GUID。 |
| ResultType |Runbook 作業的狀態。 可能的值包括：<br>- New (新增)<br>- Started (已啟動)<br>- Stopped (已停止)<br>- Suspended (暫止)<br>- Failed (失敗)<br>- Completed (已完成) |
| 類別 | 資料類型的分類。 對自動化來說，該值是 JobLogs。 |
| OperationName | 指定在 Azure 中執行的作業類型。 對自動化來說，該值是 Job。 |
| 資源 | 自動化帳戶的名稱 |
| SourceSystem | Log Analytics 如何收集資料。 針對 Azure 診斷，一律為 Azure 。 |
| ResultDescription |說明 Runbook 作業的結果狀態。 可能的值包括：<br>- Job is started (工作已啟動)<br>- Job Failed (工作失敗)<br>- Job Completed |
| CorrelationId |Runbook 作業之相互關聯識別碼的 GUID。 |
| ResourceId |指定 Runbook 的 Azure 自動化帳戶資源識別碼。 |
| SubscriptionId | 自動化帳戶的 Azure 訂用帳戶識別碼 (GUID)。 |
| ResourceGroup | 自動化帳戶的資源群組名稱。 |
| ResourceProvider | MICROSOFT.AUTOMATION |
| ResourceType | AUTOMATIONACCOUNTS |


### <a name="job-streams"></a>作業串流
| 屬性 | 說明 |
| --- | --- |
| TimeGenerated |Runbook 作業的執行日期和時間。 |
| RunbookName_s |Runbook 的名稱。 |
| Caller_s |起始作業的人員。 可能的值為電子郵件地址或排程作業的系統。 |
| StreamType_s |作業串流的類型。 可能的值包括：<br>-Progress (進度)<br>- Output (輸出)<br>- Warning (警告)<br>- Error (錯誤)<br>- Debug (偵錯)<br>- Verbose |
| Tenant_g | 識別呼叫端租用戶的 GUID。 |
| JobId_g |Runbook 作業之識別碼的 GUID。 |
| ResultType |Runbook 作業的狀態。 可能的值包括：<br>- In Progress |
| 類別 | 資料類型的分類。 對自動化來說，該值是 JobStreams。 |
| OperationName | 指定在 Azure 中執行的作業類型。 對自動化來說，該值是 Job。 |
| 資源 | 自動化帳戶的名稱 |
| SourceSystem | Log Analytics 如何收集資料。 針對 Azure 診斷，一律為 Azure 。 |
| ResultDescription |包含來自 Runbook 的輸出串流。 |
| CorrelationId |Runbook 作業之相互關聯識別碼的 GUID。 |
| ResourceId |指定 Runbook 的 Azure 自動化帳戶資源識別碼。 |
| SubscriptionId | 自動化帳戶的 Azure 訂用帳戶識別碼 (GUID)。 |
| ResourceGroup | 自動化帳戶的資源群組名稱。 |
| ResourceProvider | MICROSOFT.AUTOMATION |
| ResourceType | AUTOMATIONACCOUNTS |

## <a name="viewing-automation-logs-in-log-analytics"></a>在 Log Analytics 中檢視自動化記錄檔
現在，您一開始將您的自動化作業記錄傳送到記錄分析，我們來看看您可以使用達成這些記錄檔中記錄分析。

若要查看記錄，請執行下列查詢：`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION"`

### <a name="send-an-email-when-a-runbook-job-fails-or-suspends"></a>在 Runbook 工作失敗或暫停時傳送電子郵件
其中一個最上層的客戶詢問適用於傳送電子郵件或文字時出錯 runbook 工作的能力。   

若要建立警示規則，首先針對應叫用警示的 Runbook 工作記錄，建立記錄檔搜尋。 按一下 [警示] 按鈕，以建立並設定警示規則。

1. 從 [Log Analytics 概觀] 頁面，按一下 [記錄搜尋]。
2. 查詢欄位中輸入下列搜尋建立警示的記錄搜尋查詢：`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and (ResultType == "Failed" or ResultType == "Suspended")`您也可以依分組 RunbookName 使用：`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and (ResultType == "Failed" or ResultType == "Suspended") | summarize AggregatedValue = count() by RunbookName_s`

   如果您將設定記錄檔從多個自動化帳戶或訂用帳戶為您的工作區，您可以群組您的訂用帳戶和自動化帳戶的警示。 自動化帳戶名稱可以位於 JobLogs 搜尋中的資源欄位。
1. 若要開啟 [新增警示規則] 畫面，按一下頁面頂端的 [警示]。 如需設定警示選項的詳細資訊，請參閱 [Log Analytics 中的警示](../log-analytics/log-analytics-alerts.md#alert-rules)。

### <a name="find-all-jobs-that-have-completed-with-errors"></a>尋找所有已完成但發生錯誤的工作
除了失敗的警示，您還可以尋找 Runbook 作業發生非終止錯誤的時間。 PowerShell 會在這些情況下產生的錯誤資料流，但非終止錯誤不會造成您暫停或失敗的工作。    

1. 在 Log Analytics 工作區中，按一下 [記錄搜尋]。
2. 在查詢欄位中，輸入`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobStreams" and StreamType_s == "Error" | summarize AggregatedValue = count() by JobId_g`，然後按一下 [**搜尋**] 按鈕。

### <a name="view-job-streams-for-a-job"></a>檢視工作的工作資料流
當您在偵錯工作時，您也可以查看工作資料流。 下列查詢會顯示單一作業具有 GUID 2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0 的所有資料流：   

`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobStreams" and JobId_g == "2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0" | sort by TimeGenerated asc | project ResultDescription`

### <a name="view-historical-job-status"></a>檢視歷史工作狀態
最後，您可能會想要視覺化一段時間的工作歷程記錄。 您可以使用此查詢來搜尋您的工作在一段時間的狀態。

`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and ResultType != "started" | summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h)`  
<br> ![OMS 歷史工作狀態圖表](media/automation-manage-send-joblogs-log-analytics/historical-job-status-chart.png)<br>

## <a name="summary"></a>總結
藉由將自動化作業狀態和串流資料傳送到 Log Analytics，您就能透過下列方式，更深入了解自動化作業的狀態：
+ 發生問題時通知您設定警示。
+ 使用自訂檢視和搜尋查詢，以視覺化方式檢視您的 Runbook 結果、Runbook 作業狀態，以及其他相關的關鍵指標或計量。  

Log Analytics 可以為您的自動化作業提供更高的操作可見性，並可協助更快速地處理事件。  

## <a name="next-steps"></a>後續步驟
* 若要深入了解如何建構不同的搜尋查詢，並檢閱自動化的作業記錄，記錄分析，請參閱[記錄中記錄分析搜尋](../log-analytics/log-analytics-log-searches.md)。
* 若要了解如何建立及擷取 runbook 輸出和錯誤訊息，請參閱[Runbook 輸出和訊息](automation-runbook-output-and-messages.md)。
* 若要深入了解 Runbook 執行方式、如何監視 Runbook 作業，以及其他技術性詳細資料，請參閱[追蹤 Runbook 作業](automation-runbook-execution.md)。
* 若要了解 OMS 記錄分析與資料收集來源的詳細資訊，請參閱[收集 Azure 儲存體中的資料記錄分析概觀](../log-analytics/log-analytics-azure-storage.md)。
