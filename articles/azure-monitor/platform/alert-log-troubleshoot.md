---
title: 疑難排解在 Azure 監視器中的記錄警示 |Microsoft Docs
description: 常見的問題、 錯誤和解決方式，在 Azure 中的記錄警示規則。
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 10/29/2018
ms.author: vinagara
ms.subservice: alerts
ms.openlocfilehash: 1c7712fc2ce55a3d22995bb119a9ee485a064903
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2019
ms.locfileid: "64683400"
---
# <a name="troubleshoot-log-alerts-in-azure-monitor"></a>疑難排解在 Azure 監視器中的記錄警示  

這篇文章會示範如何解決常見的問題，您要設定 Azure 監視器中的記錄警示時可能會發生。 它也提供的功能或組態的記錄警示的一些常見問題解決方案。 

詞彙*的記錄警示*告訴您火災根據記錄檔查詢中的警示[Azure Log Analytics 工作區](../learn/tutorial-viewdata.md)或在[Azure Application Insights](../../azure-monitor/app/analytics.md)。 深入了解功能、 詞彙和中的型別[Azure 監視器中的記錄警示](../platform/alerts-unified-log.md)。

> [!NOTE]
> 這篇文章不會視為 Azure 入口網站會顯示觸發的警示規則，而且通知不由相關聯的動作群組中執行。 這種情況下，請參閱中的詳細資料[建立和管理 Azure 入口網站中的動作群組](../platform/action-groups.md)。

## <a name="log-alert-didnt-fire"></a>記錄警示未引發

以下是一些常見原因為何之設定的狀態[Azure 監視器中的記錄警示規則](../platform/alerts-log.md)不會顯示[作為*引發*預期時](../platform/alerts-managing-alert-states.md)。 

### <a name="data-ingestion-time-for-logs"></a>記錄檔的資料擷取時間

記錄警示會定期執行您查詢是根據[Log Analytics](../learn/tutorial-viewdata.md)或是[Application Insights](../../azure-monitor/app/analytics.md)。 因為 Azure 監視器處理世界各地的多個 tb 的資料從數千個客戶從不同來源，所以服務受到不同的時間延遲。 如需詳細資訊，請參閱 < [Azure 監視器記錄檔中的資料擷取時間](../platform/data-ingestion-time.md)。

若要降低延遲，系統會等候，然後重試警示的查詢多次如果找到不尚未擷取所需的資料。 系統已設定以指數方式增加等候時間。 記錄警示會觸發之後，才將資料可用，因此延遲可能造成慢速記錄資料的擷取。 

### <a name="incorrect-time-period-configured"></a>設定的時間週期不正確

中所述的文件上[記錄警示的術語](../platform/alerts-unified-log.md#log-search-alert-rule---definition-and-types)，組態中所述的時間週期指定查詢的時間範圍。 查詢會傳回此範圍內所建立的記錄。 

時間週期會限制記錄的查詢，以避免不當使用，提取的資料和其規避任何時間命令 (例如**前**) 的記錄檔查詢中使用。 例如，如果時間週期設定為 60 分鐘，且查詢會在下午 1:15 執行，則只有在下午 12:15 與下午 1:15 之間所建立的記錄會用於記錄查詢。 如果記錄檔查詢會使用這類時間命令**前 (1d)**，查詢仍然只會使用 12:15 與下午 1:15 之間的資料，因為期間會設定為該間隔。

請檢查組態中的時間週期符合您的查詢。 如先前範例所示，如果記錄檔查詢會使用**前 (1d)** 綠色的標記，時間週期應該設為 24 小時或 1440 分鐘 （以紅色表示）。 此設定可確保查詢如預期般執行。

![時間週期](media/alert-log-troubleshoot/LogAlertTimePeriod.png)

### <a name="suppress-alerts-option-is-set"></a>已設定隱藏警示選項

中所述步驟 8 的發行項上[在 Azure 入口網站中建立的記錄警示規則](../platform/alerts-log.md#managing-log-alerts-from-the-azure-portal)，提供記錄警示**抑制警示**選項來隱藏在設定內的觸發和通知動作時間。 如此一來，您可能會認為警示未引發。 事實上，它並未引發，但已抑制。  

![隱藏警示](media/alert-log-troubleshoot/LogAlertSuppress.png)

### <a name="metric-measurement-alert-rule-is-incorrect"></a>公制度量單位警示規則不正確

*計量測量記錄警示*有特殊的功能和限制的警示查詢語法的記錄警示的子型別。 計量測量記錄警示的規則需要查詢輸出中要計量的時間序列。 也就是說，輸出會是具有不同、 相同大小的時間週期，以及對應的彙總值的資料表。 

您可以選擇以及資料表中有其他變數**AggregatedValue**。 這些變數可用來排序資料表。 

例如，假設記錄計量測量警示規則已設定為：

- 查詢 `search *| summarize AggregatedValue = count() by $table, bin(timestamp, 1h)`  
- 為 6 小時的時間週期
- 臨界值為 50
- 三個連續違規的警示邏輯
- **彙總時**選為 **$table**

因為命令包含**摘要...藉由**，並提供兩個變數 (**時間戳記**並 **$table**)，系統會選擇 **$table**如**彙總時**. 系統會排序結果資料表 **$table**欄位，如下列螢幕擷取畫面所示。 然後它會查看多個**AggregatedValue**每個資料表類型的執行個體 (例如**availabilityResults**) 以查看是否有三個或多個連續的違規。

![具有多個值的計量測量查詢執行](media/alert-log-troubleshoot/LogMMQuery.png)

因為**彙總時**上定義 **$table**，將資料排序 **$table** （以紅色表示） 的資料行。 然後我們群組，並尋找類型的**彙總時**欄位。 

例如，對於 **$table**，值**availabilityResults**就會被視為一個繪圖/實體 （以橘色表示）。 此值繪圖/實體，在警示服務會檢查三個連續的違規 （以綠色表示）。 違反觸發程序的資料表值的警示**availabilityResults**。 

同樣地，如果三個連續的違規會發生的任何其他值 **$table**，相同的動作就會觸發其他警示通知。 警示服務會自動依時間排序一個繪圖/實體 （以橘色表示） 中的值。

現在，假設記錄計量測量警示規則已修改，此查詢是`search *| summarize AggregatedValue = count() by bin(timestamp, 1h)`。 其餘的設定會保持相同之前，包括三個連續違規的警示邏輯。 **彙總時**選項在此情況下是**時間戳記**預設。 只有一個值中的查詢提供**摘要...藉由**(亦即**時間戳記**)。 如同先前的範例中，在執行結束時的輸出會如下所示。

   ![計量測量與單一值的查詢執行](media/alert-log-troubleshoot/LogMMtimestamp.png)

因為**彙總時**上定義**時間戳記**，將資料排序**時間戳記**（以紅色表示） 的資料行。 然後我們分組**時間戳記**。 例如，值`2018-10-17T06:00:00Z`就會被視為一個繪圖/實體 （以橘色表示）。 此值繪圖/實體，在警示服務會找到任何連續違規 (因為每個**時間戳記**值有只有一個項目)。 因此永遠不會觸發警示。 在此情況下，使用者必須：

- 加入虛擬變數或現有的變數 (例如 **$table**) 使用正確排序**彙總時**欄位。
- 重新設定警示的規則，以使用為基礎的警示邏輯**總缺口**改。

## <a name="log-alert-fired-unnecessarily"></a>記錄警示被不必要地引發

已設定[在 Azure 監視器中的記錄警示規則](../platform/alerts-log.md)檢視中時可能會意外觸發[Azure 警示](../platform/alerts-managing-alert-states.md)。 下列各節說明一些常見原因。

### <a name="alert-triggered-by-partial-data"></a>警示被部分資料觸發

Log Analytics 和 Application Insights 會有所延遲擷取和處理。 當您執行的記錄警示查詢時，您可能會發現使用的任何資料或只有部分資料可用。 有关详细信息，请参阅 [Azure Monitor 中的日志数据引入时间](../platform/data-ingestion-time.md)。

根據設定的警示規則的方式，misfiring 可能會發生如果沒有任何資料或記錄檔中的部分資料警示執行的時間。 在此情況下，我們建議您變更警示的查詢或設定。 

例如，如果您設定記錄的警示規則會觸發從分析查詢的結果數目小於 5 時，會觸發警示時沒有任何資料 （零的記錄） 或部分的結果 （一筆記錄）。 但是，資料擷取的延遲之後，使用完整的資料相同的查詢可能會提供 10 筆記錄的結果。

### <a name="alert-query-output-is-misunderstood"></a>受到誤解警示查詢輸出

您在分析查詢中提供記錄警示的邏輯。 分析查詢可以使用各種巨量資料和數學函式。 警示服務會執行您的查詢指定的資料一段指定的時間間隔。 警示服務會對基礎的警示類型的查詢中的稍有變更。 您可以檢視這項變更**要執行查詢**區段**設定訊號邏輯**螢幕：

![若要執行的查詢](media/alert-log-troubleshoot/LogAlertPreview.png)

**要執行查詢**方塊是執行的記錄警示服務。 如果您想要了解警示的查詢輸出可能會在建立警示之前，您可以執行上述的查詢，並透過 timespan [Analytics 入口網站](../log-query/portals.md)或[Analytics API](https://docs.microsoft.com/rest/api/loganalytics/)。

## <a name="log-alert-was-disabled"></a>記錄警示已停用

下列各節列出 Azure 監視器可能會停用的原因的一些原因[記錄警示規則](../platform/alerts-log.md)。

### <a name="resource-where-the-alert-was-created-no-longer-exists"></a>不會再建立警示的資源已存在

Azure 監視器中建立的記錄警示規則的目標特定資源的 Azure Log Analytics 工作區、 Azure Application Insights 應用程式等 Azure 資源。 記錄警示服務便會執行分析查詢提供規則中指定的目標。 但是，規則建立之後，使用者經常前往從 Azure 刪除或移動 azure-記錄警示規則的目標。 由於警示規則的目標是不再有效，執行此規則就會失敗。

在此情況下，Azure 監視器會停用記錄警示，並可確保，您不收費不必要時 （例如一週） 的可調整大小期間無法持續執行此規則。 您可以找出確切的時間，當 Azure 監視器已停用透過記錄警示[Azure 活動記錄檔](../../azure-resource-manager/resource-group-audit.md)。 Azure 活動記錄檔，在 Azure 監視器停用記錄警示規則時，會加入事件。

Azure 活動記錄檔中的下列取樣事件是因為持續失敗，所以已停用警示規則。

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

### <a name="query-used-in-a-log-alert-is-not-valid"></a>用於記錄警示的查詢無效

在 Azure 監視器中建立作為其組態的一部分的每個記錄警示規則必須指定警示的服務會定期執行分析查詢。 分析查詢可能有正確的語法，在規則建立或更新的時間。 但有時候，經過一段時間，記錄警示規則中所提供的查詢可以開發語法問題，而使規則執行失敗。 為什麼要記錄的警示規則中所提供的分析查詢可以開發錯誤一些常見原因如下：

- 若要撰寫查詢[跨多個資源執行](../log-query/cross-workspace-query.md)。 與一或多個指定的資源不存在。
- 沒有任何分析平台的資料流程。 [查詢執行會產生錯誤](https://dev.loganalytics.io/documentation/Using-the-API/Errors)因為沒有提供查詢資料。
- 中的變更[查詢語言](https://docs.microsoft.com/azure/kusto/query/)包含修訂過的格式的命令和函式。 因此稍早在警示規則中所提供的查詢不再有效。

[Azure Advisor](../../advisor/advisor-overview.md)會警告您有關此行為。 建議加入特定的記錄警示規則上 Azure 建議程式，變更在與中度影響的高可用性的分類和描述"以確保監視的修復您的記錄警示規則。 Azure 監視器警示的查詢記錄警示規則中未改正後 Azure Advisor 提供了七天的建議，如果可停用記錄警示，並確保，您不收費不必要的規則無法持續執行的可調整大小的期間 （時例如一週）。

您可以找到當 Azure 監視器停用記錄警示規則事件中尋找的確切時間[Azure 活動記錄檔](../../azure-resource-manager/resource-group-audit.md)。

## <a name="next-steps"></a>後續步驟

- 了解 [Azure 中的記錄警示](../platform/alerts-unified-log.md)。
- 深入了解 [Application Insights](../../azure-monitor/app/analytics.md)。
- 了解有关[日志查询](../log-query/log-query-overview.md)的详细信息。
