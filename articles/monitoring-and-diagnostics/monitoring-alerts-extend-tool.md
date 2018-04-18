---
title: 如何將警示從 OMS 入口網站延伸 (複製) 至 Azure | Microsoft Docs
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
ms.date: 04/06/2018
ms.author: vinagara
ms.openlocfilehash: e5dc48aa5e3c614192ae140dc80b5d9845acc474
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/06/2018
---
# <a name="how-to-extend-copy-alerts-from-oms-into-azure"></a>如何將警示從 OMS 延伸 (複製) 至 Azure
自 **2018 年 5 月 14 日**開始，使用在 [Microsoft Operations Management Suite (OMS)](../operations-management-suite/operations-management-suite-overview.md) 中設定之警示的所有客戶將會擴充至 Azure。 擴充至 Azure 的警示行為與 OMS 中的行為相同。 監視功能仍維持不變。 將 OMS 中建立的警示擴充至 Azure 可提供許多好處。 如需有關將警示從 OMS 擴充至 Azure 的優點和程序詳細資訊，請參閱[將警示從 OMS 擴充至 Azure](monitoring-alerts-extend.md)。

需要立即將警示從 OMS 移至 Azure 的客戶，可以使用這裡所述的其中一種選項來進行。

## <a name="option-1---using-oms-portal"></a>選項 1 - 使用 OMS 入口網站
若要主動起始將警示從 OMS 入口網站延伸至 Azure，請依照下列步驟進行操作。

1. 在 OMS 入口網站 [概觀] 頁面上，移至 [設定]，然後移至 [警示] 區段。 按一下標示為 [擴充至 Azure] 的按鈕，如下圖中醒目提示的部分。

    ![具有 [延伸] 選項的 OMS 入口網站 [警示設定] 頁面](./media/monitor-alerts-extend/ExtendInto.png)

2. 一旦按下按鈕後，將會顯示 3 個步驟的精靈，其中的第一個步驟會提供程序的詳細資料。 按 [下一步] 繼續進行。

    ![將警示從 OMS 入口網站延伸至 Azure - 步驟 1](./media/monitor-alerts-extend/ExtendStep1.png)

3. 在第二個步驟中，系統會針對 OMS 入口網站中的警示列出適當的[動作群組](monitoring-action-groups.md)，來顯示建議的變更摘要。 如果在多個警示中看到類似動作 - 系統會提議將全部的動作與單一動作群組建立關聯。  所提議的動作群組需遵循命名慣例：WorkspaceName_AG_#Number。 若要繼續，請按 [下一步]。
以下是範例畫面。

    ![將警示從 OMS 入口網站延伸至 Azure - 步驟 2](./media/monitor-alerts-extend/ExtendStep2.png)


4. 在精靈的最後一個步驟中，您可以要求 OMS 入口網站排定將所有警示延伸至 Azure - 方法是建立新的「動作群組」，並將它們與警示建立關聯，如先前的畫面所示。 若要繼續選擇，請按一下 [完成]，並在提示字元中確認以起始程序。 (選擇性) 客戶也可以提供要讓 OMS 入口網站在處理完成時將報告傳送給他們的電子郵件地址。

    ![將警示從 OMS 入口網站延伸至 Azure - 步驟 3](./media/monitor-alerts-extend/ExtendStep3.png)

5. 一但精靈完成後，控制項將會回到 [警示設定] 頁面，且「擴充至 Azure」選項會加以移除。 在背景中，OMS 入口網站會針對要延伸至 Azure 的 Log Analytics 中警示進行排程；這可能需要一些時間，而當作業開始進行一小段時間後，OMS 入口網站中的警示即不可供修改。 目前的狀態會透過橫幅顯示，且如果在步驟 4 中提供了電子郵件地址，則當背景程序成功將所有警示擴充至 Azure 時，使用者就會收到通知。 

6. 警示會繼續列在 OMS 入口網站中，即使在成功延伸至 Azure 之後也一樣。

    ![將 OMS 入口網站中的警示延伸至 Azure 之後](./media/monitor-alerts-extend/PostExtendList.png)


## <a name="option-2---using-api"></a>選項 2 - 使用 API
客戶如果想要透過程式設計方式，控制將 OMS 入口網站中警示延伸至 Azure 的程序或將此程序自動化，Microsoft 在 Log Analytics 底下已提供新的 AlertsVersion API。

Log Analytics AlertsVersion API 是 RESTful，可透過 Azure Resource Manager REST API 來存取。 在這份文件中，您可以找到透過 [ARMClient](https://github.com/projectkudu/ARMClient) 從 PowerShell 命令列存取 API 的範例，這是一個開放原始碼命令列工具，可簡化叫用 Azure Resource Manager API。 使用 ARMClient 和 PowerShell 是存取 API 的許多選項之一。 API 會以 JSON 格式來輸出結果，可讓您以程式設計方式將結果用在許多不同用途上。

藉由針對 API 使用 GET，使用者可以在結果中取得建議的變更摘要，這會以適用於 OMS 入口網站中警示的適當[動作群組](monitoring-action-groups.md)形式列出 (JSON 格式)。 如果在多個警示中看到類似動作，系統會提議將全部的動作與單一動作群組建立關聯。  所提議的動作群組需遵循命名慣例：WorkspaceName_AG_#Number。

```
armclient GET  /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

> [!NOTE]
> 對 API 進行的 GET 呼叫並不會使 OMS 入口網站中的警示延伸至 Azure。 它只會提供所建議的變更摘要作為回應。 若要確認完成這些變更以將警示擴充至 Azure，必須完成對 API 的 POST 呼叫。

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

若要起始將 OMS 入口網站中的警示延伸至 Azure 的排程作業，請對 API 起始 POST。 進行此呼叫/命令可確認使用者是否想要及接受將他們在 OMS 入口網站中的警示延伸至 Azure，以及執行在對 API 進行之 GET 呼叫的回應中所指示的變更。 使用者也可以視需要提供電子郵件地址清單，以在所排定將 OMS 入口網站中警示延伸至 Azure 的背景程序成功完成時，接收 OMS 入口網站寄送的相關報告。

```
$emailJSON = “{‘Recipients’: [‘a@b.com’, ‘b@a.com’]}”
armclient POST  /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview $emailJSON
```

> [!NOTE]
> 將 OMS 入口網站警示延伸至 Azure 的結果可能會因系統中進行的任何變更，而與 GET 所提供的摘要有所不同。 排定之後，OMS 入口網站中的警示會暫時無法供編輯/修改 - 但可以建立新的警示。 

如果 POST 成功，就會傳回 200 OK 回應以及：
```json
{
    "version": 2
}
```
指出警示已擴充至 Azure 中，如第 2 版所示。 這個版本僅供檢查警示是否已擴充至 Azure，且與 [Log Analytics 搜尋 API](../log-analytics/log-analytics-api-alerts.md) 使用方式並無任何關聯。 將警示成功延伸至 Azure 之後，在 GET 期間提供的所有電子郵件地址，都會收到含有所進行變更之詳細資料的報告。

最後，如果指定工作區中的所有警示都已排程要擴充至 Azure，則對 POST 的回應將會是 403 禁止。 若要檢視任何錯誤訊息，或瞭解擴充程序是否卡住，使用者可以執行 GET 呼叫，而如有任何錯誤訊息，則會與摘要一起傳回。

```json
{
    "version": 1,
    "message": "OMS was unable to extend your alerts into Azure, Error: The subscription is not registered to use the namespace 'microsoft.insights'. OMS will schedule extending your alerts, once remediation steps illustrated in the troubleshooting guide are done.",
    "recipients": [
       "john.doe@email.com",
       "jane.doe@email.com"
     ],
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

## <a name="troubleshooting"></a>疑難排解 
將警示從 OMS 擴充至 Azure 期間，可能會發生防止系統建立所需[動作群組](monitoring-action-groups.md)的偶發問題。 在這類情況下，錯誤訊息會顯示在 OMS 入口網站 (透過 [警示] 區段中的橫幅) 和對 API 所進行的 GET 呼叫中。

以下列出每個錯誤的補救步驟：
1. **錯誤：訂用帳戶未註冊為使用命名空間 'microsoft.insights'**：![具有註冊錯誤訊息的 OMS 入口網站 [警示設定] 頁面](./media/monitor-alerts-extend/ErrorMissingRegistration.png)

    a. 與您的 OMS 工作區相關聯的訂用帳戶未註冊為使用 Azure 監視器 (microsoft.insights) 功能，因此 OMS 無法將警示擴充至 Azure 監視器和警示。
    
    b. 若要解決此問題，請使用 Powershell、Azure CLI 或 Azure 入口網站，在您的訂用帳戶中註冊 microsoft.insights (Azure 監視器和警示) 使用。 若要深入了解，請檢視[解決資源提供者註冊錯誤](../azure-resource-manager/resource-manager-register-provider-errors.md)文章
    
    c. 根據文章中所述的步驟解決問題後，OMS 就會在隔天的排定執行中將警示擴充至 Azure，而不需進行任何動作或初始。
2. **錯誤：寫入作業的訂用帳戶/資源群組層級出現範圍鎖定**：![具有 ScopeLock 錯誤訊息的 OMS 入口網站 [警示設定] 頁面](./media/monitor-alerts-extend/ErrorScopeLock.png)

    a. 如果已啟用範圍鎖定，則會限制在包含 Log Analytics (OMS) 工作區的訂用帳戶或資源群組中進行任何新的變更；系統無法將警示擴充 (複製) 到 Azure 和建立所需的動作群組。
    
    b. 若要解決此問題，請使用 Azure 入口網站、Powershell、Azure CLI 或 API，在包含此工作區的訂用帳戶或資源群組上刪除 ReadOnly 鎖定。 若要深入了解，請檢視[資源鎖定使用方式](../azure-resource-manager/resource-group-lock-resources.md)文章。 
    
    c. 根據文章中所述的步驟解決問題後，OMS 就會在隔天的排定執行中將警示擴充至 Azure，而不需進行任何動作或初始。


## <a name="next-steps"></a>後續步驟

* 深入了解新的 [Azure 警示體驗](monitoring-overview-unified-alerts.md)。
* 了解 [Azure 警示中的記錄警示](monitor-alerts-unified-log.md)。
