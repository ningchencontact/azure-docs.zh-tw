---
title: 疑難排解在 Azure 監視器中的記錄警示 |Microsoft Docs
description: 常見的問題、 錯誤和解決在 Azure 中的記錄警示規則。
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 10/29/2018
ms.author: vinagara
ms.subservice: alerts
ms.openlocfilehash: 0c7189f1d43a114532b30b0c1aabe6f7cd4402d8
ms.sourcegitcommit: 48a41b4b0bb89a8579fc35aa805cea22e2b9922c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2019
ms.locfileid: "59578708"
---
# <a name="troubleshooting-log-alerts-in-azure-monitor"></a>針對 Azure 監視器中的記錄警示進行疑難排解  

## <a name="overview"></a>概觀

這篇文章會示範如何解決設定 Azure 監視器中的記錄警示時常見的問題。 並提供有關記錄警示功能或設定常見問題集的解決方案。 

詞彙**記錄警示**告訴您火災根據記錄檔查詢中的警示[Log Analytics 工作區](../learn/tutorial-viewdata.md)或是[Application Insights](../../azure-monitor/app/analytics.md)。 閱讀[記錄警示 - 概觀](../platform/alerts-unified-log.md)以深入了解其功能、術語和類型。

> [!NOTE]
> 本文並不考慮以下情況：Azure 入口網站顯示所觸發的警示規則，以及由相關聯「動作群組」所執行的通知。 針對這類情況，請參考[動作群組](../platform/action-groups.md)一文中的詳細資料。

## <a name="log-alert-didnt-fire"></a>記錄警示未引發

以下是 [Azure 監視器中設定的記錄警示規則](../platform/alerts-log.md)狀態為何[在預期引發時](../platform/alerts-managing-alert-states.md)未顯示的常見原因。 

### <a name="data-ingestion-time-for-logs"></a>記錄的資料擷取時間

記錄警示會根據 [Log Analytics](../learn/tutorial-viewdata.md) 或 [Application Insights](../../azure-monitor/app/analytics.md) 定期執行您的查詢。 因為 Azure 監視器處理世界各地的多個 tb 的資料從數千個客戶從不同來源，所以服務就會很容易在不同的時間延遲。 如需詳細資訊，請參閱 <<c0> [ 在 Azure 監視器記錄檔中的資料擷取時間](../platform/data-ingestion-time.md)。

為了減少資料擷取延遲，如果系統發現尚未擷取所需的資料，則會等候並多次重試警示查詢。 系統已設定以指數方式增加等候時間。 記錄警示只會在取得資料之後觸發，所以延遲可能是因為慢速記錄資料擷取而造成。 

### <a name="incorrect-time-period-configured"></a>設定的時間週期不正確

如[記錄警示的術語](../platform/alerts-unified-log.md#log-search-alert-rule---definition-and-types)一文所述，設定中所述的時間週期會指定查詢的時間範圍。 查詢只會傳回在此時間範圍內建立的記錄。 時間週期會限制為了查詢記錄所能擷取的資料以防濫用，並可規避記錄查詢中所使用的任何時間命令 (例如 *ago*)。 例如，如果時間週期設定為 60 分鐘，且查詢會在下午 1:15 執行，則只有在下午 12:15 與下午 1:15 之間所建立的記錄會用於記錄查詢。 如果記錄查詢使用 *ago (1d)* 這類時間命令，則查詢仍然只會使用介於下午 12:15 與下午 1:15 之間的資料，因為此時間週期已設定為該間隔。*

因此，檢查組態中的時間週期是否符合您的查詢。 針對稍早所述的範例，如果記錄查詢使用如綠色標記所示的 *ago (1d)*，則應將時間週期設定為 24 小時或 1440 分鐘 (如以紅色指示的部分)，以確保查詢如預期執行。

![時間週期](media/alert-log-troubleshoot/LogAlertTimePeriod.png)

### <a name="suppress-alerts-option-is-set"></a>已設定隱藏警示選項

如[在 Azure 入口網站中建立記錄警示規則](../platform/alerts-log.md#managing-log-alerts-from-the-azure-portal)一文的步驟 8 所述，記錄警示會提供 [隱藏警示] 選項，以在設定的時間量內抑制觸發和通知動作。 因此，您可能認為並未引發警示，而實際上已引發，但遭到抑制。  

![隱藏警示](media/alert-log-troubleshoot/LogAlertSuppress.png)

### <a name="metric-measurement-alert-rule-is-incorrect"></a>公制度量單位警示規則不正確

**計量度量記錄警示**是記錄警示的子類型，其具有特殊功能及受限制的警示查詢語法。 計量度量記錄警示規則會要求查詢輸出為計量時間序列；也就是，一個具有不同相等大小時間週期及對應彙總值的資料表。 此外，使用者可以選擇該資料表中除了 AggregatedValue 之外，還有其他變數。 這些變數可用來排序資料表。 

例如，假設計量度量記錄警示規則已設定如下：

- 查詢為：`search *| summarize AggregatedValue = count() by $table, bin(timestamp, 1h)`  
- 時間週期為 6 小時
- 閾值為 50
- 警示邏輯為三個連續違規
- 選擇的「彙總對象」為 $table

因為命令包含 *summarize … by* 和所提供的兩個變數 (timestamp 和 $table)，所以系統選擇 $table 作為「彙總對象」。 它會依據 $table 欄位來排序結果資料表 (如下所示)，然後查看每個資料表類型 (例如 availabilityResults) 的多個 AggregatedValue，以了解是否有連續 3 個以上的違規。

![具有多個值的公制度量單位查詢執行](media/alert-log-troubleshoot/LogMMQuery.png)

由於「彙總對象」定義於 *$table* 上，因此會依 $table 資料行排序 (如以紅色標示的部分)；然後我們會聚集和尋找「彙總對象」欄位 (也就是 $table) 的類型，例如：availabilityResults 的值會被視為一個繪圖/實體 (如以橘色醒目提示的部分)。 在這個值繪圖/實體中，警示服務會檢查是否有發生三個將針對資料表值 'availabilityResults' 觸發警示的連續違規 (如以綠色標示的部分)。 同樣地，如果就 $table 的任何其他值而言，有看到三個連續違規，則也會觸發另一個警示通知；其中警示服務會自動將一個繪圖/實體中的值依時間排序 (如以橘色標示的部分)。

現在，假設公制度量單位記錄警示規則已經修改而查詢為 `search *| summarize AggregatedValue = count() by bin(timestamp, 1h)`，設定的其餘部分 (包括三個連續違規的警示邏輯) 維持與先前相同。 在此情況下，「彙總對象」選項將會預設為：timestamp。 由於查詢中僅為 *summarize…by* 提供一個值 (亦即 *timestamp*)；與稍早的範例類似，因此在執行結束時，輸出會顯示如下。

   ![具有單一值的公制度量單位查詢執行](media/alert-log-troubleshoot/LogMMtimestamp.png)

由於「彙總對象」定義於 timestamp 上，因此會依 *timestamp* 資料行排序 (如以紅色標示的部分)；然後我們會依 timestamp 分組，例如：`2018-10-17T06:00:00Z` 的值會被視為一個繪圖/實體 (如以橘色醒目提示的部分)。 在這個值繪圖/實體中，警示服務將不會發現有任何連續違規發生 (因為每個 timestamp 值只有一個項目)，因此永遠不會觸發警示。 因此，在這類情況下，使用者必須：

- 新增一個虛設變數或現有變數 (例如 $table)，以正確地使用已設定的「彙總對象」欄位進行排序
- (或者) 重新設定警示規則，以根據「違規總數」而不是適當次數使用警示邏輯

## <a name="log-alert-fired-unnecessarily"></a>記錄警示被不必要地引發

接下來會詳細說明在 [Azure 警示](../platform/alerts-managing-alert-states.md)中檢視某個已設定的 [Azure 監視器中記錄警示規則](../platform/alerts-log.md)，且未預期會引發該規則時，卻可能觸發該規則的一些常見原因。

### <a name="alert-triggered-by-partial-data"></a>警示被部分資料觸發

由 Analytics 提供技術支援的 Log Analytics 和 Application Insights 會受到擷取延遲和處理限制；因此，在執行所提供的記錄警示查詢時，可能會有沒有資料可用或只有部分資料可用的情況。 如需詳細資訊，請參閱 < [Azure 監視器中的記錄資料擷取時間](../platform/data-ingestion-time.md)。

視警示規則的設定方式而定，在警示執行時，如果記錄中沒有資料或只有部分資料，可能會導致錯誤地引發警示。 在此種情況下，我們建議您變更警示查詢或組態。 

例如，如果將記錄警示規則設定為在來自分析查詢的結果數少於 5 時觸發，則當沒有資料 (零筆記錄) 或只有部分結果時 (一筆記錄) 時，將會觸發警示。 不過，資料擷取延遲之後，包含完整資料的相同查詢可能會提供 10 筆記錄的結果。

### <a name="alert-query-output-misunderstood"></a>警示查詢輸出被誤解

您在分析查詢中提供記錄警示的邏輯。 分析查詢可以使用各種巨量資料和數學函式。  警示服務會在指定的時間間隔，以指定之時段的資料執行查詢。 警示服務會根據選擇的警示類型，對所提供的查詢進行細微變更。 這項變更，請檢視中的 「 查詢執行中 」 一節*設定訊號邏輯*畫面上，如下所示：![要執行的查詢](media/alert-log-troubleshoot/LogAlertPreview.png)

在 [要執行的查詢] 方塊中顯示的內容就是記錄警示服務執行的內容。 如果您想要在實際建立警示之前，了解警示查詢輸出的內容，您可以執行指定的查詢，以及 [Analytics 入口網站](../log-query/portals.md)或 [Analytics API](https://docs.microsoft.com/rest/api/loganalytics/) 執行時間範圍。

## <a name="log-alert-was-disabled"></a>記錄警示已停用

以下列出因為一些原因[在 Azure 監視器中的記錄警示規則](../platform/alerts-log.md)由 Azure 監視器可能會停用。

### <a name="resource-on-which-alert-was-created-no-longer-exists"></a>資源在其建立警示不再存在

Azure 監視器中建立的記錄警示規則的目標特定的資源，例如 Azure Log Analytics 工作區、 Azure Application Insights 的應用程式和 Azure 資源。 與記錄警示服務便會執行分析查詢提供規則中指定的目標。 但是，規則建立之後，通常使用者前往從 Azure 刪除，或在 Azure 的警示規則的目標內移動。 所記錄的警示規則的目標已不再有效，執行此規則將會失敗。

在這種情況下，Azure 監視器將會停用記錄警示，並確保客戶時不會計費不必要地，規則本身不能相當類似一週的期間內持續執行。 使用者的記錄警示規則已停用由透過 Azure 監視器的確切時間，可以找出[Azure 活動記錄檔](../../azure-resource-manager/resource-group-audit.md)。 在 Azure 活動記錄檔時記錄警示規則會停用 Azure，事件會新增 Azure 活動記錄檔中。

Azure 活動記錄檔中其持續失敗，因為停用的警示規則的取樣事件如下所示。

```json
{
    "caller": "Microsoft.Insights/ScheduledQueryRules",
    "channels": "Operation",
    "claims": {
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/spn": "Microsoft.Insights/ScheduledQueryRules"
    },
    "correlationId": "abcdefg-4d12-1234-4256-21233554aff",
    "description": "Alert: test-bad-alerts is disabled by the System due to : Alert has been failing consistently with the same exception for the past week",
    "eventDataId": "f123e07-bf45-1234-4565-123a123455b",
    "eventName": {
        "value": "",
        "localizedValue": ""
    },
    "category": {
        "value": "Administrative",
        "localizedValue": "Administrative"
    },
    "eventTimestamp": "2019-03-22T04:18:22.8569543Z",
    "id": "/SUBSCRIPTIONS/<subscriptionId>/RESOURCEGROUPS/<ResourceGroup>/PROVIDERS/MICROSOFT.INSIGHTS/SCHEDULEDQUERYRULES/TEST-BAD-ALERTS",
    "level": "Informational",
    "operationId": "",
    "operationName": {
        "value": "Microsoft.Insights/ScheduledQueryRules/disable/action",
        "localizedValue": "Microsoft.Insights/ScheduledQueryRules/disable/action"
    },
    "resourceGroupName": "<Resource Group>",
    "resourceProviderName": {
        "value": "MICROSOFT.INSIGHTS",
        "localizedValue": "Microsoft Insights"
    },
    "resourceType": {
        "value": "MICROSOFT.INSIGHTS/scheduledqueryrules",
        "localizedValue": "MICROSOFT.INSIGHTS/scheduledqueryrules"
    },
    "resourceId": "/SUBSCRIPTIONS/<subscriptionId>/RESOURCEGROUPS/<ResourceGroup>/PROVIDERS/MICROSOFT.INSIGHTS/SCHEDULEDQUERYRULES/TEST-BAD-ALERTS",
    "status": {
        "value": "Succeeded",
        "localizedValue": "Succeeded"
    },
    "subStatus": {
        "value": "",
        "localizedValue": ""
    },
    "submissionTimestamp": "2019-03-22T04:18:22.8569543Z",
    "subscriptionId": "<SubscriptionId>",
    "properties": {
        "resourceId": "/SUBSCRIPTIONS/<subscriptionId>/RESOURCEGROUPS/<ResourceGroup>/PROVIDERS/MICROSOFT.INSIGHTS/SCHEDULEDQUERYRULES/TEST-BAD-ALERTS",
        "subscriptionId": "<SubscriptionId>",
        "resourceGroup": "<ResourceGroup>",
        "eventDataId": "12e12345-12dd-1234-8e3e-12345b7a1234",
        "eventTimeStamp": "03/22/2019 04:18:22",
        "issueStartTime": "03/22/2019 04:18:22",
        "operationName": "Microsoft.Insights/ScheduledQueryRules/disable/action",
        "status": "Succeeded",
        "reason": "Alert has been failing consistently with the same exception for the past week"
    },
    "relatedEvents": []
}
```

### <a name="query-used-in-log-alert-is-not-valid"></a>用於記錄警示的查詢無效

在 Azure 監視器中建立作為其組態的一部分的每個記錄警示規則必須指定警示服務會定期執行 analytics 查詢。 而分析查詢可能有正確的語法，在規則建立或更新的時間。 記錄檔中的一段時間，有時候的查詢提供警示規則可以開發語法問題，並造成啟動失敗的規則執行。 為什麼要記錄的警示規則中所提供的分析查詢可以開發錯誤一些常見原因如下：

- 若要撰寫查詢[跨多個資源執行](../log-query/cross-workspace-query.md)而一或多個資源指定，現在並不存在。
- 已分析平台，因為沒有資料流向[查詢執行會產生錯誤](https://dev.loganalytics.io/documentation/Using-the-API/Errors)提供的查詢沒有資料現狀。
- 中的變更[查詢語言](https://docs.microsoft.com/azure/kusto/query/)發生哪些命令和函式已修改過的格式。 因此稍早提供的查詢中的警示規則不再有效。

使用者應透過第一次警告的這種行為[Azure Advisor](../../advisor/advisor-overview.md)。 會加入特定的記錄警示規則上 Azure Advisor 變更使用中度影響和描述，如 「 修復您的記錄警示規則，以確保監視 」 類別的高可用性建議。 如果超過七天，在 Azure Advisor 提供建議的警示查詢中指定的記錄警示規則不會執行修正。 然後 Azure 監視器可停用記錄警示，並確保客戶時不會計費不必要地，規則本身不能相當類似一週的期間內持續執行。

使用者的記錄警示規則已停用由透過 Azure 監視器的確切時間，可以找出[Azure 活動記錄檔](../../azure-resource-manager/resource-group-audit.md)。 在 Azure 活動記錄檔，當記錄警示規則會停用 Azure-事件新增 Azure 活動記錄檔中。

## <a name="next-steps"></a>後續步驟

- 了解 [Azure 警示中的記錄警示](../platform/alerts-unified-log.md)
- 深入了解 [Application Insights](../../azure-monitor/app/analytics.md)
- 深入了解[記錄查詢](../log-query/log-query-overview.md)
