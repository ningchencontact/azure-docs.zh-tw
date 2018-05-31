---
title: 如何透過 Azure 監視器警示和動作群組觸發複雜動作 | Microsoft Docs
description: 了解如何建立邏輯應用程動作來處理序 Azure 監視器警示。
author: dkamstra
manager: chrad
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: ''
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/30/2018
ms.author: dukek
ms.openlocfilehash: 0020f1475d52d01897320062edbd3a66c8acf751
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/08/2018
ms.locfileid: "33887448"
---
# <a name="create-a-logic-app-action"></a>建立邏輯應用程式動作
## <a name="overview"></a>概觀 ##
Azure 監視器警示會在觸發時呼叫[動作群組](monitoring-action-groups.md)。 動作群組可讓您觸發一個或多個動作來通知人員發生警示，甚至可以進行修復。

本文會示範如何設定並觸發邏輯應用程式，以便在引發警示時與 Microsoft Teams 建立交談。

一般的程序如下：

-   建立個別警示類型的邏輯應用程式

-   將個別警示類型的結構描述匯入邏輯應用程式

-   定義邏輯應用程式的行為

-   將邏輯應用程式的 HTTP 端點複製到 Azure 動作群組

如果您想要讓邏輯應用程式執行不同動作，其程序也是類似的。

## <a name="create-an-activity-log-alert--administrative"></a>建立活動記錄警示 – 系統管理

1.  按一下 Azure 入口網站左上角的 [建立資源] 按鈕。

2.  搜尋並選取**邏輯應用程式**。 按一下 [ **建立** ] 按鈕。

3.  指定邏輯應用程式的名稱，並選擇資源群組等項目。

    ![建立邏輯應用程式對話方塊](media/monitoring-action-groups/create-logic-app-dialog.png "建立邏輯應用程式對話方塊")

4.  按一下 [建立] 按鈕以建立邏輯應用程式。 邏輯應用程式建好後，將會顯示快顯視窗。 按一下 [啟動資源] 按鈕，以開啟 Logic Apps 設計工具。

5.  選取 [收到 HTTP 要求時] 觸發程序。

    ![邏輯應用程式觸發程序](media/monitoring-action-groups/logic-app-triggers.png "邏輯應用程式觸發程序")

6.  選取 [編輯] 來變更 HTTP 要求觸發程序

    ![HTTP 要求觸發程序圖形](media/monitoring-action-groups/http-request-trigger-shape.png "HTTP 要求觸發程序圖形")

7.  選取 [使用範例承載來產生結構描述]。

    ![使用範例承載按鈕](media/monitoring-action-groups/use-sample-payload-button.png "使用範例承載按鈕")

8.  複製下列結構描述範例，並貼入對話方塊。

    ```json
        {
            "schemaId": "Microsoft.Insights/activityLogs",
            "data": {
                "status": "Activated",
                "context": {
                "activityLog": {
                    "authorization": {
                    "action": "microsoft.insights/activityLogAlerts/write",
                    "scope": "/subscriptions/…"
                    },
                    "channels": "Operation",
                    "claims": "…",
                    "caller": "logicappdemo@contoso.com",
                    "correlationId": "91ad2bac-1afa-4932-a2ce-2f8efd6765a3",
                    "description": "",
                    "eventSource": "Administrative",
                    "eventTimestamp": "2018-04-03T22:33:11.762469+00:00",
                    "eventDataId": "ec74c4a2-d7ae-48c3-a4d0-2684a1611ca0",
                    "level": "Informational",
                    "operationName": "microsoft.insights/activityLogAlerts/write",
                    "operationId": "61f59fc8-1442-4c74-9f5f-937392a9723c",
                    "resourceId": "/subscriptions/…",
                    "resourceGroupName": "LOGICAPP-DEMO",
                    "resourceProviderName": "microsoft.insights",
                    "status": "Succeeded",
                    "subStatus": "",
                    "subscriptionId": "…",
                    "submissionTimestamp": "2018-04-03T22:33:36.1068742+00:00",
                    "resourceType": "microsoft.insights/activityLogAlerts"
                }
                },
                "properties": {}
            }
        }
    ```

9. 邏輯應用程式設計工具將會顯示提示，提醒您對於傳送至邏輯應用程式的要求，必須將 Content-Type 標頭設定為 application/json。 請繼續並關閉對話方塊。 Azure 監視器警示會正確地執行此操作。

    ![Content-type 標頭](media/monitoring-action-groups/content-type-header.png "Content-type 標頭")

10. 選取 [+ 新增步驟]，然後選擇 [新增動作]。

    ![新增動作](media/monitoring-action-groups/add-action.png "新增動作")

11. 搜尋並選取 Microsoft Teams 連接器。 選擇 [Microsoft Teams – 張貼訊息] 動作。

    ![動作](media/monitoring-action-groups/microsoft-teams-actions.png "動作")

12. 設定 Microsoft Teams 動作。 Logic Apps 設計工具會要求您驗證 Office365 帳戶。 選擇要對其傳送訊息的 [團隊識別碼] 和 [通道識別碼]。

13. 使用靜態文字和動態內容中的 \<欄位\> 參考組合，來設定 [訊息]。 剪下以下文字並貼到 [訊息] 欄位中。

    ```text
      Activity Log Alert: <eventSource>
      operationName: <operationName>
      status: <status>
      resourceId: <resourceId>
    ```

    然後搜尋相同名稱的動態內容標記，並用其取代 \<欄位\>。

    **[注意！]** 有兩個名為 [狀態] 的動態欄位。 請將這兩個狀態欄位新增至訊息中。 使用 activityLog 屬性包中的狀態，並刪除另一個。 如果您將滑鼠停留在 [狀態] 欄位上方，您會看到完整欄位參考，如螢幕擷取畫面所示

    ![Teams 動作 - 張貼訊息](media/monitoring-action-groups/teams-action-post-message.png "Teams 動作 - 張貼訊息")

14. 按一下設計工具上方的 [儲存] 按鈕，以儲存邏輯應用程式

15. 按一下 [HTTP 要求] 圖形，讓它展開。 複製 HTTP 貼文 URL。

    ![HTTP 貼文 URL](media/monitoring-action-groups/http-post-url.png "HTTP 貼文 URL")

16. 開啟現有的動作群組，並加入要參考邏輯應用程式的動作。 如果您沒有現有的動作群組，請參閱 <https://docs.microsoft.com/en-us/azure/monitoring-and-diagnostics/monitoring-action-groups> 以建立一個。 請記得儲存變更。

    ![更新動作群組](media/monitoring-action-groups/update-action-group.png "更新動作群組")

下一次警示叫用您的動作群組時，系統就會呼叫您的應用程式邏輯。

## <a name="create-a-service-health-alert"></a>建立服務健康情況警示

服務健康情況項目屬於活動記錄的一部分，因此程序類似，但會有以下變動

1.  步驟 1 至 7 都相同。
2.  在步驟 8 中，使用下列結構描述範例作為 HTTP 觸發程序。

    ```json
    {
        "schemaId": "Microsoft.Insights/activityLogs",
        "data": {
            "status": "Activated",
            "context": {
                "activityLog": {
                    "channels": "Admin",
                    "correlationId": "e416ed3c-8874-4ec8-bc6b-54e3c92a24d4",
                    "description": "…",
                    "eventSource": "ServiceHealth",
                    "eventTimestamp": "2018-04-03T22:44:43.7467716+00:00",
                    "eventDataId": "9ce152f5-d435-ee31-2dce-104228486a6d",
                    "level": "Informational",
                    "operationName": "Microsoft.ServiceHealth/incident/action",
                    "operationId": "e416ed3c-8874-4ec8-bc6b-54e3c92a24d4",
                    "properties": {
                        "title": "…",
                        "service": "…",
                        "region": "Global",
                        "communication": "…",
                        "incidentType": "Incident",
                        "trackingId": "…",
                        "impactStartTime": "2018-03-22T21:40:00.0000000Z",
                        "impactMitigationTime": "2018-03-22T21:41:00.0000000Z",
                        "impactedServices": "[{"ImpactedRegions"}]",
                        "defaultLanguageTitle": "…",
                        "defaultLanguageContent": "…",
                        "stage": "Active",
                        "communicationId": "11000001466525",
                        "version": "0.1.1"
                    },
                    "status": "Active",
                    "subscriptionId": "…",
                    "submissionTimestamp": "2018-04-03T22:44:50.8013523+00:00"
                }
            },
            "properties": {}
        }
    }
    ```

3.  步驟 9-10 都相同。
4.  從步驟 11 開始，使用下列程序。
5.  按一下 [+ 新增步驟] 按鈕，並選擇 [新增條件]。 設定下列條件，確保只有當輸入資料符合這些值時，邏輯應用才會在執行
    - schemaId == Microsoft.Insights/activityLogs
    - eventSource == ServiceHealth
    - version == 0.1.1

    ![「服務健康情況承載條件」](media/monitoring-action-groups/service-health-payload-condition.png "服務健康情況承載條件")

6. 在 [如果為 True] 條件中，使用前一個範例的步驟 11 – 13 來新增 Microsoft Teams 動作。

7. 使用 HTML 和 [動態內容] 的組合來定義訊息。 複製下方內容並貼到 [訊息] 欄位。 以相同名稱的動態內容標記取代 [incidentType]、[trackingID]、[title] 和 [communication] 欄位

    ```html
    <p>
    <b>Alert Type:</b>&nbsp;<strong>[incidentType]</strong>&nbsp;
    <strong>Tracking ID:</strong>&nbsp;[trackingId]&nbsp;
    <strong>Title:</strong>&nbsp;[title]</p>
    <p>
    <a href="https://ms.portal.azure.com/#blade/Microsoft_Azure_Health/AzureHealthBrowseBlade/serviceIssues">For details log into the Azure Service Health dashboard</a>
    </p>
    <p>[communication]</p>
    ```

    ![「服務健康情況的 True 條件張貼動作」](media/monitoring-action-groups/service-health-true-condition-post-action.png "服務健康情況的 True 條件張貼動作")

8. 針對 [如果為 False] 的條件提供實用訊息

    ```html
    <p><strong>Service Health Alert</strong></p>
    <p><b>Unrecognized alert schema</b></p>
    <p><a href="https://ms.portal.azure.com/#blade/Microsoft_Azure_Health/AzureHealthBrowseBlade/serviceIssues">For details log into the Azure Service Health dashboard\</a></p>
    ```

    ![「服務健康情況的 False 條件張貼動作」](media/monitoring-action-groups/service-health-false-condition-post-action.png "服務健康情況的 False 條件張貼動作")

9.  請遵循前一個範例的步驟 15-16，儲存邏輯應用程式並更新動作群組

## <a name="metric-alert"></a>計量警示

1.  步驟 1 至 7 與第一個範例相同
2.  在步驟 8 中，使用下列結構描述範例作為 HTTP 觸發程序。

    ```json
    {
    "schemaId": "AzureMonitorMetricAlert",
    "data": {
        "version": "2.0",
        "status": "Activated",
        "context": {
        "timestamp": "2018-04-09T19:00:07.7461615Z",
        "id": "…",
        "name": "TEST-VM CPU Utilization",
        "description": "",
        "conditionType": "SingleResourceMultipleMetricCriteria",
        "condition": {
            "windowSize": "PT15M",
            "allOf": [
                {
                    "metricName": "Percentage CPU",
                    "dimensions": [
                    {
                        "name": "ResourceId",
                        "value": "d92fc5cb-06cf-4309-8c9a-538eea6a17a6"
                    }
                ],
                "operator": "GreaterThan",
                "threshold": "5",
                "timeAggregation": "PT15M",
                "metricValue": 1.0
            }
            ]
        },
        "subscriptionId": "…",
        "resourceGroupName": "TEST",
        "resourceName": "test-vm",
        "resourceType": "Microsoft.Compute/virtualMachines",
        "resourceId": "…",
        "portalLink": "…"
        },
        "properties": {}
    }
    }
    ```

3.  步驟 9-10 都相同。
4.  從步驟 11 開始，使用下列程序。
5.  按一下 [+ 新增步驟] 按鈕，並選擇 [新增條件]。 設定下列條件，確保只有當輸入資料符合這些值時，邏輯應用才會在執行

    - schemaId == AzureMonitorMetricAlert
    - version == 2.0

    ![「計量警示承載條件」](media/monitoring-action-groups/metric-alert-payload-condition.png "計量警示承載條件")

6.  在 [如果為 True] 條件中，我們會新增 **For each** 圖形和 Microsoft Teams 動作，並使用 HTML 和 [動態內容] 的組合來定義訊息

    ![「計量警示的 True 條件張貼動作」](media/monitoring-action-groups/metric-alert-true-condition-post-action.png "計量警示的 True 條件張貼動作")

7.  在 [如果為 False] 圖形中定義 Microsoft Teams 動作，讓其在計量警示不符合邏輯應用程式的預期結果時發出通知，並包含 JSON 承載。 請注意我們如何使用 json() 運算式來參考 triggerBody 動態內容。

    ![「計量警示的 False 條件張貼動作」](media/monitoring-action-groups/metric-alert-false-condition-post-action.png "計量警示的 False 條件張貼動作")

8.  請遵循第一個範例的步驟 15-16，儲存邏輯應用程式並更新動作群組

## <a name="next-steps"></a>後續步驟 ##
* 取得[活動記錄警示的概觀](monitoring-overview-alerts.md)，並了解如何收到警示。  
* 了解如何[設定每當服務健康狀態通知公佈時的警示](monitoring-activity-log-alerts-on-service-notifications.md)。
* 深入了解[動作群組](monitoring-action-groups.md)