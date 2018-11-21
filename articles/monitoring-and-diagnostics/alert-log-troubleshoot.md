---
title: 針對 Azure 監視器中的記錄警示進行疑難排解
description: Azure 中記錄警示規則的常見問題、錯誤及解決方式。
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 10/29/2018
ms.author: vinagara
ms.component: alerts
ms.openlocfilehash: 68488788f73c9662b5d1eaa3b670f2120941defc
ms.sourcegitcommit: b62f138cc477d2bd7e658488aff8e9a5dd24d577
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/13/2018
ms.locfileid: "51616481"
---
# <a name="troubleshooting-log-alerts-in-azure-monitor"></a>針對 Azure 監視器中的記錄警示進行疑難排解  
## <a name="overview"></a>概觀
本文說明如何解決在設定 Azure 監視器內記錄警示時遇到的常見問題。 並提供有關記錄警示功能或設定常見問題集的解決方案。 

**記錄警示**一詞描述根據 [Log Analytics](../log-analytics/log-analytics-tutorial-viewdata.md) 或 [Application Insights](../application-insights/app-insights-analytics.md) 中的自訂查詢引發的警示。 閱讀[記錄警示 - 概觀](monitor-alerts-unified-log.md)以深入了解其功能、術語和類型。

> [!NOTE]
> 本文並不考慮以下情況：Azure 入口網站顯示所觸發的警示規則，以及由相關聯「動作群組」所執行的通知。 針對這類情況，請參考[動作群組](monitoring-action-groups.md)一文中的詳細資料。


## <a name="log-alert-didnt-fire"></a>記錄警示未引發

以下是 [Azure 監視器中設定的記錄警示規則](alert-log.md)狀態為何[在預期引發時](monitoring-alerts-managing-alert-states.md)未顯示的常見原因。 

### <a name="data-ingestion-time-for-logs"></a>記錄的資料擷取時間
記錄警示會根據 [Log Analytics](../log-analytics/log-analytics-tutorial-viewdata.md) 或 [Application Insights](../application-insights/app-insights-analytics.md) 定期執行您的查詢。 因為 Log Analytics 會處理來自全球各種來源及數千個客戶的數 TB 資料，所以服務容易受到多變的時間延遲影響。 如需詳細資訊，請參閱 [Log Analytics 中的資料擷取時間](../log-analytics/log-analytics-data-ingestion-time.md)。

為了減少資料擷取延遲，如果系統發現尚未擷取所需的資料，則會等候並多次重試警示查詢。 系統已設定以指數方式增加等候時間。 記錄警示只會在取得資料之後觸發，所以延遲可能是因為慢速記錄資料擷取而造成。 

### <a name="incorrect-time-period-configured"></a>設定的時間週期不正確
如[記錄警示的術語](monitor-alerts-unified-log.md#log-search-alert-rule---definition-and-types)一文所述，設定中所述的時間週期會指定查詢的時間範圍。 查詢只會傳回在此時間範圍內建立的記錄。 時間週期會限制為了查詢記錄所能擷取的資料以防濫用，並可規避記錄查詢中所使用的任何時間命令 (例如 ago)。 
*例如，如果時間週期設定為 60 分鐘，且查詢會在下午 1:15 執行，則只有在下午 12:15 與下午 1:15 之間所建立的記錄會用於記錄查詢。如果記錄查詢使用 *ago (1d)* 這類時間命令，則查詢仍然只會使用介於下午 12:15 與下午 1:15 之間的資料，因為此時間週期已設定為該間隔。*

因此，檢查組態中的時間週期是否符合您的查詢。 針對稍早所述的範例，如果記錄查詢使用如綠色標記所示的 *ago (1d)*，則應將時間週期設定為 24 小時或 1440 分鐘 (如以紅色指示的部分)，以確保查詢如預期執行。

![時間週期](./media/monitor-alerts-unified/LogAlertTimePeriod.png)

### <a name="suppress-alerts-option-is-set"></a>已設定隱藏警示選項
如[在 Azure 入口網站中建立記錄警示規則](alert-log.md#managing-log-alerts-from-the-azure-portal)一文的步驟 8 所述，記錄警示會提供 [隱藏警示] 選項，以在設定的時間量內抑制觸發和通知動作。 因此，您可能認為並未引發警示，而實際上已引發，但遭到抑制。  

![隱藏警示](./media/monitor-alerts-unified/LogAlertSuppress.png)

### <a name="metric-measurement-alert-rule-is-incorrect"></a>公制度量單位警示規則不正確
**計量度量記錄警示**是記錄警示的子類型，其具有特殊功能及受限制的警示查詢語法。 計量度量記錄警示規則會要求查詢輸出為計量時間序列；也就是，一個具有不同相等大小時間週期及對應彙總值的資料表。 此外，使用者可以選擇該資料表中除了 AggregatedValue 之外，還有其他變數。 這些變數可用來排序資料表。 

例如，假設計量度量記錄警示規則已設定如下：
- 查詢為：`search *| summarize AggregatedValue = count() by $table, bin(timestamp, 1h)`  
- 時間週期為 6 小時
- 閾值為 50
- 警示邏輯為三個連續違規
- 選擇的「彙總對象」為 $table

因為命令包含 *summarize … by* 和所提供的兩個變數 (timestamp 和 $table)，所以系統選擇 $table 來作為「彙總對象」。 它會依據 $table 欄位來排序結果資料表 (如下所示)，然後查看每個資料表類型 (例如 availabilityResults) 的多個 AggregatedValue，以了解是否有連續 3 個以上的違規。

![具有多個值的公制度量單位查詢執行](./media/monitor-alerts-unified/LogMMQuery.png)

由於「彙總對象」是 $table，因此會依 $table 資料行排序 (如以紅色標示的部分)；然後我們會聚集和尋找「彙總對象」欄位 (也就是 $table) 的類型，例如：availabilityResults 的值會被視為一個繪圖/實體 (如以橘色醒目提示的部分)。 在這個值繪圖/實體中，警示服務會檢查是否有發生三個將針對資料表值 'availabilityResults' 觸發警示的連續違規 (如以綠色標示的部分)。 同樣地，如果就 $table 的任何其他值而言，有看到三個連續違規，則也會觸發另一個警示通知；其中警示服務會自動將一個繪圖/實體中的值依時間排序 (如以橘色標示的部分)。

現在，假設公制度量單位記錄警示規則已經修改而查詢為 `search *| summarize AggregatedValue = count() by bin(timestamp, 1h)`，設定的其餘部分 (包括三個連續違規的警示邏輯) 維持與先前相同。 在此情況下，「彙總對象」選項將會預設為：timestamp。 由於查詢中僅為 summarize…by 提供一個值 (亦即 timestamp)；與稍早的範例類似，因此在執行結束時，輸出會如以下所示。 

   ![具有單一值的公制度量單位查詢執行](./media/monitor-alerts-unified/LogMMtimestamp.png)

由於「彙總對象」是 timestamp，因此會依 timestamp 資料行排序 (如以紅色標示的部分)；然後我們會依 timestamp 分組，例如：`2018-10-17T06:00:00Z` 的值會被視為一個繪圖/實體 (如以橘色醒目提示的部分)。 在這個值繪圖/實體中，警示服務將不會發現有任何連續違規發生 (因為每個 timestamp 值只有一個項目)，因此永遠不會觸發警示。 因此，在這類情況下，使用者必須 -
- 新增一個虛設變數或現有變數 (例如 $table)，以正確地使用已設定的「彙總對象」欄位進行排序
- (或者) 重新設定警示規則，以根據「違規總數」而不是適當次數使用警示邏輯
 
## <a name="log-alert-fired-unnecessarily"></a>記錄警示被不必要地引發
接下來會詳細說明在 [Azure 警示](monitoring-alerts-managing-alert-states.md)中檢視某個已設定的 [Azure 監視器中記錄警示規則](alert-log.md)，且未預期會引發該規則時，卻可能觸發該規則的一些常見原因。

### <a name="alert-triggered-by-partial-data"></a>警示被部分資料觸發
由 Analytics 提供技術支援的 Log Analytics 和 Application Insights 會受到擷取延遲和處理限制；因此，在執行所提供的記錄警示查詢時，可能會有沒有資料可用或只有部分資料可用的情況。 如需詳細資訊，請參閱 [Log Analytics 中的資料擷取時間](../log-analytics/log-analytics-data-ingestion-time.md)。

視警示規則的設定方式而定，在警示執行時，如果記錄中沒有資料或只有部分資料，可能會導致錯誤地引發警示。 在這類情況下，建議您變更警示查詢或設定。 *例如，如果將記錄警示規則設定為在來自分析查詢的結果數少於 (舉例來說) 5 時觸發；則當沒有資料 (零筆記錄) 或只有部分結果時 (一筆記錄) 時，將會觸發警示規則。不過，在發生擷取延遲之後，在 Analytics 中執行相同查詢時，具有完整資料的查詢可能會提供有 10 筆記錄的結果。*

### <a name="alert-query-output-misunderstood"></a>警示查詢輸出被誤解
就記錄警示而言，會由使用者透過分析查詢提供警示邏輯。 所提供的分析查詢可以採用各種巨量資料和數學函式來建立特定建構。 警示服務會依指定的間隔搭配適用於所指定時間週期的資料，執行客戶提供的查詢；警示服務會根據所選擇的警示類型對所提供的查詢進行細微變更，在 [設定訊號邏輯] 畫面的 [要執行的查詢] 區段中可以見證到相同的情況，如下所示：![要執行的查詢](./media/monitor-alerts-unified/LogAlertPreview.png)
 
[要執行的查詢] 區段中顯示的查詢就是記錄警示服務將執行的查詢；使用者如果想要在建立警示前先了解警示查詢輸出的可能樣貌，可以透過 [Analytics 入口網站](../log-analytics/log-analytics-log-search-portals.md)或 [Analytics API](https://docs.microsoft.com/rest/api/loganalytics/) 來執行上述查詢及時間範圍。
 
## <a name="next-steps"></a>後續步驟

* 了解 [Azure 警示中的記錄警示](monitor-alerts-unified-log.md)
* 深入了解 [Application Insights](../application-insights/app-insights-analytics.md)
* 深入了解 [Log Analytics](../log-analytics/log-analytics-overview.md)。 

