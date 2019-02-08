---
title: 將警示從 Log Analytics 延伸至 Azure Government Cloud
description: 本文說明可用來將警示從 Log Analytics 延伸至 Azure 警示的工具和 API。
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/04/2018
ms.author: vinagara
ms.subservice: alerts
ms.openlocfilehash: 9d734f74c4e12b369e46c15dcb9d01a8185dddd6
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2019
ms.locfileid: "55103372"
---
# <a name="extend-alerts-from-log-analytics-into-azure-alerts"></a>將警示從 Log Analytics 延伸至 Azure 警示
OMS 入口網站中的警示功能正由 Azure Government Cloud 中的「Azure 警示」取代。 作為此轉換的一部分，您最初在 Log Analytics 中設定的警示將會延伸至 Azure。 如果不想等警示自動移動到 Azure 中，您可以起始此程序：

- 從 Operations Management Suite 入口網站手動執行。 
- 使用 AlertsVersion API 以程式設計方式執行。  

> [!NOTE]
> 從 2019 年 3 月 1 日起，Microsoft 會以有條理的方式，自動將在 Azure Government OMS 入口網站的 Log Analytics 執行個體中建立的警示延伸至「Azure 警示」。 如果您建立[動作群組](../../azure-monitor/platform/action-groups.md)時遇到問題，請使用[這些補救步驟](alerts-extend-tool.md#troubleshooting)讓系統自動建立動作群組。 您可以在 Azure Government OMS 入口網站中使用這些步驟，直到 2019 年 3 月 15 日止。

## <a name="option-1-initiate-from-the-operations-management-suite-portal"></a>選項 1：從 Operations Management Suite 入口網站起始
下列步驟說明如何從 Azure Government Cloud 的 Operations Management Suite 入口網站延伸工作區的警示。  

1. 在 Azure 入口網站中，選取 [所有服務]。 在資源清單中輸入 **Log Analytics**。 當您開始輸入時，清單會根據您輸入的文字進行篩選。 選取 [Log Analytics]。
2. 在 [Log Analytics 訂用帳戶] 窗格中，選取工作區，然後選取 [OMS 入口網站] 圖格。
![Log Analytics 訂用帳戶窗格螢幕擷取畫面，其中反白顯示 OMS 入口網站圖格](media/alerts-extend-tool/azure-portal-01.png) 
3. 將您重新導向至 Operations Management Suite 入口網站之後，請選取 [設定] 圖示。
![Operations Management Suite 入口網站的螢幕擷取畫面，其中反白顯示 [設定] 圖示](media/alerts-extend-tool/oms-portal-settings-option.png) 
4. 在 [設定] 頁面中，選取 [警示]。  
5. 選取 [延伸至 Azure]。
![Operations Management Suite 入口網站 [警示設定] 頁面的螢幕擷取畫面，其中反白顯示 [延伸至 Azure]](media/alerts-extend-tool/ExtendInto.png)
6. 三步驟精靈會出現在 [警示] 窗格中。 閱讀概觀，並選取 [下一步]。
![精靈的步驟 1 螢幕擷取畫面](media/alerts-extend-tool/ExtendStep1.png)  
7. 在第二個步驟中，您會看到建議變更的摘要，其中列出適用於警示的[動作群組](../../azure-monitor/platform/action-groups.md)。 如果在多個警示中看到類似動作，精靈會提議將單一動作群組與這些動作建立關聯。  命名慣例如下︰*WorkspaceName_AG_#Number*。 若要繼續，請選取 [下一步]。
![精靈的步驟 2 螢幕擷取畫面](media/alerts-extend-tool/ExtendStep2.png)  
8. 在精靈的最後一個步驟中，選取 [完成]，並在系統提示您時確認以起始程序。 或者，您可以提供電子郵件地址，以便在程序完成且所有警示都已成功移至 Azure 警示時收到通知。
![精靈的步驟 3 螢幕擷取畫面](media/alerts-extend-tool/ExtendStep3.png)

精靈完成時，在 [警示設定] 頁面上，將警示延伸至 Azure 的選項會移除。 在背景中，警示會移動到 Azure，這可能需要一些時間。 在作業期間，您無法從 Operations Management Suite 入口網站對警示進行變更。 您可以在入口網站頂端的橫幅中看到目前狀態。 如果您先前已提供電子郵件地址，則程序順利完成時，您會收到電子郵件。  


警示會持續列在 Operations Management Suite 入口網站中，即使在成功移至 Azure 之後也是。
![Operations Management Suite 入口網站警示設定頁面的螢幕擷取畫面](media/alerts-extend-tool/PostExtendList.png)


## <a name="option-2-use-the-alertsversion-api"></a>選項 2：使用 AlertsVersion API
您可以從可呼叫 REST API 的任何用戶端使用 Log Analytics AlertsVersion API，將警示從 Log Analytics 延伸至 Azure 警示。 您可以使用開放原始碼命令列工具 [ARMClient](https://github.com/projectkudu/ARMClient) 從 PowerShell 存取 API。 您可以輸出 JSON 的結果。  

若要使用 API，首先要建立 GET 要求。 這會評估並傳回建議變更的摘要，然後再使用 POST 要求嘗試實際延伸至 Azure。 結果會以 JSON 格式列出警示和建議的[動作群組](../../azure-monitor/platform/action-groups.md)清單。 如果在多個警示中看到類似動作，服務會提議將這些動作與單一動作群組建立關聯。 命名慣例如下︰*WorkspaceName_AG_#Number*。

```
armclient GET  /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

如果 GET 要求成功，會傳回 HTTP 狀態碼 200，以及 JSON 資料中的警示清單和建議動作群組。 以下是範例回應：

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
                            "serviceUri": "https://test.com"
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
如果指定的工作區並未定義任何警示規則，則 JSON 資料會傳回下列結果：

```json
{
    "version": 1,
    "Message": "No Alerts found in the workspace for migration."
}
```

如果指定工作區中的所有警示規則都已延伸至 Azure，GET 要求的回應會是：

```json
{
    "version": 2
}
```

若要起始將警示移轉至 Azure，請起始 POST 回應。 POST 回應會確認您的目的並接受，以讓警示從 Log Analytics 延伸至 Azure 警示。 活動已排程，且警示已根據先前執行 GET 回應的結果進行處理。 或者，您可以提供電子郵件地址清單，在移轉警示的排程背景程序成功完成時，讓 Log Analytics 傳送報告。 您可以使用下列要求範例：

```
$emailJSON = “{‘Recipients’: [‘a@b.com’, ‘b@a.com’]}”
armclient POST  /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview $emailJSON
```

> [!NOTE]
> 將警示移轉至 Azure 警示的結果可能會根據 GET 回應所提供的摘要而有不同。 排程時，會暫時無法在 Operations Management Suite 入口網站中修改 Log Analytics 中的警示。 不過，您可以建立新的警示。 

如果 POST 要求成功，會傳回 HTTP 200 OK 狀態以及下列回應：

```json
{
    "version": 2
}
```

此回應指出警示已成功延伸至 Azure 警示。 版本屬性僅供檢查警示是否已延伸至 Azure，與 [Log Analytics 搜尋 API](../../azure-monitor/platform/api-alerts.md) 並無任何關聯。 當警示成功延伸至 Azure，會將報告傳送到與 POST 要求一起提供的所有電子郵件地址。 如果指定工作區中的所有警示都已排程要進行延伸，則對 POST 要求的回應會是嘗試已禁止 (403 狀態碼)。 若要檢視任何錯誤訊息，或了解程序是否卡住，您可以提交 GET 要求。 如果有錯誤訊息，它會傳回，並附上摘要資訊。

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
                            "serviceUri": "https://test.com"
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
在延伸警示的程序中，問題可能導致系統無法建立所需的[動作群組](../../azure-monitor/platform/action-groups.md)。 在這種情況下，您會在 Operations Management Suite 入口網站 [警示] 區段的橫幅中看到錯誤訊息，或在對 API 執行的 GET 呼叫中看到。

> [!IMPORTANT]
> 如果 Azure Government Cloud 型 OMS 入口網站使用者未在 2019 年 3 月 15 日之前採取下列補救步驟，則警示會在 Azure 中執行，但不會觸發任何動作或通知。 若要取得警示通知，您必須在 Azure 中手動編輯其警示規則並新增[動作群組](../../azure-monitor/platform/action-groups.md)

以下是每個錯誤的修復步驟：
- **錯誤：範圍鎖定出現在寫入作業的訂用帳戶/資源群組層級**： ![Operations Management Suite 入口網站 [警示設定] 頁面的螢幕擷取畫面，其中反白顯示「範圍鎖定」錯誤訊息](media/alerts-extend-tool/ErrorScopeLock.png)

    啟用範圍鎖定時，此功能會限制包含 Log Analytics (Operations Management Suite) 工作區的訂用帳戶或資源群組中的任何新變更。 系統無法將警示延伸至 Azure，並建立必要的動作群組。
    
    若要解決，請刪除含有工作區的訂用帳戶或資源群組上的 *ReadOnly* 鎖定。 您可以使用 Azure 入口網站、PowerShell、Azure CLI 或 API 來執行這項工作。 若要深入了解，請參閱[資源鎖定使用方式](../../azure-resource-manager/resource-group-lock-resources.md)。 
    
    在您使用文章所述步驟來解決錯誤後，Operations Management Suite 會在隔日的排定執行內將警示延伸到 Azure。 您不需要採取任何進一步的動作或起始任何項目。

- **錯誤：原則出現在訂用帳戶/資源群組層級**： ![Operations Management Suite 入口網站 [警示設定] 頁面的螢幕擷取畫面，其中反白顯示「原則」錯誤訊息](media/alerts-extend-tool/ErrorPolicy.png)

    套用 [Azure 原則](../../governance/policy/overview.md)時，它會限制包含 Log Analytics (Operations Management Suite) 工作區的訂用帳戶或資源群組中的任何新資源。 系統無法將警示延伸至 Azure，並建立必要的動作群組。
    
    若要解決，請編輯引起 *[RequestDisallowedByPolicy](../../azure-resource-manager/resource-manager-policy-requestdisallowedbypolicy-error.md)* 錯誤的原則，該錯誤會妨礙在您的訂用帳戶或資源群組上，建立包含工作區的新資源。 您可以使用 Azure 入口網站、PowerShell、Azure CLI 或 API 來執行這項工作。 您可以稽核動作以尋找造成失敗的原則。 若要深入了解，請參閱[檢視活動記錄以稽核動作](../../azure-resource-manager/resource-group-audit.md)。 
    
    在您使用文章所述步驟來解決錯誤後，Operations Management Suite 會在隔日的排定執行內將警示延伸到 Azure。 您不需要採取任何進一步的動作或起始任何項目。


## <a name="next-steps"></a>後續步驟

* 深入了解新的 [Azure 警示體驗](../../azure-monitor/platform/alerts-overview.md)。
* 了解 [Azure 警示中的記錄警示](alerts-unified-log.md)。

