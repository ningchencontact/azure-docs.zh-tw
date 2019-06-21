---
title: 查詢 Azure 監視器中的記錄檔概觀 |Microsoft Docs
description: 一般問題的解答和相關記錄的查詢可讓您開始使用它們。
services: log-analytics
author: bwren
ms.service: log-analytics
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: bwren
ms.openlocfilehash: b395b7bccbf93b56e84d5e7b5a4ed7355eaca335
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/20/2019
ms.locfileid: "67296351"
---
# <a name="overview-of-log-queries-in-azure-monitor"></a>Azure 監視器中的記錄檔查詢的概觀
記錄檔查詢會協助您充分利用的值中的資料收集[Azure 監視器記錄檔](../platform/data-platform-logs.md)。 強大的查詢語言可讓您將資料從多個資料表聯結、 彙總大量的資料，並執行複雜的作業，以最少的程式碼。 可以回答任何問題，只要已收集支援的資料，而且您了解如何建構正確的查詢執行分析。

部分功能，例如 Azure 監視器[insights](../insights/insights-overview.md)並[解決方案](../insights/solutions-inventory.md)處理記錄資料，而不會公開您的基礎的查詢。 若要完全利用 Azure 監視器的其他功能，您應該了解如何建構查詢，以及如何使用它們來以互動方式分析 Azure 監視器記錄檔中的資料。

做為起點來了解 Azure 監視器中的記錄檔查詢中使用這篇文章。 它回答的常見問題，並提供其他詳細資料和課程提供進一步的文件的連結。

## <a name="how-can-i-learn-how-to-write-queries"></a>如何了解如何撰寫查詢？
如果您想要直接跳到項目，您就可以開始下列教學課程：

- [開始使用 Azure 監視器中的 Log Analytics](get-started-portal.md)。
- [開始使用 Azure 監視器中的記錄檔查詢](get-started-queries.md)。

一旦您有基本概念，逐步解說多項課程使用您自己的資料或從我們的示範環境中開始的資料： 

- [使用 Azure 監視器的記錄檔查詢中的字串](string-operations.md)
 
## <a name="what-language-do-log-queries-use"></a>何種語言是否有記錄查詢使用？
Azure 監視器記錄檔為基礎[Azure 資料總管](/azure/data-explorer)，並使用相同的 Kusto 查詢語言 (KQL) 寫入記錄檔查詢。 這是一個豐富的語言，設計為易於閱讀和作者，而且您應該可以開始使用最小量的指引。

請參閱[Azure 資料總管 KQL 文件](/azure/kusto/query)如 KQL 和可用的不同函式的參考上的完整文件。<br>
請參閱[開始使用 Azure 監視器中的記錄檔查詢](get-started-queries.md)快速逐步解說中，使用 Azure 監視器記錄檔中的資料的語言。
請參閱[Azure 監視器記錄檔查詢語言差異](data-explorer-difference.md)如 KQL Azure 監視器所用的版本中的細微差異。

## <a name="what-data-is-available-to-log-queries"></a>哪些資料可用於記錄的查詢？
擷取及分析記錄檔查詢中使用 Azure 監視器記錄檔中收集的所有資料都。 不同的資料來源會將其資料寫入至不同的資料表，但您可以包含多個資料表中單一查詢來分析跨多個來源的資料。 當您建置查詢時，您先判斷哪些資料表有您所尋找的資料，因此您應該至少有基本了解 Azure 監視器記錄檔中的資料結構的方式。

請參閱[來源的 Azure 監視器記錄檔](../platform/data-platform-logs.md#sources-of-azure-monitor-logs)，一份不同的資料來源填入 Azure 監視器記錄檔。<br>
請參閱[結構的 Azure 監視器記錄檔](logs-structure.md)以了解如何結構化資料。

## <a name="what-does-a-log-query-look-like"></a>沒有記錄檔查詢如下所示？
查詢可能很簡單，單一資料表的名稱，以從該資料表中擷取所有記錄：

```Kusto
Syslog
```

或者，可以篩選特定記錄、 彙總，以及以視覺化方式檢視圖表中的結果：

```
SecurityEvent
| where TimeGenerated > ago(7d)
| where EventID == 4625
| summarize count() by Computer, bin(TimeGenerated, 1h)
| render timechart 
```

針對更複雜的分析，您可能會使用聯結在一起分析結果的多個資料表中擷取資料。

```Kusto
app("ContosoRetailWeb").requests
| summarize count() by bin(timestamp,1hr)
| join kind= inner (Perf
    | summarize avg(CounterValue) 
      by bin(TimeGenerated,1hr))
on $left.timestamp == $right.TimeGenerated
```
即使您不熟悉 KQL，您應該能夠至少找出正在使用這些查詢的基本邏輯。 它們會啟動資料表的名稱，然後再加入篩選和處理該資料的多個命令。 查詢可以使用任何數目的命令，而且您可以撰寫更複雜的查詢，為您熟悉不同的 KQL 命令。

請參閱[開始使用 Azure 監視器中的記錄檔查詢](get-started-queries.md)的記錄檔查詢的教學課程，介紹的語言和常見的函式。<br>


## <a name="what-is-log-analytics"></a>什麼是 Log Analytics？
Log Analytics 是在 Azure 入口網站中寫入記錄檔查詢和以互動方式分析其結果的主要工具。 即使記錄檔查詢使用 Azure 監視器中的其他位置，您通常會撰寫並測試查詢第一次使用 Log Analytics。

您可以從 Azure 入口網站中的數個位置啟動 Log Analytics。 可用到 Log Analytics 資料的範圍取決於您啟動的方式。 請參閱[查詢範圍](scope.md)如需詳細資訊。

- 選取 **記錄檔**從**Azure 監視器**功能表或**Log Analytics 工作區**功能表。
- 選取  **Analytics**從**概觀**Application Insights 應用程式的頁面。
- 選取 **記錄檔**從 Azure 資源的功能表。

![Log Analytics](media/log-query-overview/log-analytics.png)

請參閱[開始使用 Azure 監視器中的 Log Analytics](get-started-portal.md)教學課程的逐步解說中，會介紹幾個及其功能的 Log analytics。

## <a name="where-else-are-log-queries-used"></a>記錄檔查詢用在其他地方？
除了以互動方式使用記錄檔查詢和它們在 Log Analytics 中的結果，您將在其中使用查詢的 Azure 監視器中的區域則包含下列項目：

- **警示規則。** [警示規則](../platform/alerts-overview.md)會主動識別您的工作區中資料的問題。  每個警示規則是根據以固定間隔自動執行的記錄搜尋。  會檢查結果來判斷是否應該建立警示。
- **儀表板。** 您可以將任何查詢的結果釘選到 [Azure 儀表板](../learn/tutorial-logs-dashboards.md)中，如此即可以視覺化方式顯示記錄和計量資料，並選擇性地將其與其他 Azure 使用者共用。
- **檢視。**  您可以使用[檢視設計工具](../platform/view-designer.md)，建立要包含在使用者儀表板中的資料視覺效果。  記錄查詢會在各個檢視中提供[圖格](../platform/view-designer-tiles.md)和[視覺效果部分](../platform/view-designer-parts.md)所使用的資料。  
- **匯出。**  當您從 Azure 監視器將記錄資料匯入到 Excel 或 [Power BI](../platform/powerbi.md) 時，您會建立定義要匯出之資料的記錄查詢。
- **Powershell。** 您可以從命令列或使用 Azure 自動化 runbook 來執行 PowerShell 指令碼[Get AzOperationalInsightsSearchResults](/powershell/module/az.operationalinsights/get-azoperationalinsightssearchresult)來擷取 Azure 監視器中的記錄檔資料。  此 Cmdlet 需要查詢來決定要擷取的資料。
- **Azure 監視器記錄 API。**  [Azure 監視器記錄 API](../platform/alerts-overview.md) 可讓任何 REST API 用戶端從工作區擷取記錄資料。  API 要求包含針對 Azure 監視器執行的查詢，來判斷要擷取的資料。


## <a name="next-steps"></a>後續步驟
- 逐步解說[使用 Azure 入口網站中的 Log Analytics 的教學課程](get-started-portal.md)。
- 逐步解說[撰寫查詢的教學課程](get-started-queries.md)。
