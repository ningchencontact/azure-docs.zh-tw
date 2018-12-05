---
title: 在 Log Analytics 中分析資料使用量 | Microsoft Docs
description: 在 Log Analytics 中使用 [使用量和估計成本] 儀表板來評估多少資料傳送到 Log Analytics，以及找出可能造成未預期增加的原因。
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: 74d0adcb-4dc2-425e-8b62-c65537cef270
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/11/2018
ms.author: magoedte
ms.component: ''
ms.openlocfilehash: e702e1f5eb1816b007317765e4c9a9f88bb99bfd
ms.sourcegitcommit: c8088371d1786d016f785c437a7b4f9c64e57af0
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/30/2018
ms.locfileid: "52635419"
---
# <a name="analyze-data-usage-in-log-analytics"></a>在 Log Analytics 中分析資料使用量

> [!NOTE]
> 本文說明如何在 Log Analytics 中分析資料使用量。  請參閱下列文章以了解相關資訊。
> - [藉由控制 Log Analytics 中的資料量與保留期來管理成本](log-analytics-manage-cost-storage.md)說明如何藉由變更資料保留期限來控制成本。
> - [監視使用量和估計成本](../monitoring-and-diagnostics/monitoring-usage-and-estimated-costs.md)說明如何針對不同的定價模型，檢視多項 Azure 監視功能的使用量和估計成本。 其中也會說明如何變更定價模型。

## <a name="understand-usage"></a>了解使用量

使用 Log Analytics [使用量和估計成本] 來檢閱及分析資料使用量。 這能顯示每個解決方案所收集的資料量、所保留的資料量，以及根據所擷取的資料量及超出所包含量之任何額外保留資料量所進行的成本評估。

![使用量和估計成本](media/log-analytics-usage/usage-estimated-cost-dashboard-01.png)<br>

若要更詳細地探索您的資料，請在 [使用量和估計成本] 頁面上，按一下位於任一圖表右上方的圖示。 現在您可以使用此查詢來探索更詳細的使用量。  

![[Logs] 檢視](media/log-analytics-usage/logs.png)<br>

## <a name="troubleshooting-why-usage-is-higher-than-expected"></a>針對使用量高於預期的原因進行疑難排解
較高的使用量是由下列一個或兩個原因所造成：
- 傳送到 Log Analytics 的資料比預期更多
- 有比預期更多的節點將資料傳送到 Log Analytics，或是某些節點所傳送的資料量比預期更多

讓我們看看如何深入了解這兩種原因。 

> [!NOTE]
> [使用量] 資料類型的部分欄位雖然仍位於結構描述中，但是皆已經過時，且系統已不會再填入其值。 這包括 [Computer]，以及其他與擷取相關的欄位 ([TotalBatches]、[BatchesWithinSla]、[BatchesOutsideSla]、[BatchesCapped]，以及 [AverageProcessingTimeMs])。

### <a name="data-volume"></a>資料量 
在 [使用量和估計成本] 頁面上，[每個解決方案的資料擷取] 圖表會顯示所傳送的資料總量，以及每個解決方案所傳送的資料量。 這可讓您判斷各種趨勢，例如整體資料使用量 (或特定解決方案的使用量) 是否正在增長、穩定不變或正在減少。 用來產生此內容的查詢為：

`Usage| where TimeGenerated > startofday(ago(31d))| where IsBillable == true
| summarize TotalVolumeGB = sum(Quantity) / 1024 by bin(TimeGenerated, 1d), Solution| render barchart`

請注意，子句 "where IsBillable = true" 會篩選掉來自無擷取成本之特定解決方案的資料類型。 

您可以進一步向下鑽研，以查看特定資料類型的資料趨勢，例如假設您想研究基於 IIS 記錄的資料：

`Usage| where TimeGenerated > startofday(ago(31d))| where IsBillable == true
| where DataType == "W3CIISLog"
| summarize TotalVolumeGB = sum(Quantity) / 1024 by bin(TimeGenerated, 1d), Solution| render barchart`

### <a name="nodes-sending-data"></a>傳送資料的節點

若要了解在上個月回報資料的節點數目，請使用：

`Heartbeat | where TimeGenerated > startofday(ago(31d))
| summarize dcount(ComputerIP) by bin(TimeGenerated, 1d)    
| render timechart`

若要查看每部電腦所擷取的事件計數，請使用：

`union withsource = tt *
| summarize count() by Computer | sort by count_ nulls last`

請謹慎使用此查詢，因為它相當耗費資源。 若要查看每部電腦所擷取之可計費事件的計數，請使用： 

`union withsource = tt * 
| where _IsBillable == true 
| summarize count() by Computer  | sort by count_ nulls last`

如果您想要查看有哪些可計費資料類型正在傳送資料到特定的電腦，請使用：

`union withsource = tt *
| where Computer == "*computer name*"
| where _IsBillable == true 
| summarize count() by tt | sort by count_ nulls last `

若要更深入挖掘特定資料類型的資料來源，以下是一些實用的範例查詢：

+ **安全性**解決方案
  - `SecurityEvent | summarize AggregatedValue = count() by EventID`
+ **記錄管理**解決方案
  - `Usage | where Solution == "LogManagement" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | summarize AggregatedValue = count() by DataType`
+ **Perf** 資料類型
  - `Perf | summarize AggregatedValue = count() by CounterPath`
  - `Perf | summarize AggregatedValue = count() by CounterName`
+ **Event** 資料類型
  - `Event | summarize AggregatedValue = count() by EventID`
  - `Event | summarize AggregatedValue = count() by EventLog, EventLevelName`
+ **Syslog** 資料類型
  - `Syslog | summarize AggregatedValue = count() by Facility, SeverityLevel`
  - `Syslog | summarize AggregatedValue = count() by ProcessName`
+ **AzureDiagnostics** 資料類型
  - `AzureDiagnostics | summarize AggregatedValue = count() by ResourceProvider, ResourceId`

### <a name="tips-for-reducing-data-volume"></a>降低成本的訣竅

降低所收集記錄量的一些建議包括：

| 高資料量的來源 | 如何縮減資料量 |
| -------------------------- | ------------------------- |
| 安全性事件            | 選取[一般或最小安全性事件](https://blogs.technet.microsoft.com/msoms/2016/11/08/filter-the-security-events-the-oms-security-collects/) <br> 變更安全性稽核原則為只收集所需事件。 特別檢閱下列原則是否需要收集事件： <br> - [a稽核篩選平台](https://technet.microsoft.com/library/dd772749(WS.10).aspx) <br> - [稽核登錄](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd941614(v%3dws.10))<br> - [稽核檔案系統](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772661(v%3dws.10))<br> - [稽核核心物件](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd941615(v%3dws.10))<br> - [稽核控制代碼操作](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772626(v%3dws.10))<br> - 稽核抽取式存放裝置 |
| 效能計數器       | 變更[效能計數器組態](log-analytics-data-sources-performance-counters.md)以： <br> - 減少收集頻率 <br> - 減少效能計數器的數目 |
| 事件記錄檔                 | 變更[事件記錄組態](log-analytics-data-sources-windows-events.md)以： <br> - 減少所收集的事件記錄數目 <br> - 只收集必要的事件層級。 例如，不要收集「資訊」層級事件 |
| syslog                     | 變更 [Syslog 組態](log-analytics-data-sources-syslog.md)以： <br> - 減少所收集的設施數目 <br> - 只收集必要的事件層級。 例如，不要收集「資訊」和「偵錯」層級事件 |
| AzureDiagnostics           | 變更資源記錄集合： <br> - 減少會將記錄傳送至 Log Analytics 的資源數目 <br> - 只收集必要的記錄 |
| 電腦中不需要解決方案的方案資料 | 使用[方案目標](../azure-monitor/insights/solution-targeting.md)，只從必要的電腦群組收集資料。 |

### <a name="getting-node-counts"></a>取得節點計數 

如果您是處於「每節點 (OMS)」定價層，則系統會根據您使用的節點和解決方案數目來向您收取費用，您需支付費用的見解與分析節點數目將會顯示在 [使用量和估計成本] 頁面上的資料表中。  

若要查看不同安全性節點的數目，您可以使用此查詢：

`union
(
    Heartbeat
    | where (Solutions has 'security' or Solutions has 'antimalware' or Solutions has 'securitycenter')
    | project Computer
),
(
    ProtectionStatus
    | where Computer !in~
    (
        (
            Heartbeat
            | project Computer
        )
    )
    | project Computer
)
| distinct Computer
| project lowComputer = tolower(Computer)
| distinct lowComputer
| count`

若要查看不同自動化節點的數目，請使用此查詢：

` ConfigurationData 
 | where (ConfigDataType == "WindowsServices" or ConfigDataType == "Software" or ConfigDataType =="Daemons") 
 | extend lowComputer = tolower(Computer) | summarize by lowComputer 
 | join (
     Heartbeat 
       | where SCAgentChannel == "Direct"
       | extend lowComputer = tolower(Computer) | summarize by lowComputer, ComputerEnvironment
 ) on lowComputer
 | summarize count() by ComputerEnvironment | sort by ComputerEnvironment asc`

## <a name="create-an-alert-when-data-collection-is-higher-than-expected"></a>當資料收集高於預期時建立警示
本節說明在下列情況下如何建立警示：
- 資料量超出指定的數量。
- 資料量預計會超出指定的數量。

Azure 警示支援使用搜尋查詢的[記錄警示](../monitoring-and-diagnostics/monitor-alerts-unified-log.md)。 

在過去 24 小時收集超過 100GB 的資料時，下列查詢的結果：

`union withsource = $table Usage | where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | extend Type = $table | summarize DataGB = sum((Quantity / 1024)) by Type | where DataGB > 100`

下列查詢會使用簡單的公式來預測在一天中何時會傳送超過 100GB 的資料： 

`union withsource = $table Usage | where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | extend Type = $table | summarize EstimatedGB = sum(((Quantity * 8) / 1024)) by Type | where EstimatedGB > 100`

若要針對不同的資料量顯示警示，請將查詢中的 100 變更為您要顯示警示的 GB 數。

使用[建立新記錄警示](../monitoring-and-diagnostics/alert-metric.md)中所述的步驟，可在資料收集高於預期時收到通知。

建立第一個查詢的警示時 - 在 24 小時內有超過 100GB 的資料時，請：  

- **定義警示條件**：將您的 Log Analytics 工作區指定為資源目標。
- **警示準則**：指定下列項目：
   - **訊號名稱**：選取 [自訂記錄搜尋]
   - 將 [搜尋查詢] 設定為 `union withsource = $table Usage | where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | extend Type = $table | summarize DataGB = sum((Quantity / 1024)) by Type | where DataGB > 100`
   - [警示邏輯] 為 [根據結果數目]，而 [條件] 為 [大於臨界值 0]
   - [時間週期] 為 1440 分鐘，而 [警示頻率] 設定為 60 分鐘，因為使用量資料每小時只會更新一次。
- **定義警示詳細資料**：指定下列項目：
   - 將 [名稱] 設定為「在 24 小時內大於 100GB 的資料量」
   - 將 [嚴重性] 設定為「警告」

指定現有或建立新的[動作群組](../monitoring-and-diagnostics/monitoring-action-groups.md)，以便在記錄警示符合準則時收到通知。

建立第二個查詢的警示時 - 預計在 24 小時內會有超過 100GB 的資料時，請：

- **定義警示條件**：將您的 Log Analytics 工作區指定為資源目標。
- **警示準則**：指定下列項目：
   - **訊號名稱**：選取 [自訂記錄搜尋]
   - 將 [搜尋查詢] 設定為 `union withsource = $table Usage | where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | extend Type = $table | summarize EstimatedGB = sum(((Quantity * 8) / 1024)) by Type | where EstimatedGB > 100`
   - [警示邏輯] 為 [根據結果數目]，而 [條件] 為 [大於臨界值 0]
   - [時間週期] 為 180 分鐘，而 [警示頻率] 設定為 60 分鐘，因為使用量資料每小時只會更新一次。
- **定義警示詳細資料**：指定下列項目：
   - 將 [名稱] 設定為「預計在 24 小時內大於 100GB 的資料量」
   - 將 [嚴重性] 設定為「警告」

指定現有或建立新的[動作群組](../monitoring-and-diagnostics/monitoring-action-groups.md)，以便在記錄警示符合準則時收到通知。

當您收到警示時，請使用下一節中的步驟，針對使用量高於預期的原因進行疑難排解。

## <a name="next-steps"></a>後續步驟
* 請參閱 [Log Analytics 中的記錄搜尋](log-analytics-queries.md)，以了解如何使用搜尋語言。 您可以使用搜尋查詢，對使用量資料執行額外的分析。
* 使用[建立新的記錄警示](../monitoring-and-diagnostics/alert-metric.md)中所述的步驟，可在符合搜尋條件時收到通知。
* 使用[方案目標](../azure-monitor/insights/solution-targeting.md)，只從必要的電腦群組收集資料。
* 若要設定有效的安全性事件收集原則，請檢閱 [Azure 資訊安全中心篩選原則](../security-center/security-center-enable-data-collection.md)。
* 變更[效能計數器組態](log-analytics-data-sources-performance-counters.md)。
* 若要修改事件收集設定，請檢閱[事件記錄組態](log-analytics-data-sources-windows-events.md)。
* 若要修改 syslog 收集設定，請檢閱 [Syslog 組態](log-analytics-data-sources-syslog.md)。
