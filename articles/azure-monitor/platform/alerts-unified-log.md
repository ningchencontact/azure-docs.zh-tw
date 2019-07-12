---
title: Azure 監視器中的記錄警示
description: 當您指定的分析查詢條件符合 Azure 警示時，觸發電子郵件、通知、呼叫網站 URL (Webhook) 或自動化。
author: msvijayn
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 5/31/2019
ms.author: vinagara
ms.subservice: alerts
ms.openlocfilehash: ae35c735cffeb8cd85af1f32bb2d14ede6dc6b69
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66427425"
---
# <a name="log-alerts-in-azure-monitor"></a>Azure 監視器中的記錄警示

此文章提供記錄警示的詳細資料，記錄警示是 [Azure 警示](../../azure-monitor/platform/alerts-overview.md) 內所支援的其中一種警示類型，可讓使用者使用 Azure 的分析平台來作為基礎警示。

記錄警示包含針對 [Azure 監視器記錄](../../azure-monitor/learn/tutorial-viewdata.md)或 [Application Insights](../../azure-monitor/app/cloudservices.md#view-azure-diagnostics-events) 所建立的記錄搜尋規則。 若要深入了解其使用方式，請參閱[在 Azure 中建立記錄警示](../../azure-monitor/platform/alerts-log.md)

> [!NOTE]
> [Azure 監視器記錄](../../azure-monitor/learn/tutorial-viewdata.md)中常用的記錄資料現在也會在 Azure 監視器的計量平台上提供。 如需詳細資料檢視，請參閱[記錄的計量警示](../../azure-monitor/platform/alerts-metric-logs.md)


## <a name="log-search-alert-rule---definition-and-types"></a>記錄搜尋警示規則 - 定義和類型

Azure 警示會建立記錄搜尋規則，以自動定期執行指定的記錄查詢。  如果記錄查詢的結果符合特定準則，則會建立警示的記錄。 規則接著可自動使用[動作群組](../../azure-monitor/platform/action-groups.md)來執行一或多個動作。 可能必須擁有可建立、修改和更新記錄警示的 [Azure 監視參與者](../../azure-monitor/platform/roles-permissions-security.md)角色；再加上警示規則或警示查詢中分析目標的存取和查詢執行權限。 如果建立之使用者沒有存取權的警示規則或警示的查詢-中的所有分析目標的規則建立可能會失敗，或記錄警示規則將會執行部分的結果。

記錄搜尋規則會由下列詳細資料定義：

- **記錄查詢**。  每次引發警示規則都會執行的查詢。  此查詢所傳回的記錄會用來判斷是否要觸發警示。 分析查詢可以用於特定的 Log Analytics 工作區或 Application Insights 應用程式，甚至是跨[多個 Log Analytics 和 Application Insights 資源](../../azure-monitor/log-query/cross-workspace-query.md#querying-across-log-analytics-workspaces-and-from-application-insights)，只要使用者具備所有資源的存取和查詢權限即可。 
    > [!IMPORTANT]
    > [跨資源查詢](../../azure-monitor/log-query/cross-workspace-query.md#querying-across-log-analytics-workspaces-and-from-application-insights)中的 Application Insights 和記錄警示的記錄警示支援[Log Analytics 使用 scheduledQueryRules API 設定](../../azure-monitor/platform/alerts-log-api-switch.md)只。

    某些分析命令和組合與在記錄警示中的使用不相容；如需更多詳細資料，請檢視 [Azure 監視器中的記錄警示查詢](../../azure-monitor/platform/alerts-log-query.md)。

- **時間週期**。  指定查詢的時間範圍。 查詢只會傳回在此目前時間範圍內建立的記錄。 時間週期會限制為了查詢記錄所能擷取的資料以防濫用，並可規避記錄查詢中所使用的任何時間命令 (例如 ago)。 <br>*例如，如果時間週期設定為 60 分鐘，且查詢會在下午 1:15 執行，則只會傳回在下午 12:15 與下午 1:15 之間所建立的記錄以執行記錄查詢。現在，如果記錄查詢使用 ago (7d) 之類的時間命令，則只會對下午 12:15 與下午 1:15 之間的資料執行記錄查詢 - 彷彿只有過去 60 分鐘有資料。而不是記錄查詢中所指定的七天資料。*

- **頻率**。  指定應執行查詢的頻率。 可以是介於 5 分鐘與 24 小時之間的任何值。 應等於或小於此時間週期。  如果值大於時間週期，則您可能有遺漏記錄的風險。<br>*例如，請考慮 30 分鐘的時間週期，以及 60 分鐘的頻率。如果在 1:00 執行查詢，它會傳回 12:30 到下午 1:00 之間的記錄。下一次執行查詢就是 2:00 時，它會傳回 1:30 至 2:00 之間的記錄。1:00 和 1:30 之間建立的任何記錄一律不會評估。*

- **閾值**。  系統會評估記錄搜尋的結果，以判斷是否應該建立警示。  不同類型的記錄搜尋警示規則會有不同的閾值。

不論是 [Azure 監視器記錄](../../azure-monitor/learn/tutorial-viewdata.md)還是 [Application Insights](../../azure-monitor/app/cloudservices.md#view-azure-diagnostics-events) 的記錄搜尋規則都可以屬於兩個類型。 下列各節會詳細說明這兩個類型。

- **[結果數目](#number-of-results-alert-rules)** 。 當記錄搜尋所傳回的數目記錄超過指定數目時，系統會建立單一警示。
- **[計量測量](#metric-measurement-alert-rules)** 。  針對記錄搜尋結果中的每個物件，若其值超過指定的閾值，系統就會為其建立警示。

警示規則類型之間的差異如下所示。

- 「結果數目」  警示規則一律會建立單一警示，而「計量測量」  警示規則會為每個超過閾值的物件建立警示。
- *結果數目*警示規則會在閾值超過一次時建立警示。 *計量測量*警示規則會在閾值於特定時間間隔內超過一定次數時建立警示。

### <a name="number-of-results-alert-rules"></a>結果數目警示規則

**結果數目**警示規則會在搜尋查詢所傳回的記錄數目超過指定閾值時建立單一警示。 這種類型的警示規則適用於處理例如 Windows 事件記錄、Syslog、WebApp 回應和自訂記錄的事件。  您可能希望在建立特定的錯誤事件時，或是在特定時間週期內建立多個錯誤事件時建立警示。

**閾值**：「結果數目」警示規則的閾值會大於或小於特定值。  如果記錄搜尋所傳回的記錄數目符合此準則，則會建立警示。

若要針對單一事件警示，請將結果數目設定為大於 0，並檢查在上次執行查詢後是否發生所建立的單一事件。 有些應用程式可能會記錄不一定會產生警示的偶發錯誤。  例如，應用程式可能會重試造成錯誤事件的處理序，而下一次就會成功。  在此情況下，除非在特定時間週期內建立多個事件，否則您不會想建立警示。  

在某些情況下，您可能想在某個事件不存在時建立警示。  例如，處理序可能會記錄一般事件，表示運作正常。  如果它不在特定的時間週期內記錄一個事件，則應建立警示。  在此情況下，您會將閾值設定為**小於 1**。

#### <a name="example-of-number-of-records-type-log-alert"></a>記錄數目類型記錄警示的範例

請考慮一個情境，您想要知道您的 Web 架構應用程式何時向使用者回應程式碼 500 (亦即) 內部伺服器錯誤。 您可以建立詳細資料如下的警示規則：  

- **查詢：** 要求 | 其中 resultCode =="500"<br>
- **時間週期：** 30 分鐘<br>
- **警示頻率：** 5 分鐘<br>
- **閾值：** 大於 0<br>

然後警示會以 30 分鐘的資料，每隔 5 分鐘執行一次查詢，以尋找結果碼為 500 的任何記錄。 如果找到一筆這樣的記錄，它就會引發警示並觸發設定的動作。

### <a name="metric-measurement-alert-rules"></a>公制度量單位的警示規則

**計量測量**警示規則的值，超過指定的臨界值，指定觸發程序條件查詢中建立每個物件的警示。 不同於**的結果數目**警示規則，**計量測量**分析結果所提供的時間序列時，警示規則將會運作。 這些警示規則與**結果數目**警示規則具有下列明顯差異。

- **彙總函式**：決定要執行的計算，並可能決定要彙總的數值欄位。  例如，**count()** 會在查詢中傳回記錄數目，**avg(CounterValue)** 則會傳回 CounterValue 欄位在一段間隔內的平均值。 查詢中的彙總函式必須是命名為/稱為：AggregatedValue，並提供一個數值。 

- **群組欄位**：系統會為這個欄位中的每個執行個體建立帶有彙總值的記錄，而且每個執行個體都可產生警示。  例如，如果您想要為每部電腦產生警示，您可以使用**依電腦**。 如果警示查詢中指定了多個群組欄位，則使用者可以使用 [彙總項目]  (metricColumn) 參數來指定要使用哪個欄位來排序結果。

    > [!NOTE]
    > [彙總項目]  (metricColumn) 選項僅適用於 Application Insights 的「公制度量單位」類型記錄警示，以及[使用 scheduledQueryRules API 來設定的 Log Analytics](../../azure-monitor/platform/alerts-log-api-switch.md) 的記錄警示。

- **間隔**：定義用來彙總資料的時間間隔。  例如，如果您指定 **5 分鐘**，則系統會為群組欄位的每個執行個體建立記錄，而這些執行個體是在針對警示所指定的時間週期內以每 5 分鐘為間隔進行彙總的。

    > [!NOTE]
    > 查詢中必須使用 Bin 函式來指定間隔。 由於 bin() 可能會導致時間間隔不相等，警示會在執行階段自動使用適當時間將 bin 命令轉換為 bin_at 命令，以確保結果有固定時間點。 公制度量單位類型的警示記錄是專為與最多具有三個 bin() 命令執行個體搭配的查詢搭配運作而設計的
    
- **閾值**：計量測量警示規則的閾值是由彙總值與若干違規所定義。  如果記錄搜尋中的任何資料點超過此值，系統就會將其視為違規。  如果結果中任何物件的違規數目超過指定值，系統舊會為該物件建立警示。

[彙總項目]  或 *metricColumn* 選項設定錯誤可能會造成警示規則錯誤地觸發。 如需詳細資訊，請參閱[在公制度量單位警示規則不正確時進行疑難排解](alert-log-troubleshoot.md#metric-measurement-alert-rule-is-incorrect)。

#### <a name="example-of-metric-measurement-type-log-alert"></a>計量測量類型記錄警示的範例

假設您想要在任何電腦於過去 30 分鐘內發生三次處理器使用率超過 90% 時收到警示。  您可以建立詳細資料如下的警示規則：  

- **查詢：** Perf | where ObjectName == "Processor" and CounterName == "% Processor Time" | summarize AggregatedValue = avg(CounterValue) by bin(TimeGenerated, 5m), Computer<br>
- **時間週期：** 30 分鐘<br>
- **警示頻率：** 5 分鐘<br>
- **警示邏輯 - 條件和閾值：** 大於 90<br>
- **群組欄位 (開啟彙總)：** Computer
- **觸發警示的依據：** 違規數總計大於 2<br>

查詢會以 5 分鐘為間隔為每部電腦建立平均值。  此查詢會每隔 5 分鐘針對在先前 30 分鐘內所收集的資料來執行。 由於所選的「群組欄位 (開啟彙總)」是單欄式的「電腦」，AggregatedValue 會分割為「電腦」的各種值，而每部電腦的平均處理器使用率是以 5 分鐘的時間間隔判斷。  3 部電腦 (假設) 的範例查詢結果會如下所示。


|TimeGenerated [UTC] |Computer  |AggregatedValue  |
|---------|---------|---------|
|20xx-xx-xxT01:00:00Z     |   srv01.contoso.com      |    72     |
|20xx-xx-xxT01:00:00Z     |   srv02.contoso.com      |    91     |
|20xx-xx-xxT01:00:00Z     |   srv03.contoso.com      |    83     |
|...     |   ...      |    ...     |
|20xx-xx-xxT01:30:00Z     |   srv01.contoso.com      |    88     |
|20xx-xx-xxT01:30:00Z     |   srv02.contoso.com      |    84     |
|20xx-xx-xxT01:30:00Z     |   srv03.contoso.com      |    92     |

如果會繪製查詢結果，則會顯示如下。

![查詢結果範例](media/alerts-unified-log/metrics-measurement-sample-graph.png)

在此範例中，我們看到 3 部電腦每一部的 5 分鐘間隔，平均處理器使用率是以 5 分鐘來計算。 srv01 只有在 1:25 間隔違反 90 閾值一次。 相較之下，srv02 在 1:10、1:15 和 1:25 間隔超過 90 閾值；而 srv03 在 1:10、1:15、1:20 和 1:30 超過 90 閾值。
由於警示設定為違規超過兩次才觸發，我們看到 srv02 和 srv03 符合準則。 因此系統會為 srv02 和 srv03 建立個別警示，因為它們在多個時間間隔內違反 90% 閾值 2 次。如果「觸發警示依據：」  參數改為設定 [連續違規]  選項，則「只有」  srv03 會觸發警示，因為它在 1:10 到 1:20 的時間間隔連續違反閾值 3 次。 而 srv02 則「不會」  觸發，因為它在 1:10 到 1:15 連續違反閾值 2 次。

## <a name="log-search-alert-rule---firing-and-state"></a>記錄搜尋警示規則 - 引發與狀態

記錄搜尋警示規則適用於使用者根據組態和所使用的自訂分析查詢所決定的邏輯。 因為監視邏輯包括確切的條件或觸發警示的規則應該為何的原因被封裝在分析查詢-在每個記錄警示規則都不一樣。 Azure 警示都有特定的基礎根本原因 （或） 符合或超過臨界值條件的記錄搜尋警示規則時評估的案例很少的資訊。 因此，無狀態稱為記錄警示。 和，也會持續引發記錄警示規則，只要提供自訂的分析查詢的結果符合警示條件。 沒有警示每個開始解析，為邏輯的確切監視失敗的根本-原因會遮罩內使用者所提供的分析查詢。 那里目前沒有機制來做出推斷所要解決的根本原因的 Azure 監視器警示。

可讓我們看到相同的實用範例。 假設我們有呼叫的記錄警示規則*Contoso 記錄警示*，每個中的組態[數字的結果類型的記錄警示所提供的範例](#example-of-number-of-records-type-log-alert)-自訂警示查詢可尋找 500記錄檔中的結果碼。

- 在下午 1:05 Contoso 記錄警示執行 Azure 的警示時，記錄搜尋結果會產生零的記錄與結果程式碼 500。 由於零低於閾值，並不會引發警示。
- 在下午 1:10 Contoso 記錄警示時執行的 Azure 警示的下一個反覆項目記錄搜尋結果會提供五筆記錄與結果碼為 500。 因為五個超過閾值，而且會在引發警示的相關聯的動作，會觸發。
- 在下午 1:15 Contoso 記錄警示執行 Azure 的警示時，記錄搜尋結果會提供兩筆記錄 500 的結果碼。 因為兩個超過閾值，而且會在引發警示的相關聯的動作，會觸發。
- 現在在下午 1:20 Contoso 記錄警示時執行的 Azure 警示的下一個反覆項目記錄搜尋結果會提供一次，500 的結果為零記錄。 由於零低於閾值，並不會引發警示。

但在上述所列的情況下，在下午 1:15-Azure 警示無法判斷下午 1:10 的基礎問題仍然存在，以及是否淨新的失敗。 為使用者所提供的查詢可能會注意到較早的記錄-Azure 警示可以確定。 因為警示的邏輯會封裝在警示查詢-因此 500 的結果碼出現在下午 1:15 兩筆記錄可能會或可能不會已經出現在下午 1:10。 因此要不要粗心大意，在下午 1:15 執行 Contoso 記錄警示時，已設定的動作就會觸發一次。 現在在下午 1:20 當沒有資料錄中看到 500 的結果碼-使用 Azure 警示不能確定現在已解決在下午 1:10 點和下午 1:15 看到的 500 結果程式碼的原因，並可以自信滿滿地推算 Azure 監視器會發出警示的 500 錯誤問題不會基於相同原因s 一次。 因此 Contoso 記錄警示將不會變更為 已解決在 Azure 警示儀表板及/或送出說明解決警示的通知。 了解確切的條件或基於分析查詢中內嵌的邏輯的使用者可以改用[將警示標示為已關閉](alerts-managing-alert-states.md)視。

## <a name="pricing-and-billing-of-log-alerts"></a>記錄警示的價格和計費

在 [Azure 監視器價格](https://azure.microsoft.com/pricing/details/monitor/)頁面有說明適用於記錄警示的價格。 在 Azure 帳單中，記錄警示是以類型 `microsoft.insights/scheduledqueryrules` 搭配下列項目來表示：

- 隨著警示名稱與資源群組警示內容顯示的 Application Insights 上的記錄警示
- 所顯示 Log Analytics 上的「記錄警示」會有確切的警示名稱，以及資源群組和屬性 (當建立方式是使用 [scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) 時)

[舊版 Log Analytics API](../../azure-monitor/platform/api-alerts.md) 具有屬於 Log Analytics 儲存搜尋的警示動作和排程，且並非為適當的 [Azure 資源](../../azure-resource-manager/resource-group-overview.md)。 因此，若要啟用這類針對使用 Azure 入口網站的 Log Analytics 所建立的舊版記錄警示，而「沒有」  [切換至新 API](../../azure-monitor/platform/alerts-log-api-switch.md) 或透過[舊版 Log Analytics API](../../azure-monitor/platform/api-alerts.md)，則會在 `microsoft.insights/scheduledqueryrules` 上建立 Azure 隱藏虛擬警示規則以啟用計費。 在`microsoft.insights/scheduledqueryrules`上為計費建立的隱藏虛擬警示規則，會有如`<WorkspaceName>|<savedSearchId>|<scheduleId>|<ActionId>`所示的資源群組和警示屬性。

> [!NOTE]
> 如果出現 `<, >, %, &, \, ?, /` 等無效字元，則會在隱藏虛擬警示規則名稱以及 Azure 帳單中以 `_` 取代這類字元。

若要使用[舊版 Log Analytics API](api-alerts.md) 來移除為警示規則計費建立的隱藏 scheduleQueryRules 資源，使用者可以執行下列任一項：

- 使用者可以在 [Log Analytics 工作區上切換警示規則的 API 喜好設定](../../azure-monitor/platform/alerts-log-api-switch.md)，並且不會遺失警示規則或監視移至 Azure Resource Manager 規範 [scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules)。 因此可排除為計費建立虛擬隱藏警示規則的需求。
- 或者，如果使用者不想切換 API 喜好設定，使用者則必須使用[舊版 Log Analytics API](api-alerts.md) 來**刪除**原始排程和警示動作，或在 [Azure 入口網站刪除原始警示動作](../../azure-monitor/platform/alerts-log.md#view--manage-log-alerts-in-azure-portal)。

此外的隱藏的 scheduleQueryRules 資源建立的警示規則使用的計費[舊版 Log Analytics API](api-alerts.md)，任何修改作業，例如 PUT 將會失敗。 作為`microsoft.insights/scheduledqueryrules`型別虛擬規則是為了計費使用所建立的警示規則[舊版 Log Analytics API](api-alerts.md)。 任何警示規則修改時應使用[舊版 Log Analytics API](api-alerts.md) （或） 使用者可以[切換 API 喜好設定警示規則](../../azure-monitor/platform/alerts-log-api-switch.md)若要使用[scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules)改為。

## <a name="next-steps"></a>後續步驟

* 了解[在 Azure 警示中的記錄警示中建立](../../azure-monitor/platform/alerts-log.md)。
* 了解 [Azure 中記錄警示中的 Webhook](alerts-log-webhook.md)。
* 了解 [Azure 警示](../../azure-monitor/platform/alerts-overview.md)。
* 深入了解 [Application Insights](../../azure-monitor/app/analytics.md)。
* 深入了解 [Log Analytics](../../azure-monitor/log-query/log-query-overview.md)。