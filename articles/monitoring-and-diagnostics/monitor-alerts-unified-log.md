---
title: Azure 監視器中的記錄警示
description: 當您指定的分析查詢條件符合 Azure 警示時，觸發電子郵件、通知、呼叫網站 URL (Webhook) 或自動化。
author: msvijayn
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: vinagara
ms.component: alerts
ms.openlocfilehash: aab3c843493048291583bea111a52fe3356dc0f0
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/11/2018
ms.locfileid: "35264369"
---
# <a name="log-alerts-in-azure-monitor---alerts"></a>Azure 監視器中的記錄警示 - 警示 
本文提供記錄警示的詳細資料，記錄警示是新的 [Azure 警示](monitoring-overview-unified-alerts.md) 內所支援的其中一種警示類型，可讓使用者使用 Azure 的分析平台來作為基礎警示。 如需計量警示使用記錄的詳細資訊，請參閱[近乎即時的度量警示](monitoring-near-real-time-metric-alerts.md)。


記錄警示包含針對 [Azure Log Analytics](../log-analytics/log-analytics-tutorial-viewdata.md) 或 [Application Insights](../application-insights/app-insights-cloudservices.md#view-azure-diagnostic-events) 所建立的記錄搜尋規則


## <a name="log-search-alert-rule---definition-and-types"></a>記錄搜尋警示規則 - 定義和類型

Azure 警示會建立記錄搜尋規則，以自動定期執行指定的記錄查詢。  如果記錄查詢的結果符合特定準則，則會建立警示的記錄。 規則接著可自動使用[動作群組](monitoring-action-groups.md)來執行一或多個動作。 

記錄搜尋規則會由下列詳細資料定義：
- **記錄查詢**。  每次引發警示規則都會執行的查詢。  此查詢所傳回的記錄將是用來判斷是否要建立警示。 *Azure Application Insights* 查詢也可以包含[跨應用程式呼叫](https://dev.applicationinsights.io/ai/documentation/2-Using-the-API/CrossResourceQuery)，但前提是使用者具有外部應用程式的存取權限。 

    > [!IMPORTANT]
    > [Application Insights 的跨應用程式查詢](https://dev.applicationinsights.io/ai/documentation/2-Using-the-API/CrossResourceQuery)支援目前為預覽版 - 其功能僅限 2 個以上的應用程式使用，而且使用者體驗可能會有變動。 Azure 警示目前**不支援**使用[跨工作區查詢](https://dev.loganalytics.io/oms/documentation/3-Using-the-API/CrossResourceQuery)和 [Log Analytics 的跨資源查詢](../log-analytics/log-analytics-cross-workspace-search.md)方式。

- **時間週期**。  指定查詢的時間範圍。 查詢只會傳回在此目前時間範圍內建立的記錄。 時間週期會限制為了查詢記錄所能擷取的資料以防濫用，並可規避記錄查詢中所使用的任何時間命令 (例如 ago)。 <br>*例如，如果時間週期設定為 60 分鐘，且查詢會在下午 1:15 執行，則只會傳回在下午 12:15 與下午 1:15 之間所建立的記錄以執行記錄查詢。現在，如果記錄查詢使用 ago (7d) 之類的時間命令，則只會對下午 12:15 與下午 1:15 之間的資料執行記錄查詢 - 彷彿只有過去 60 分鐘有資料。而不是記錄查詢中所指定的七天資料。*
- **頻率**。  指定應執行查詢的頻率。 可以是介於 5 分鐘與 24 小時之間的任何值。 應等於或小於此時間週期。  如果值大於時間週期，則您可能有遺漏記錄的風險。<br>*例如，請考慮 30 分鐘的時間週期，以及 60 分鐘的頻率。如果在 1:00 執行查詢，它會傳回 12:30 到下午 1:00 之間的記錄。下一次執行查詢就是 2:00 時，它會傳回 1:30 至 2:00 之間的記錄。1:00 和 1:30 之間建立的任何記錄一律不會評估。*
- **閾值**。  系統會評估記錄搜尋的結果，以判斷是否應該建立警示。  不同類型的記錄搜尋警示規則會有不同的閾值。

不論是 [Azure Log Analytics](../log-analytics/log-analytics-tutorial-viewdata.md) 還是 [Application Insights](../application-insights/app-insights-cloudservices.md#view-azure-diagnostic-events) 的記錄搜尋規則都可以屬於兩個類型。 下列各節會詳細說明這兩個類型。

- **[結果數目](#number-of-results-alert-rules)**。 當記錄搜尋所傳回的數目記錄超過指定數目時，系統會建立單一警示。
- **[計量測量](#metric-measurement-alert-rules)**。  針對記錄搜尋結果中的每個物件，若其值超過指定的閾值，系統就會為其建立警示。

警示規則類型之間的差異如下所示。

- 「結果數目」警示規則一律會建立單一警示，而「計量測量」警示規則會為每個超過閾值的物件建立警示。
- *結果數目*警示規則會在閾值超過一次時建立警示。 *計量測量*警示規則會在閾值於特定時間間隔內超過一定次數時建立警示。

### <a name="number-of-results-alert-rules"></a>結果數目警示規則
**結果數目**警示規則會在搜尋查詢所傳回的記錄數目超過指定閾值時建立單一警示。 這種類型的警示規則適用於處理例如 Windows 事件記錄、Syslog、WebApp 回應和自訂記錄的事件。  您可能希望在建立特定的錯誤事件時，或是在特定時間週期內建立多個錯誤事件時建立警示。

**閾值**：結果數目警示規則的閾值會大於或小於特定值。  如果記錄搜尋所傳回的記錄數目符合此準則，則會建立警示。

若要針對單一事件警示，請將結果數目設定為大於 0，並檢查在上次執行查詢後是否發生所建立的單一事件。 有些應用程式可能會記錄不一定會產生警示的偶發錯誤。  例如，應用程式可能會重試造成錯誤事件的處理序，而下一次就會成功。  在此情況下，除非在特定時間週期內建立多個事件，否則您不會想建立警示。  

在某些情況下，您可能想在某個事件不存在時建立警示。  例如，處理序可能會記錄一般事件，表示運作正常。  如果它不在特定的時間週期內記錄一個事件，則應建立警示。  在此情況下，您會將閾值設定為**小於 1**。

#### <a name="example"></a>範例
請考慮一個情境，您想要知道您的 Web 架構應用程式何時向使用者回應程式碼 500 (亦即) 內部伺服器錯誤。 您可以建立詳細資料如下的警示規則：  
- **查詢：** 要求 | 其中 resultCode =="500"<br>
- **時間週期：** 30 分鐘<br>
- **警示頻率：** 5 分鐘<br>
- **臨界值︰** 大於 0<br>

然後警示會以 30 分鐘的資料，每隔 5 分鐘執行一次查詢，以尋找結果碼為 500 的任何記錄。 如果找到一筆這樣的記錄，它就會引發警示並觸發設定的動作。

### <a name="metric-measurement-alert-rules"></a>公制度量單位的警示規則

- **計量測量**警示規則會針對查詢中其值超過指定閾值的每個物件建立警示。  這些警示規則與**結果數目**警示規則具有下列明顯差異。
- **彙總函式**：決定要執行的計算，並可能決定要彙總的數值欄位。  例如，**count()** 會在查詢中傳回記錄數目，**avg(CounterValue)** 則會傳回 CounterValue 欄位在一段間隔內的平均值。 查詢中的彙總函式必須是名為/稱為：AggregatedValue，並提供數值。 
- **群組欄位**：系統會為這個欄位中的每個執行個體建立帶有彙總值的記錄，而且每個執行個體都可產生警示。  例如，如果您想要為每部電腦產生警示，您可以使用**依電腦** 

    > [!NOTE]
    > 對於以 Application Insights 為基礎的計量測量警示規則，您可以指定用來分組資料的欄位。 若要這樣做，請使用規則定義中的 [彙總依據] 選項。   
    
- **間隔**：定義用來彙總資料的時間間隔。  例如，如果您指定 **5 分鐘**，則系統會為群組欄位的每個執行個體建立記錄，而這些執行個體是在針對警示所指定的時間週期內以每 5 分鐘為間隔進行彙總的。

    > [!NOTE]
    > 查詢中必須使用 Bin 函式來指定間隔。 由於 bin() 可能會導致時間間隔不相等，警示會在執行階段自動使用適當時間將 bin 命令轉換為 bin_at 命令，以確保結果有固定時間點
    
- **閾值**：計量測量警示規則的閾值是由彙總值與若干違規所定義。  如果記錄搜尋中的任何資料點超過此值，系統就會將其視為違規。  如果結果中任何物件的違規數目超過指定值，系統舊會為該物件建立警示。

#### <a name="example"></a>範例
假設您想要在任何電腦於過去 30 分鐘內發生三次處理器使用率超過 90% 時收到警示。  您可以建立詳細資料如下的警示規則：  

- **查詢：** Perf | where ObjectName == "Processor" and CounterName == "% Processor Time" | summarize AggregatedValue = avg(CounterValue) by bin(TimeGenerated, 5 m), Computer<br>
- **時間週期：** 30 分鐘<br>
- **警示頻率：** 5 分鐘<br>
- **彙總值︰** 大於 90<br>
- **警示觸發依據︰** 違規總數大於 2<br>

查詢會以 5 分鐘為間隔為每部電腦建立平均值。  此查詢會每隔 5 分鐘針對在先前 30 分鐘內所收集的資料來執行。  三部電腦的資料範例如下所示。

![查詢結果範例](./media/monitor-alerts-unified/metrics-measurement-sample-graph.png)

在此範例中，系統會為 srv02 和 srv03 建立個別警示，因為它們在時間週期內違反 90% 閾值 3 次。  如果**警示觸發依據︰** 變更為**連續**，則系統只會為 srv03 建立警示，因為它違反了 3 個連續取樣的閾值。


## <a name="log-search-alert-rule---creation-and-modification"></a>記錄搜尋警示規則 - 建立和修改

記錄警示及其組成記錄搜尋警示規則可透過下列項目來檢視、建立或修改：
- Azure 入口網站
- REST API (包括透過 PowerShell)
- Azure Resource Manager 範本

### <a name="azure-portal"></a>Azure 入口網站
由於[新 Azure 警示](monitoring-overview-unified-alerts.md)的推出，現在使用者可以在 Azure 入口網站中透過單一位置和類似的步驟管理所有類型的警示。 深入了解如何[使用新的 Azure 警示](monitor-alerts-unified-usage.md)。

此外，使用者可在 Azure 中選擇的 Analytics平台中完成理想的查詢後，*藉著儲存查詢來匯入它們以便在警示中使用*。 要遵循的步驟如下：
- 針對 Application Insights：前往 Analytics 入口網站，驗證查詢及其結果。 然後使用唯一名稱儲存到 [共用的查詢]中。
- 針對 Log Analytics：前往記錄搜尋，驗證查詢及其結果。 然後使用唯一名稱儲存到任何類別中。

然後，[在警示中建立記錄警示時](monitor-alerts-unified-usage.md)，您會看到儲存的查詢列在 [記錄 (已儲存的查詢)] 訊號類型；如下列範例所示：![已儲存的查詢匯入至警示](./media/monitor-alerts-unified/AlertsPreviewResourceSelectionLog-new.png)

> [!NOTE]
> 使用 [記錄 (已儲存的查詢)] 會造成匯入到警示。 因此，在 Analytics 中完成後的任何變更不會反映到記錄搜尋警示規則中，反之亦然。

### <a name="rest-apis"></a>REST API
針對記錄警示所提供的 API 是 RESTful，可透過 Azure Resource Manager REST API 來存取。 因此可以透過 PowerShell 以及其他選項來存取，以利用 API。

如需詳細資訊以及 REST API 的使用範例，請參閱：
- [Log Analytics 警示 REST API](../log-analytics/log-analytics-api-alerts.md) - 以建立和管理 Azure Log Analytics 的記錄搜尋警示規則
- [Azure 監視器排程的查詢規則 REST API](https://docs.microsoft.com/en-us/rest/api/monitor/scheduledqueryrules/) - 以建立和管理 Azure Application Insights 的記錄搜尋警示規則

### <a name="azure-resource-manager-template"></a>Azure Resource Manager 範本
使用者也可以使用 [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) 所提供的彈性來建立和更新資源 - 用於建立或更新記錄警示。

如需詳細資訊以及 Resource Manager 範本的使用範例，請參閱：
- [已儲存的搜尋和警示管理](monitor-alerts-unified-log-template.md#managing-log-alert-on-log-analytics)，適用於以 Azure Log Analytics 為基礎的記錄警示
- [排程的查詢規則](monitor-alerts-unified-log-template.md#managing-log-alert-on-application-insights)，適用於以 Azure Application Insights 為基礎的記錄警示
 

## <a name="next-steps"></a>後續步驟
* 了解 [Azure 中的記錄警示](monitor-alerts-unified-log-webhook.md)。
* 了解新的 [Azure 警示](monitoring-overview-unified-alerts.md)。
* 深入了解 [Application Insights](../application-insights/app-insights-analytics.md)。
* 深入了解 [Log Analytics](../log-analytics/log-analytics-overview.md)。    
