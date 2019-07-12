---
title: Azure 警示中的記錄警示 Webhook 動作
description: 這篇文章說明如何使用 Log Analytics 工作區或 Application Insights 中建立的記錄警示規則的警示如何將資料推送作為 HTTP webhook，以及可能的不同自訂的詳細資料。
author: msvijayn
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: vinagara
ms.subservice: alerts
ms.openlocfilehash: 6aa007c621e76cb0c188a7dab6279fd9e387b2b3
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2019
ms.locfileid: "67705180"
---
# <a name="webhook-actions-for-log-alert-rules"></a>Webhook 動作記錄警示規則
當[在 Azure 中建立記錄警示](alerts-log.md)，您可以選擇[設定使用動作群組](action-groups.md)執行一或多個動作。 這篇文章說明可用的不同的 webhook 動作，並示範如何設定自訂 JSON 型 webhook。

> [!NOTE]
> 您也可以使用[常見的警示結構描述](https://aka.ms/commonAlertSchemaDocs)的 webhook 整合。 常見的警示結構描述的好處是能夠在 Azure 監視器中的所有警示服務上有單一的可延伸的整合式警示承載。 [深入了解常見的警示的結構描述定義。](https://aka.ms/commonAlertSchemaDefinitions)

## <a name="webhook-actions"></a>Webhook 動作

使用 webhook 動作，您可以叫用外部處理序透過單一 HTTP POST 要求。 呼叫的服務應支援 webhook，並決定如何使用它所接收的任何裝載。

Webhook 動作需要下表中的屬性。

| 屬性 | 描述 |
|:--- |:--- |
| **Webhook URL** |Webhook 的 URL。 |
| **自訂 JSON 承載** |若要在警示建立期間選擇此選項時，使用 webhook 傳送的自訂承載。 如需詳細資訊，請參閱 <<c0> [ 管理記錄警示](alerts-log.md)。|

> [!NOTE]
> **檢視 Webhook**按鈕旁**包含自訂 JSON 承載的 webhook**記錄警示會顯示範例的 webhook 承載所提供的自訂選項。 它不包含實際資料，但代表用於記錄警示的 JSON 結構描述。 

Webhook 包括 URL 和資料傳送至外部服務的 JSON 格式的承載。 根據預設，承載會包含下表中的值。 您可以選擇用自己的自訂承載來取代此承載。 在此情況下，在您的自訂承載中包含其值使用資料表中的每個參數的變數。


| 參數 | 變數 | 描述 |
|:--- |:--- |:--- |
| *AlertRuleName* |#alertrulename |警示規則的名稱。 |
| *Severity* |#severity |為引發的記錄警示設定的嚴重性。 |
| *AlertThresholdOperator* |#thresholdoperator |警示的規則，可使用大於或小於臨界值運算子比。 |
| *AlertThresholdValue* |#thresholdvalue |警示規則的臨界值。 |
| *LinkToSearchResults* |#linktosearchresults |建立警示的查詢傳回的記錄分析入口網站的連結。 |
| *ResultCount* |#searchresultcount |搜尋結果中的記錄數目。 |
| *搜尋間隔結束時間* |#searchintervalendtimeutc |結束時間-utc 時區，查詢格式 mm/dd/yyyy hh: mm: ss AM/PM。 |
| *搜尋間隔* |#searchinterval |警示規則，以格式 hh: mm： 時間範圍。 |
| *搜尋間隔開始時間* |#searchintervalstarttimeutc |查詢時間-utc 時區，開始格式 mm/dd/yyyy hh: mm: ss AM/PM。 
| *SearchQuery* |#searchquery |警示規則所使用的記錄檔搜尋查詢。 |
| *SearchResults* |"IncludeSearchResults": true|如果，查詢所傳回為 JSON 資料表，受限於前 1,000 筆記錄，記錄"IncludeSearchResults": true 新增為最上層屬性的自訂 JSON webhook 定義中。 |
| *警示類型*| #alerttype | 記錄警示規則設定為類型[計量測量](alerts-unified-log.md#metric-measurement-alert-rules)或是[的結果數目](alerts-unified-log.md#number-of-results-alert-rules)。|
| *WorkspaceID* |#workspaceid |Log Analytics 工作區的識別碼。 |
| *應用程式識別碼* |#applicationid |識別碼的 Application Insights 應用程式。 |
| *訂用帳戶識別碼* |#subscriptionid |使用您 Azure 訂用帳戶識別碼。 

> [!NOTE]
> *LinkToSearchResults*傳遞參數，例如*SearchQuery*，*搜尋間隔開始時間*，和*搜尋間隔結束時間*azure url在 [分析] 區段中檢視的網站。 Azure 入口網站的 URI 大小上限為大約 2000 個字元。 在入口網站將會*不*開啟警示中提供，如果參數值超過限制的連結。 您可以手動輸入詳細資料，以在 Analytics 入口網站中檢視結果。 或者，您可以使用[Application Insights Analytics REST API](https://dev.applicationinsights.io/documentation/Using-the-API)或[Log Analytics REST API](/rest/api/loganalytics/)來以程式設計方式擷取結果。 

例如，您可以指定下列自訂承載，其中包含稱為 text  的單一參數。 此 webhook 所呼叫的服務需要此參數。

```json

    {
        "text":"#alertrulename fired with #searchresultcount over threshold of #thresholdvalue."
    }
```
傳送到 webhook 時，此範例承載會解析成如下所示：

```json
    {
        "text":"My Alert Rule fired with 18 records over threshold of 10 ."
    }
```
因為必須指定自訂的 webhook 中的所有變數內的 JSON 機箱，像是 「 #searchinterval"，結果的 webhook 也有在機箱內的變數資料像是"00: 05:00。 」

若要在自訂承載中包含搜尋結果，請確認**IncludeSearchResults**會設定為 JSON 承載中的最上層屬性。 

## <a name="sample-payloads"></a>承載範例
本節說明記錄警示 webhook 範例承載。 範例承載會包含範例，當裝載為標準和自訂。

### <a name="standard-webhook-for-log-alerts"></a>記錄警示標準 webhook 
這兩個範例有虛擬承載，只有兩個資料行和兩個資料列。

#### <a name="log-alert-for-log-analytics"></a>Log Analytics 的記錄警示
下列的範例承載是標準 webhook 動作*不含自訂 JSON 選項*用基礎 Log Analytics 的警示：

```json
{
    "SubscriptionId":"12345a-1234b-123c-123d-12345678e",
    "AlertRuleName":"AcmeRule",
    "SearchQuery":"Perf | where ObjectName == \"Processor\" and CounterName == \"% Processor Time\" | summarize AggregatedValue = avg(CounterValue) by bin(TimeGenerated, 5m), Computer",
    "SearchIntervalStartTimeUtc": "2018-03-26T08:10:40Z",
    "SearchIntervalEndtimeUtc": "2018-03-26T09:10:40Z",
    "AlertThresholdOperator": "Greater Than",
    "AlertThresholdValue": 0,
    "ResultCount": 2,
    "SearchIntervalInSeconds": 3600,
    "LinkToSearchResults": "https://portal.azure.com/#Analyticsblade/search/index?_timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
    "Description": "log alert rule",
    "Severity": "Warning",
    "SearchResult":
        {
        "tables":[
                    {"name":"PrimaryResult","columns":
                        [
                        {"name":"$table","type":"string"},
                        {"name":"Id","type":"string"},
                        {"name":"TimeGenerated","type":"datetime"}
                        ],
                    "rows":
                        [
                            ["Fabrikam","33446677a","2018-02-02T15:03:12.18Z"],
                            ["Contoso","33445566b","2018-02-02T15:16:53.932Z"]
                        ]
                    }
                ]
        },
    "WorkspaceId":"12345a-1234b-123c-123d-12345678e",
    "AlertType": "Metric measurement"
 }
 ```

> [!NOTE]
> [嚴重性] 欄位值可能會變更，如果您已經[切換您 API 的喜好設定](alerts-log-api-switch.md)Log analytics 的記錄警示。


#### <a name="log-alert-for-application-insights"></a>Application Insights 的記錄警示
下列的範例承載是標準的 webhook*不含自訂 JSON 選項*Application Insights 使用的記錄警示時根據：
    
```json
{
    "schemaId":"Microsoft.Insights/LogAlert","data":
    { 
    "SubscriptionId":"12345a-1234b-123c-123d-12345678e",
    "AlertRuleName":"AcmeRule",
    "SearchQuery":"requests | where resultCode == \"500\"",
    "SearchIntervalStartTimeUtc": "2018-03-26T08:10:40Z",
    "SearchIntervalEndtimeUtc": "2018-03-26T09:10:40Z",
    "AlertThresholdOperator": "Greater Than",
    "AlertThresholdValue": 0,
    "ResultCount": 2,
    "SearchIntervalInSeconds": 3600,
    "LinkToSearchResults": "https://portal.azure.com/AnalyticsBlade/subscriptions/12345a-1234b-123c-123d-12345678e/?query=search+*+&timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
    "Description": null,
    "Severity": "3",
    "SearchResult":
        {
        "tables":[
                    {"name":"PrimaryResult","columns":
                        [
                        {"name":"$table","type":"string"},
                        {"name":"Id","type":"string"},
                        {"name":"TimeGenerated","type":"datetime"}
                        ],
                    "rows":
                        [
                            ["Fabrikam","33446677a","2018-02-02T15:03:12.18Z"],
                            ["Contoso","33445566b","2018-02-02T15:16:53.932Z"]
                        ]
                    }
                ]
        },
    "ApplicationId": "123123f0-01d3-12ab-123f-abc1ab01c0a1",
    "AlertType": "Number of results"
    }
}
```

#### <a name="log-alert-with-custom-json-payload"></a>含有自訂 JSON 承載的記錄警示
例如，若要建立只包含警示名稱和搜尋結果的自訂承載，您可以使用下列： 

```json
    {
       "alertname":"#alertrulename",
       "IncludeSearchResults":true
    }
```

下列的範例承載適用於任何記錄警示的自訂 webhook 動作：
    
```json
    {
    "alertname":"AcmeRule","IncludeSearchResults":true,
    "SearchResult":
        {
        "tables":[
                    {"name":"PrimaryResult","columns":
                        [
                        {"name":"$table","type":"string"},
                        {"name":"Id","type":"string"},
                        {"name":"TimeGenerated","type":"datetime"}
                        ],
                    "rows":
                        [
                            ["Fabrikam","33446677a","2018-02-02T15:03:12.18Z"],
                            ["Contoso","33445566b","2018-02-02T15:16:53.932Z"]
                        ]
                    }
                ]
        }
    }
```


## <a name="next-steps"></a>後續步驟
- 深入了解[Azure 警示中的記錄警示](alerts-unified-log.md)。
- 了解如何[管理 Azure 中的記錄警示](alerts-log.md)。
- 建立和管理[在 Azure 中的動作群組](action-groups.md)。
- 深入了解 [Application Insights](../../azure-monitor/app/analytics.md)。
- 深入了解[記錄查詢](../log-query/log-query-overview.md)。 

