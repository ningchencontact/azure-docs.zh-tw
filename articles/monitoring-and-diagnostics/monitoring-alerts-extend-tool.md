---
title: 將警示從 OMS 初始化擴充至 Azure | Microsoft Docs
description: 客戶可主動完成用來將警示從 OMS 擴充至 Azure 警示的工具和 API。
author: msvijayn
manager: kmadnani1
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/16/2018
ms.author: vinagara
ms.openlocfilehash: 76b7481223566f16a5da8c08d9d76f2bdb6b542a
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2018
---
# <a name="initiate-extending-alerts-from-oms-into-azure"></a>將警示從 OMS 初始化擴充至 Azure
自 **2018 年 4 月 23 日**開始，使用在 [Microsoft Operations Management Suite (OMS)](../operations-management-suite/operations-management-suite-overview.md) 中設定之警示的所有客戶將會擴充至 Azure。 擴充至 Azure 的警示行為與 OMS 中的行為相同。 監視功能仍維持不變。 將 OMS 中建立的警示擴充至 Azure 可提供許多好處。 如需有關將警示從 OMS 擴充至 Azure 的優點和程序詳細資訊，請參閱[將警示從 OMS 擴充至 Azure](monitoring-alerts-extend.md)。

需要立即將警示從 OMS 移至 Azure 的客戶，可以使用這裡所述的其中一種選項來進行。

## <a name="option-1---using-oms-portal"></a>選項 1 - 使用 OMS 入口網站
若要主動將警示從 OMS 初始化擴充至 Azure，請遵循下面所列的步驟。

1. 在 Operations Management Suite (OMS) 概觀頁面上，移至 [設定]，然後移至 [警示] 區段。 按一下標示為 [擴充至 Azure] 的按鈕，如下圖中醒目提示的部分。

    ![包含 [擴充] 選項的 [OMS 警示設定] 頁面](./media/monitor-alerts-extend/ExtendInto.png)

2. 一旦按下按鈕後，將會顯示 3 個步驟的精靈，其中的第一個步驟會提供程序的詳細資料。 按 [下一步] 繼續進行。

    ![將警示從 OMS 擴充至 Azure - 步驟 1](./media/monitor-alerts-extend/ExtendStep1.png)

3. 在第二個步驟中，系統會列出適當的[動作群組](monitoring-action-groups.md)，針對 OMS 中的警示顯示建議變更的摘要。 如果在多個警示中看到類似動作 - 系統會提議將全部的動作與單一動作群組建立關聯。  所提議的動作群組需遵循命名慣例：WorkspaceName_AG_#Number。 若要繼續，請按 [下一步]。
以下是範例畫面。

    ![將警示從 OMS 擴充至 Azure - 步驟 2](./media/monitor-alerts-extend/ExtendStep2.png)


4. 在精靈的最後一個步驟中，您可以要求 OMS 針對將所有警示擴充至 Azure 的作業進行排程 - 方法是建立新的動作群組，並將它們與警示建立關聯，如先前畫面所示。 若要繼續選擇，請按一下 [完成]，並在提示字元中確認以起始程序。 (選擇性) 客戶也可以提供他們的電子郵件地址，以便 OMS 在處理程序完成時，將報告傳送給他們。

    ![將警示從 OMS 擴充至 Azure - 步驟 3](./media/monitor-alerts-extend/ExtendStep3.png)

5. 一但精靈完成後，控制項將會回到 [警示設定] 頁面，且「擴充至 Azure」選項會加以移除。 OMS 會在背景中對 OMS 中要擴充到 Azure 的警示進行排程；這可能需要一些時間，作業剛開始一段時間後，OMS 中的警示將無法進行修改。 目前的狀態會透過橫幅顯示，且如果在步驟 4 中提供了電子郵件地址，則當背景程序成功將所有警示擴充至 Azure 時，使用者就會收到通知。 

6. 警示會繼續列在 OMS 中，即使是已成功將警示擴充至 Azure 也一樣。

    ![將 OMS 中的警示擴充至 Azure 之後](./media/monitor-alerts-extend/PostExtendList.png)


## <a name="option-2---using-api"></a>選項 2 - 使用 API
如果客戶需要以程式設計方式來控制或將 OMS 中警示擴充到 Azure 的程序自動化，Microsoft 已在 Log Analytics 下提供新的 AlertsVersion API。

Log Analytics AlertsVersion API 是 RESTful，可透過 Azure Resource Manager REST API 來存取。 在這份文件中，您可以找到透過 [ARMClient](https://github.com/projectkudu/ARMClient) 從 PowerShell 命令列存取 API 的範例，這是一個開放原始碼命令列工具，可簡化叫用 Azure Resource Manager API。 使用 ARMClient 和 PowerShell 是存取 API 的許多選項之一。 API 會以 JSON 格式來輸出結果，可讓您以程式設計方式將結果用在許多不同用途上。

藉由使用 API 的 GET，使用者可以在結果中取得建議的變更摘要，也就是 OMS 中警示的適當[動作群組](monitoring-action-groups.md)清單 (JSON 格式)。 如果在多個警示中看到類似動作，系統會提議將全部的動作與單一動作群組建立關聯。  所提議的動作群組需遵循命名慣例：WorkspaceName_AG_#Number。

```
armclient GET  /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

> [!NOTE]
> API 的 GET 呼叫不會使 OMS 中的警示擴充至 Azure。 它只會提供所建議的變更摘要作為回應。 若要確認完成這些變更以將警示擴充至 Azure，必須完成對 API 的 POST 呼叫。

如果對 API 的 GET 呼叫成功，且包含 200 OK 回應，就會提供警示及所提議動作群組的 JSON 清單。 以下是回應範例：

```json
{
    "version": 1,
    "migrationSummary": {
        "alertsCount": 2,
        "actionGroupsCount": 2,
        "alerts": [
            {
                "alertName": "DemoAlert_1",
                "alertId": " /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/savedSearches/<savedSearchId>/schedules/<scheduleId>/actions/<actionId>",
                "actionGroupName": "<workspaceName>_AG_1"
            },
            {
                "alertName": "DemoAlert_2",
                "alertId": " /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/savedSearches/<savedSearchId>/schedules/<scheduleId>/actions/<actionId>",
                "actionGroupName": "<workspaceName>_AG_2"
            }
        ],
        "actionGroups": [
            {
                "actionGroupName": "<workspaceName>_AG_1",
                "actionGroupResourceId": "/subscriptions/<subscriptionid>/resourceGroups/<resourceGroupName>/providers/microsoft.insights/actionGroups/<workspaceName>_AG_1",
                "actions": {
                    "emailIds": [
                        "JohnDoe@mail.com"
                    ],
                    "webhookActions": [
                        {
                            "name": "Webhook_1",
                            "serviceUri": "http://test.com"
                        }
                    ],
                    "itsmAction": {}
                }
            },
            {
                "actionGroupName": "<workspaceName>_AG_1",
                "actionGroupResourceId": "/subscriptions/<subscriptionid>/resourceGroups/<resourceGroupName>/providers/microsoft.insights/actionGroups/<workspaceName>_AG_1",
                 "actions": {
                    "emailIds": [
                        "test1@mail.com",
                          "test2@mail.com"
                    ],
                    "webhookActions": [],
                    "itsmAction": {
                        "connectionId": "<Guid>",
                        "templateInfo":"{\"PayloadRevision\":0,\"WorkItemType\":\"Incident\",\"UseTemplate\":false,\"WorkItemData\":\"{\\\"contact_type\\\":\\\"email\\\",\\\"impact\\\":\\\"3\\\",\\\"urgency\\\":\\\"2\\\",\\\"category\\\":\\\"request\\\",\\\"subcategory\\\":\\\"password\\\"}\",\"CreateOneWIPerCI\":false}"
                    }
                }
            }
        ]
    }
}

```
萬一指定的工作區中沒有警示，GET 作業的 200 OK 回應所包含的 JSON 就會是：

```json
{
    "version": 1,
    "Message": "No Alerts found in the workspace for migration."
}
```

如果指定工作區中的所有警示都已擴充至 Azure，GET 呼叫的回應就會是：
```json
{
    "version": 2
}
```

若要起始將警示從 OMS 中擴充至 Azure 的排程，請起始 API 的 POST。 進行此呼叫/命令可確認使用者對於將 OMS 中的警示擴充至 Azure、以及執行 API GET 呼叫回應中所示變更的意圖及接受度。 或者，使用者可以提供電子郵件地址清單，在將 OMS 中的警示擴充至 Azure 的排程背景程序成功完成時，讓 OMS 寄送報告。

```
$emailJSON = “{‘Recipients’: [‘a@b.com’, ‘b@a.com’]}”
armclient POST  /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview $emailJSON
```

> [!NOTE]
> 將 OMS 警示擴充到 Azure 的結果可能會因系統中進行的任何變更，而與由 GET 提供的摘要有所不同。 一旦排程後，OMS 中的警示會暫時無法進行編輯/修改 - 但可以建立新的警示。 

如果 POST 成功，就會傳回 200 OK 回應以及：
```json
{
    "version": 2
}
```
指出警示已擴充至 Azure 中，如第 2 版所示。 這個版本僅供檢查警示是否已擴充至 Azure，且與 [Log Analytics 搜尋 API](../log-analytics/log-analytics-api-alerts.md) 使用方式並無任何關聯。 一旦警示成功擴充至 Azure 後，工作區中與系統管理員和參與者角色相關聯的所有使用者都會收到電子郵件，當中包含所進行之變更的詳細資料。


最後，如果指定工作區中的所有警示都已排程要擴充至 Azure，則對 POST 的回應將會是 403 禁止。


## <a name="next-steps"></a>後續步驟

* 深入了解新的 [Azure 警示體驗](monitoring-overview-unified-alerts.md)。
* 了解 [Azure 警示中的記錄警示](monitor-alerts-unified-log.md)。
