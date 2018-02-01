---
title: "針對 Azure 活動記錄警示呼叫 Webhook | Microsoft Docs"
description: "將活動記錄事件路由至其他服務以進行自訂動作。 例如傳送簡訊、記錄 Bug、或透過聊天/傳訊服務來通知團隊。"
author: johnkemnetz
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 64d333d1-7f37-4a00-9d16-dda6e69a113b
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: johnkem
ms.openlocfilehash: 08467aed4e1601b32598fc42515d9c38b601a9d4
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/19/2018
---
# <a name="call-a-webhook-on-azure-activity-log-alerts"></a>針對 Azure 活動記錄警示呼叫 Webhook
Webhook 可讓您將 Azure 警示通知路由到其他系統以進行後處理或自訂動作。 您可以針對警示使用 Webhook，以將警示路由到會傳送簡訊、記錄錯誤、透過聊天/傳訊服務通知小組，或進行任意數量之其他動作的服務。 本文說明如何設定 Webhook，以在引發 Azure 活動記錄警示時加以呼叫。 另外也會說明 HTTP POST 至 Webhook 的承載資料樣貌。 如需有關 Azure 度量警示之設定和結構描述的資訊，[請改為參閱本頁](insights-webhooks-alerts.md)。 您也可以將活動記錄警示設定為在啟動時傳送電子郵件。

> [!NOTE]
> 這項功能目前為預覽狀態，並將在未來某個時候移除。
>
>

您可以使用 [Azure PowerShell Cmdlet](insights-powershell-samples.md#create-metric-alerts)、[跨平台 CLI](insights-cli-samples.md#work-with-alerts) 或 [Azure 監視器 REST API](https://msdn.microsoft.com/library/azure/dn933805.aspx) 設定活動記錄警示。 目前，您無法使用 Azure 入口網站進行設定。

## <a name="authenticating-the-webhook"></a>驗證 Webhook
Webhook 可使用下列其中一種方法來進行驗證︰

1. **權杖型授權**：所儲存的 Webhook URI 具有權杖識別碼，例如 `https://mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue`
2. **基本授權**：所儲存的 Webhook URI 具有使用者名稱和密碼，例如 `https://userid:password@mysamplealert/webcallback?someparamater=somevalue&foo=bar`

## <a name="payload-schema"></a>承載結構描述
POST 作業對於所有以活動記錄為基礎的警示會包含下列 JSON 承載和結構描述。 此結構描述類似於度量型警示所使用的結構描述。

```json
{
    "WebhookName": "Alert1515526229589",
    "RequestBody": {
        "schemaId": "Microsoft.Insights/activityLogs",
        "data": {
            "status": "Activated",
            "context": {
                "activityLog": {
                    "authorization": {
                        "action": "Microsoft.Compute/virtualMachines/deallocate/action",
                        "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoVM/providers/Microsoft.Compute/virtualMachines/ContosoVM1"
                    },
                    "channels": "Operation",
                    "claims": {
                        "aud": "https://management.core.windows.net/",
                        "iss": "https://sts.windows.net/00000000-0000-0000-0000-000000000000/",
                        "iat": "1234567890",
                        "nbf": "1234567890",
                        "exp": "1234567890",
                        "aio": "Y2NgYBD8ZLlhu27JU6WZsXemMIvVAAA=",
                        "appid": "00000000-0000-0000-0000-000000000000",
                        "appidacr": "2",
                        "e_exp": "262800",
                        "http://schemas.microsoft.com/identity/claims/identityprovider": "https://sts.windows.net/00000000-0000-0000-0000-000000000000/",
                        "http://schemas.microsoft.com/identity/claims/objectidentifier": "00000000-0000-0000-0000-000000000000",
                        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "00000000-0000-0000-0000-000000000000",
                        "http://schemas.microsoft.com/identity/claims/tenantid": "00000000-0000-0000-0000-000000000000",
                        "uti": "XnCk46TrDkOQXwo49Y8fAA",
                        "ver": "1.0"
                    },
                    "caller": "00000000-0000-0000-0000-000000000000",
                    "correlationId": "00000000-0000-0000-0000-000000000000",
                    "description": "",
                    "eventSource": "Administrative",
                    "eventTimestamp": "2018-01-09T20:11:25.8410967+00:00",
                    "eventDataId": "00000000-0000-0000-0000-000000000000",
                    "level": "Informational",
                    "operationName": "Microsoft.Compute/virtualMachines/deallocate/action",
                    "operationId": "00000000-0000-0000-0000-000000000000",
                    "resourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoVM/providers/Microsoft.Compute/virtualMachines/ContosoVM1",
                    "resourceGroupName": "ContosoVM",
                    "resourceProviderName": "Microsoft.Compute",
                    "status": "Succeeded",
                    "subStatus": "",
                    "subscriptionId": "00000000-0000-0000-0000-000000000000",
                    "submissionTimestamp": "2018-01-09T20:11:40.2986126+00:00",
                    "resourceType": "Microsoft.Compute/virtualMachines"
                }
            },
            "properties": {}
        }
    },
    "RequestHeader": {
        "Expect": "100-continue",
        "Host": "s1events.azure-automation.net",
        "User-Agent": "IcMBroadcaster/1.0",
        "X-CorrelationContext": "RkkKACgAAAACAAAAEADlBbM7x86VTrHdQ2JlmlxoAQAQALwazYvJ/INPskb8S5QzgDk=",
        "x-ms-request-id": "00000000-0000-0000-0000-000000000000"
    }
}
```

| 元素名稱 | 說明 |
| --- | --- |
| status |用於度量警示。 針對活動記錄警示，一律設為「啟動」。 |
| context |事件的內容。 |
| activityLog | 事件的記錄屬性。|
| 授權 |事件的 RBAC 屬性。 這些屬性通常包括「動作」、「角色」和「範圍」。 |
| 動作 | 警示所擷取的動作。 |
| scope | 警示的範圍 (也就是 資源)。|
| 通道 | 作業 |
| claims | 資訊的集合與宣告相關。 |
| 呼叫者 |已執行作業之使用者的 GUID 或使用者名稱，根據可用性的 UPN 宣告或 SPN 宣告。 特定系統呼叫可為 Null。 |
| correlationId |通常是字串格式的 GUID。 具有屬於同一個較大動作的 correlationId 的事件，且通常會共用 correlationId。 |
| 說明 |警示建立期間所設定的警示說明。 |
| eventSource |產生事件的 Azure 服務或基礎結構的名稱。 |
| eventTimestamp |發生事件的時間。 |
| eventDataId |事件的唯一識別碼。 |
| 層級 |下列其中一個值：「重大」、「錯誤」、「警告」、「資訊」和「詳細資訊」。 |
| operationName |作業名稱。 |
| operationId |通常是對應至單一作業的事件之間共用的 GUID。 |
| ResourceId |受影響資源的資源識別碼。 |
| resourceGroupName |受影響資源的資源群組的名稱 |
| resourceProviderName |受影響資源的資源提供者。 |
| status |字串。 作業的狀態。 常見的值包括︰「已啟動」、「進行中」、「成功」、「失敗」、「使用中」、「已解決」。 |
| 子狀態 |通常包含對應 REST 呼叫的 HTTP 狀態碼。 它也可以包含其他描述子狀態的字串。 常見子狀態的值包括：確定 (HTTP 狀態碼︰200)，已建立 (HTTP 狀態碼︰201)、接受 (HTTP 狀態碼︰202)、沒有內容 (HTTP 狀態碼︰204)、不正確的要求 (HTTP 狀態碼︰400)、找不到 (HTTP 狀態碼︰404)，衝突 (HTTP 狀態碼︰409)、內部伺服器錯誤 (HTTP 狀態碼︰500)、服務無法使用 (HTTP 狀態碼︰503)、閘道逾時 (HTTP 狀態碼︰504) |
| subscriptionId |Azure 訂用帳戶識別碼。 |
| submissionTimestamp |處理要求的 Azure 服務產生事件的時間。 |
| resourceType | 產生事件的資源類型。|
| properties |一組包含事件相關詳細資料的 `<Key, Value>` 配對 (也就是 `Dictionary<String, String>`)。 |

## <a name="next-steps"></a>後續步驟
* [深入了解活動記錄](monitoring-overview-activity-logs.md)
* [對 Azure 警示執行 Azure 自動化指令碼 (Runbook)](http://go.microsoft.com/fwlink/?LinkId=627081)
* [使用邏輯應用程式透過 Twilio 從 Azure 警示傳送簡訊](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app)。 此範例適用於度量警示，但經過修改後即可用於活動記錄警示。
* [使用邏輯應用程式從 Azure 警示傳送 Slack 訊息](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app)。 此範例適用於度量警示，但經過修改後即可用於活動記錄警示。
* [使用邏輯應用程式從 Azure 警示傳送訊息到 Azure 佇列](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app)。 此範例適用於度量警示，但經過修改後即可用於活動記錄警示。
