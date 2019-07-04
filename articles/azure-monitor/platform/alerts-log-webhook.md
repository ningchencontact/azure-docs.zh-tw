---
title: Azure 警示中各項記錄警示的 Webhook 動作
description: 本文說明如何記錄警示規則來使用 log analytics 工作區或 application insights，將資料作為 HTTP webhook 推送以及不同的自訂的詳細資料可能。
author: msvijayn
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: vinagara
ms.subservice: alerts
ms.openlocfilehash: cad1b0ab484d172000bd62146a88a27bfab1e9f2
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/28/2019
ms.locfileid: "67448777"
---
# <a name="webhook-actions-for-log-alert-rules"></a>Webhook 動作記錄警示規則
[在 Azure 中建立記錄警示](alerts-log.md)後，您可以選擇[使用動作群組設定](action-groups.md)以執行一或多個動作。  本文說明各種可用的 Webhook 動作以及設定自訂 JSON 型 Webhook 的詳細資訊。

> [!NOTE]
> 您也可以使用[常見的警示結構描述](https://aka.ms/commonAlertSchemaDocs)、 提供一個可延伸的優點，並統一跨所有警示的警示承載的 webhook 整合服務在 Azure 監視器中。 [深入了解常見的警示的結構描述定義。](https://aka.ms/commonAlertSchemaDefinitions)

## <a name="webhook-actions"></a>Webhook 動作

Webhook 動作可讓您透過單一 HTTP POST 要求叫用外部處理序。  被呼叫的服務應支援 Webhook，並判斷它將如何使用所接收的承載。    

Webhook 動作需要下表中的屬性：

| 屬性 | 描述 |
|:--- |:--- |
| Webhook URL |Webhook 的 URL。 |
| 自訂 JSON 承載 |自訂在警示建立期間選擇此選項時使用 Webhook 傳送承載。 您可以在[管理記錄警示](alerts-log.md)找到詳細資料 |

> [!NOTE]
> 記錄警示的 [包含 Webhook 的自訂 JSON 承載]  選項旁的 [檢視 Webhook] 按鈕，會顯示所提供自訂的範例 Webhook 承載。 它不包含實際資料，也不包含用於記錄警示的代表性 JSON 結構描述。 

Webhook 包括 URL 以及 JSON 格式的承載 (也就是傳送至外部服務的資料)。  根據預設，承載會包含下表中的值：您可以選擇用自己的自訂承載來取代此承載。  在此情況下，您可以使用下表中每個參數的變數，將其值包含在您的自訂承載中。


| 參數 | 變數 | 描述 |
|:--- |:--- |:--- |
| AlertRuleName |#alertrulename |警示規則的名稱。 |
| 嚴重性 |#severity |為引發的記錄警示設定的嚴重性。 |
| AlertThresholdOperator |#thresholdoperator |警示規則的臨界值運算子。  大於  或等於  。 |
| AlertThresholdValue |#thresholdvalue |警示規則的臨界值。 |
| LinkToSearchResults |#linktosearchresults |連結至 Analytics 入口網站；此入口網站會從建立警示的查詢傳回記錄。 |
| ResultCount |#searchresultcount |搜尋結果中的記錄數目。 |
| 搜尋間隔結束時間 |#searchintervalendtimeutc |查詢的結束時間 (UTC)，格式 - mm/dd/yyyy HH:mm:ss AM/PM。 |
| 搜尋間隔 |#searchinterval |警示規則的時間範圍，格式 - HH:mm:ss。 |
| 搜尋間隔開始時間 |#searchintervalstarttimeutc |查詢的開始時間 (UTC)，格式 - mm/dd/yyyy HH:mm:ss AM/PM。 
| SearchQuery |#searchquery |警示規則所使用的記錄檔搜尋查詢。 |
| SearchResults |"IncludeSearchResults": true|查詢會將記錄傳回為 JSON 資料表，受限於前 1,000 筆記錄；如果在自訂 JSON Webhook 定義中將 "IncludeSearchResults": true 新增為最上層屬性的話。 |
| 警示類型| #alerttype | 記錄警示規則設定為類型[計量測量](alerts-unified-log.md#metric-measurement-alert-rules)或[的結果數目](alerts-unified-log.md#number-of-results-alert-rules)。|
| WorkspaceID |#workspaceid |Log Analytics 工作區的識別碼。 |
| 應用程式識別碼 |#applicationid |Application Insight 應用程式的識別碼。 |
| 訂用帳戶識別碼 |#subscriptionid |使用您 Azure 訂用帳戶識別碼。 

> [!NOTE]
> LinkToSearchResults 會將參數 (例如 SearchQuery、URL 中的搜尋間隔開始時間和搜尋間隔結束時間) 傳遞至 Azure 入口網站，以便在 Analytics 區段中檢視。 Azure 入口網站有大小限制，大約 2000年個字元，並將 URI*不*提供在警示中，如果參數值超過該的限制的開啟連結。 使用者可以手動輸入詳細資料，以在 Analytics 入口網站中檢視結果，或使用 [Application Insights Analytics REST API](https://dev.applicationinsights.io/documentation/Using-the-API) 或 [Log Analytics REST API](/rest/api/loganalytics/) 以程式設計方式擷取結果 

例如，您可以指定下列自訂承載，其中包含稱為 text  的單一參數。  此 Webhook 所呼叫的服務需要有這個參數。

```json

    {
        "text":"#alertrulename fired with #searchresultcount over threshold of #thresholdvalue."
    }
```
此範例承載會在傳送到 Webhook 時解析如下。

```json
    {
        "text":"My Alert Rule fired with 18 records over threshold of 10 ."
    }
```
正如自訂 Webhook 中的所有變數都必須指定於 JSON 括號內 (例如 "#searchinterval")，產生的 Webhook 也會將變數資料包含在括號內，例如 "00:05:00"。

若要在自訂承載中包含搜尋結果，請務必將 **IncludeSearchResults** 設定為 JSON 承載中的最上層屬性。 

## <a name="sample-payloads"></a>承載範例
本節說明記錄警示的 Webhook 範例承載，包括其為標準和自訂承載時。

### <a name="standard-webhook-for-log-alerts"></a>記錄警示的標準 Webhook 
這兩個範例都提供只含有兩個資料行和兩個資料列的虛擬承載。

#### <a name="log-alert-for-azure-log-analytics"></a>Azure Log-Analytics 記錄警示
以下是用於記錄分析型警示時，*不含自訂 Json 選項*的標準 Webhook 動作範例承載。

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
> 嚴重性 欄位值可能會變更，如果您有[切換您 API 的喜好設定](alerts-log-api-switch.md)Log analytics 的記錄警示。


#### <a name="log-alert-for-azure-application-insights"></a>Azure Application Insights 記錄警示
以下是用於 Application Insights 型記錄警示時，*不含自訂 Json 選項*的標準 Webhook 範例承載。
    
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
例如，若要建立只包含警示名稱和搜尋結果的自訂承載，您可以使用下列程式碼： 

```json
    {
       "alertname":"#alertrulename",
       "IncludeSearchResults":true
    }
```

以下是任何記錄警示的自訂 Webhook 動作範例承載。
    
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
- 了解 [Azure 警示中的記錄警示](alerts-unified-log.md)
- 了解[管理 Azure 中的記錄警示](alerts-log.md)
- 建立和管理 [Azure 中的動作群組](action-groups.md)
- 深入了解 [Application Insights](../../azure-monitor/app/analytics.md)
- 深入了解[記錄查詢](../log-query/log-query-overview.md)。 

