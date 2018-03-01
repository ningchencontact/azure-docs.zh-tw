---
title: "Azure 警示 (預覽) 中記錄警示的 Webhook 動作 | Microsoft Docs"
description: "本文說明使用 Log Analytics 或 Application Insights 的記錄警示規則如何將資料作為 HTTP Webhook 推送以及不同可行自訂的詳細資料。"
author: msvijayn
manager: kmadnani1
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 49905638-f9f2-427b-8489-a0bcc7d8b9fe
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/05/2018
ms.author: vinagara
ms.openlocfilehash: 4af1bb61888810011ce64fde7931cabfefe76ab6
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/09/2018
---
# <a name="webhook-actions-for-log-alert-rules"></a>Webhook 動作記錄警示規則
[在 Azure (預覽) 中建立警示](monitor-alerts-unified-usage.md)後，您可以選擇[使用動作群組設定](monitoring-action-groups.md)以執行一或多個動作。  本文說明各種可用的 Webhook 動作以及設定自訂 JSON 型 Webhook 的詳細資訊。


## <a name="webhook-actions"></a>Webhook 動作

Webhook 動作可讓您透過單一 HTTP POST 要求叫用外部處理序。  被呼叫的服務應支援 Webhook，並判斷將如何使用所接收的承載。  只要此要求是 API 所認得的格式，您也可以呼叫不會特別支援 Webhook 的 REST API。  使用 Webhook 來回應警示的範例會在 [Slack](http://slack.com) 中傳送訊息，或在 [PagerDuty](http://pagerduty.com/) 中建立事件。  如需使用 Webhook 來呼叫 Slack 以建立警示規則的完整逐步解說，請參閱 [Log Analytics 警示中的 Webhook](../log-analytics/log-analytics-alerts-webhooks.md)。

Webhook 動作需要下表中的屬性。

| 屬性 | 說明 |
|:--- |:--- |
| Webhook URL |Webhook 的 URL。 |
| 自訂 JSON 承載 |要隨著 webhook 傳送的自訂承載。  若是在警示建立期間選擇選項。 如需詳細資料，請參閱[使用 Azure 警示 (預覽) 管理警示](monitor-alerts-unified-usage.md) |

> [!NOTE]
> 記錄警示的 [包含 webhook 的自訂 JSON 承載] 選項旁的 [測試 Webhook] 按鈕會觸發虛擬呼叫來測試 Webhook URL。 它不包含實際資料或用於記錄警示的代表性 JSON 結構描述。 雖然您可以使用代表性的自訂 JSON 測試任何一個 webhook，但動作群組中設定的所有 Webhook 都會隨自訂 JSON 承載一起傳送。

Webhook 包括 URL 以及 JSON 格式的承載 (也就是傳送至外部服務的資料)。  根據預設，承載會包含下表中的值。  您可以選擇用自己的自訂承載來取代此承載。  在此情況下，您可以使用下表中每個參數的變數，將其值包含在您的自訂承載中。


| 參數 | 變數 | 說明 |
|:--- |:--- |:--- |
| AlertRuleName |#alertrulename |警示規則的名稱。 |
| AlertThresholdOperator |#thresholdoperator |警示規則的臨界值運算子。  大於或等於。 |
| AlertThresholdValue |#thresholdvalue |警示規則的臨界值。 |
| LinkToSearchResults |#linktosearchresults |連結至 Log Analytics 記錄檔搜尋，而該搜尋會從建立警示的查詢傳回記錄。 |
| ResultCount |#searchresultcount |搜尋結果中的記錄數目。 |
| SearchIntervalEndtimeUtc |#searchintervalendtimeutc |查詢的結束時間 (UTC 格式)。 |
| SearchIntervalInSeconds |#searchinterval |警示規則的時間範圍。 |
| SearchIntervalStartTimeUtc |#searchintervalstarttimeutc |查詢的開始時間 (UTC 格式)。 |
| SearchQuery |#searchquery |警示規則所使用的記錄檔搜尋查詢。 |
| SearchResults |"IncludeSearchResults":true|查詢會將記錄傳回為 JSON 資料表，受限於前 1,000 筆記錄/列；如果在自訂 JSON Webhook 定義中將 "IncludeSearchResults":true 新增為最上層屬性的話 |
| WorkspaceID |#workspaceid |Log Analytics 工作區的識別碼。 |
| 嚴重性 |#severity |為引發的記錄警示設定的嚴重性。 |

例如，您可以指定下列自訂承載，其中包含稱為 text 的單一參數。  此 Webhook 所呼叫的服務需要有這個參數。

    {
        "text":"#alertrulename fired with #searchresultcount over threshold of #thresholdvalue."
    }

此範例承載會在傳送到 Webhook 時解析如下。

    {
        "text":"My Alert Rule fired with 18 records over threshold of 10 ."
    }

若要在自訂承載中包含搜尋結果，請務必將 **IncudeSearchResults** 設定為 json 承載中的最上層屬性。


您可以[在 Log Analytics 中建立警示 webhook 動作以傳送訊息給 Slack](../log-analytics/log-analytics-alerts-webhooks.md)時使用 Webhook 來啟動外部服務，逐步執行建立警示規則的完整範例。

## <a name="sample-payload"></a>範例承載
本節說明記錄警示的 Webhook 範例承載，包括其為標準和自訂承載時。

> [!NOTE]
> 為了確保回溯相容性，使用 Azure Log Analytics 的警示標準 Webhook 承載會與 [OMS 警示管理](../log-analytics/log-analytics-solution-alert-management.md)相同。 但使用 [Application Insights](../application-insights/app-insights-analytics.md) 的記錄警示，標準 Webhook 承載則以動作群組結構描述為基礎

### <a name="standard-webhook-for-log-alerts"></a>記錄警示的標準 Webhook
這兩個範例都提供只含有兩個資料行和兩個資料列的虛擬乘載。

#### <a name="log-alert-for-azure-log-analytics"></a>Azure Log-Analytics 記錄警示
以下是用於 Log-Analytics 記錄警示時，不含自訂 Json 的標準 Webhook 動作範例承載。

    {
    "WorkspaceId":"12345a-1234b-123c-123d-12345678e",
    "AlertRuleName":"AcmeRule","SearchQuery":"search *",
    "SearchResult":
        {
        "tables":[
                    {"name":"PrimaryResult","columns":
                        [
                        {"name":"$table","type":"string"},
                        {"name":"Id","type":"string"},
                        {"name":"TimeGenerated","type":"datetime"},
                        ],
                    "rows":
                        [
                            ["Fabrikam","33446677a","2018-02-02T15:03:12.18Z"],
                            ["Contoso","33445566b","2018-02-02T15:16:53.932Z"]
                        ]
                    }
                ]
        }
    "SearchIntervalStartTimeUtc": "2018-03-26T08:10:40Z",
    "SearchIntervalEndtimeUtc": "2018-03-26T09:10:40Z",
    "AlertThresholdOperator": "Greater Than",
    "AlertThresholdValue": 0,
    "ResultCount": 2,
    "SearchIntervalInSeconds": 3600,
    "LinkToSearchResults": "https://workspaceID.portal.mms.microsoft.com/#Workspace/search/index?_timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
    "Description": null,
    "Severity": "Low"
    }



#### <a name="log-alert-for-azure-application-insights"></a>Azure Application Insights 記錄警示
以下是用於 Application Insights 型記錄警示時，不含自訂 Json 的標準 Webhook 動作範例承載。


    {
    "schemaId":"LogAlert","data":
    {
    "WorkspaceId":"12345a-1234b-123c-123d-12345678e",
    "AlertRuleName":"AcmeRule","SearchQuery":"search *",
    "SearchResult":
        {
        "tables":[
                    {"name":"PrimaryResult","columns":
                        [
                        {"name":"$table","type":"string"},
                        {"name":"Id","type":"string"},
                        {"name":"TimeGenerated","type":"datetime"},
                        ],
                    "rows":
                        [
                            ["Fabrikam","33446677a","2018-02-02T15:03:12.18Z"],
                            ["Contoso","33445566b","2018-02-02T15:16:53.932Z"]
                        ]
                    }
                ]
        }
    "SearchIntervalStartTimeUtc": "2018-03-26T08:10:40Z",
    "SearchIntervalEndtimeUtc": "2018-03-26T09:10:40Z",
    "AlertThresholdOperator": "Greater Than",
    "AlertThresholdValue": 0,
    "ResultCount": 2,
    "SearchIntervalInSeconds": 3600,
    "LinkToSearchResults": "https://workspaceID.portal.mms.microsoft.com/#Workspace/search/index?_timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
    "Description": null,
    "Severity": "Low"
    }
    }


#### <a name="log-alert-with-custom-json-payload"></a>含有自訂 JSON 承載的記錄警示
例如，若要建立只包含警示名稱和搜尋結果的自訂承載，您可以使用下列程式碼。

    {
       "alertname":"#alertrulename",
       "IncludeSearchResults":true
    }

以下是任何記錄警示的自訂 Webhook 動作範例承載。


    {
    "AlertRuleName":"AcmeRule","IncludeSearchResults":true,
    "SearchResult":
        {
        "tables":[
                    {"name":"PrimaryResult","columns":
                        [
                        {"name":"$table","type":"string"},
                        {"name":"Id","type":"string"},
                        {"name":"TimeGenerated","type":"datetime"},
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




## <a name="next-steps"></a>後續步驟
- 建立和管理 [Azure 中的動作群組](monitoring-action-groups.md)
- 了解 [Azure 警示 (預覽) 中的記錄警示](monitor-alerts-unified-log.md)
