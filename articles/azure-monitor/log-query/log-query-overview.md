---
title: 分析 Azure 監視器中的 Log Analytics 資料 | Microsoft Docs
description: 您需要記錄搜尋以便從 Log Analytics 擷取任何資料。  本文說明新的記錄搜尋在 Log Analytics 中的使用方式，並且提供在建立之前需要了解的概念。
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/18/2018
ms.author: bwren
ms.openlocfilehash: d3fc44456ac4f0df2bee35300c0f40728a40cb92
ms.sourcegitcommit: b4755b3262c5b7d546e598c0a034a7c0d1e261ec
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/24/2019
ms.locfileid: "54882223"
---
# <a name="analyze-log-analytics-data-in-azure-monitor"></a>分析 Azure 監視器中的 Log Analytics 資料

Azure 監視器所收集的記錄檔資料會儲存在 Log Analytics 工作區中，此作業取決於 [Azure 資料總管](/azure/data-explorer)。 它會從各種來源收集遙測，並使用[資料總管中的查詢語言](/azure/kusto/query)來擷取和分析資料。

> [!NOTE]
> Log Analytics 先前被視為其本身在 Azure 中的服務。 現在則被視為 Azure 監視器的一部分，主要功能為使用其查詢語言進行記錄資料的儲存和分析。 先前隸屬於 Log Analytics 的功能 (例如 Windows 和 Linux 的資料收集代理程式、以視覺化方式呈現現有資料的檢視，以及會在問題發生時主動通知您的警示) 並未變更，但現已被視為 Azure 監視器的一部分。



## <a name="log-queries"></a>記錄查詢

您需要記錄查詢，以從 Log Analytics 擷取任何資料。  無論您要[分析入口網站中的資料](../log-query/portals.md)、[設定在特定條件下進行通知的警示規則](../platform/alerts-metric.md)，或是使用 [Log Analytics API](https://dev.loganalytics.io/) 擷取資料，會需要使用查詢來指定您想要的資料。  本文說明記錄查詢在 Log Analytics 中的使用方式，並且提供在建立之前應該了解的概念。



## <a name="where-log-queries-are-used"></a>記錄查詢的使用位置

您在 Log Analytics 中使用查詢的不同方式包括：

- **入口網站。** 您可以在 [Azure 入口網站](../log-query/portals.md)中執行記錄資料的互動式分析。  這可讓您在各種不同的格式和視覺效果中編輯查詢及分析結果。  
- **警示規則。** [警示規則](../platform/alerts-overview.md)會主動識別您的工作區中資料的問題。  每個警示規則是根據以固定間隔自動執行的記錄搜尋。  會檢查結果來判斷是否應該建立警示。
- **儀表板。** 您可以將任何查詢的結果釘選到 [Azure 儀表板](../learn/tutorial-logs-dashboards.md)中，如此即可以視覺化方式顯示記錄和計量資料，並選擇性地將其與其他 Azure 使用者共用。 
- **檢視。**  您可以使用[檢視設計工具](../platform/view-designer.md)，建立要包含在使用者儀表板中的資料視覺效果。  記錄查詢會在各個檢視中提供[圖格](../platform/view-designer-tiles.md)和[視覺效果部分](../platform/view-designer-parts.md)所使用的資料。  
- **匯出。**  當您將資料從 Log Analytics 工作區匯入至 Excel 或 [Power BI](../platform/powerbi.md) 時，您會建立記錄查詢以定義要匯出的資料。
- **Powershell。** 您可以從命令列或 Azure 自動化 Runbook (使用 [Get-AzureRmOperationalInsightsSearchResults](https://docs.microsoft.com/powershell/module/azurerm.operationalinsights/get-azurermoperationalinsightssearchresults?view=azurermps-4.0.0)) 執行 PowerShell 指令碼，以從 Log Analytics 擷取資料。  此 Cmdlet 需要查詢來決定要擷取的資料。
- **Log Analytics API。**  [Log Analytics 記錄搜尋 API](../platform/alerts-overview.md) 可讓任何 REST API 用戶端從工作區擷取記錄資料。  API 要求包含針對 Log Analytics 執行的查詢，來判斷要擷取的資料。

![記錄檔搜尋](media/log-query-overview/queries-overview.png)

## <a name="write-a-query"></a>撰寫查詢
Log Analytics 使用[資料總管查詢語言的版本](../log-query/get-started-queries.md)，可讓您以各種方式擷取和分析記錄資料。  您通常會從基本查詢開始，然後在您的需求變得更複雜時，使用更進階的函式。

查詢的基本結構是一個來源資料表，後面接著一系列由管道字元 `|` 隔開的運算子。  您可以將多個運算子鏈結在一起，以調整資料及執行進階函式。

例如，假設您想要尋找過去幾天具有最多錯誤事件的前十部電腦。

```Kusto
Event
| where (EventLevelName == "Error")
| where (TimeGenerated > ago(1days))
| summarize ErrorCount = count() by Computer
| top 10 by ErrorCount desc
```

或者，您想要尋找過去幾天沒有活動訊號的電腦。

```Kusto
Heartbeat
| where TimeGenerated > ago(7d)
| summarize max(TimeGenerated) by Computer
| where max_TimeGenerated < ago(1d)  
```

使用具有上一週每部電腦處理器使用率的折線圖如何？

```Kusto
Perf
| where ObjectName == "Processor" and CounterName == "% Processor Time"
| where TimeGenerated  between (startofweek(ago(7d)) .. endofweek(ago(7d)) )
| summarize avg(CounterValue) by Computer, bin(TimeGenerated, 5min)
| render timechart    
```

您可以從這些快速範例看到，不論您使用的資料種類為何，查詢的結構是類似的。  您可以將它細分為不同的步驟，從一個命令產生的資料會透過管線傳送給下一個命令。

您也可以在您的訂用帳戶內跨 Log Analytics 工作區查詢資料。

```Kusto
union Update, workspace("contoso-workspace").Update
| where TimeGenerated >= ago(1h)
| summarize dcount(Computer) by Classification 
```

## <a name="how-log-analytics-data-is-organized"></a>Log Analytics 資料的組織方式
當您建置查詢時，從判斷哪些資料表具有您要尋找的資料開始。 不同類型的資料會分別存放到各個 [Log Analytics 工作區](../learn/quick-create-workspace.md)的專用資料表中。  不同資料來源的文件包含它所建立之資料類型的名稱，及其每個屬性的描述。  許多查詢只需要單一資料表的資料，但是其他查詢可能會使用各種不同的選項以包含來自多個資料表的資料。

雖然 [Application Insights](../app/app-insights-overview.md) 會將要求、例外狀況、追蹤和使用情形等應用程式資料存放在 Log Analytics 中，但這項資料會儲存與其他記錄資料不同的分割區中。 您可以使用相同的查詢語言來存取這項資料，但必須使用 [Application Insights 主控台](../app/analytics.md)或 [Application Insights REST API](https://dev.applicationinsights.io/) 來存取此資料。 您可以使用[跨資源查詢](../log-query/cross-workspace-query.md)，將 Application Insights 資料與其他資料結合到 Log Analytics 中。


![資料表](media/log-query-overview/queries-tables.png)







## <a name="next-steps"></a>後續步驟

- 深入了解[您用來建立和編輯記錄搜尋的入口網站](../log-query/portals.md)。
- 請參閱使用新的查詢語言[撰寫查詢的教學課程](../log-query/get-started-queries.md)。
