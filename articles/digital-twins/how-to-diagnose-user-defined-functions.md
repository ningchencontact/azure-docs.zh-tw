---
title: 如何為 Azure Digital Twins 中的 UDF 偵錯 | Microsoft Docs
description: 有關如何為 Azure Digital Twins 中的 UDF 偵錯的指導方針
author: stefanmsft
manager: deshner
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/22/2018
ms.author: stefanmsft
ms.openlocfilehash: 852b2d35ae605f5529d162d52655fd258ca07c5a
ms.sourcegitcommit: 9e179a577533ab3b2c0c7a4899ae13a7a0d5252b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/23/2018
ms.locfileid: "49946091"
---
# <a name="how-to-debug-issues-with-user-defined-functions-in-azure-digital-twins"></a>如何為 Azure Digital Twins 中使用使用者定義函式的問題偵錯

本文摘要說明如何診斷使用者定義函式。 接著，它會找出一些使用這些函式時發生的最常見案例。

## <a name="debug-issues"></a>為問題偵錯

了解如何診斷 Azure Digital Twins 執行個體內所發生的任何問題將能協助您有效地找出問題、問題的原因和解決方案。

### <a name="enable-log-analytics-for-your-instance"></a>啟用適用於您執行個體的 Log Analytics

Azure Digital Twins 執行個體的記錄和計量是透過 Azure 監視器公開的。 下列文件假設您已透過 [Azure 入口網站](../log-analytics/log-analytics-quick-create-workspace.md)、[Azure CLI](../log-analytics/log-analytics-quick-create-workspace-cli.md) 或 [PowerShell](../log-analytics/log-analytics-quick-create-workspace-posh.md) 建立 [Azure Log Analytics](../log-analytics/log-analytics-queries.md) 工作區。

> [!NOTE]
> 當您第一次將事件傳送到 **Log Analytics** 時，可能會經歷 5 分鐘的延遲。

閱讀[「收集並取用來自 Azure 資源的記錄資料」](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)一文可透過入口網站、Azure CLI 或 PowerShell，為 Azure Digital Twins 執行個體啟用診斷設定。 請務必選取所有記錄檔類別、計量，以及您的 Azure Log Analytics 工作區。

### <a name="trace-sensor-telemetry"></a>追蹤感應器遙測

請確定已在 Azure Digital Twins 執行個體上啟用診斷設定、已選取所有記錄檔類別，並已將記錄檔傳送到 Azure Log Analytics。

若要使感應器遙測訊息與其各自的記錄檔相符，您可以針對所傳送的事件資料指定相互關聯識別碼。 若要這樣做，將 `x-ms-client-request-id` 屬性設為 GUID。

傳送遙測資料之後，開啟 Azure Log Analytics，就可以使用設定的相互關聯識別碼查詢記錄檔：

```Kusto
AzureDiagnostics
| where CorrelationId = 'yourCorrelationIdentifier'
```

| 自訂屬性名稱 | 取代為 |
| --- | --- |
| *yourCorrelationIdentifier* | 針對事件資料指定的相互關聯識別碼 |

如果您記錄使用者定義函式，這些記錄檔將會出現在類別為 `UserDefinedFunction` 的 Azure Log Analytics 執行個體中。 若要擷取這些記錄檔，請在 Azure Log Analytics 中輸入下列查詢條件：

```Kusto
AzureDiagnostics
| where Category = 'UserDefinedFunction'
```

如需有關強大的查詢作業的詳細資訊，請參閱[開始使用查詢](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-queries)。

## <a name="identify-common-issues"></a>找出常見問題

為解決方案進行疑難排解時，診斷並找出常見問題很重要。 以下摘要說明開發使用者定義函式時所碰到的幾個常見問題。

### <a name="ensure-a-role-assignment-was-created"></a>請確定已建立角色指派

如果沒有在管理 API 中建立的角色指派，使用者定義函式將沒有執行任何動作的存取權，例如傳送通知、擷取中繼資料，以及在拓撲內設定計算的值。

透過管理 API 檢查使用者定義函式是否存在角色指派：

```plaintext
GET https://yourManagementApiUrl/api/v1.0/roleassignments?path=/&traverse=Down&objectId=yourUserDefinedFunctionId
```

| 自訂屬性名稱 | 取代為 |
| --- | --- |
| *yourManagementApiUrl* | 管理 API 的完整 URL 路徑  |
| *yourUserDefinedFunctionId* | 擷取其角色指派之使用者定義函式的識別碼|

如果未擷取任何角色指派，則依照本文中的[如何為使用者定義函式建立角色指派](./how-to-user-defined-functions.md)進行。

### <a name="check-if-the-matcher-will-work-for-a-sensors-telemetry"></a>檢查比對器是否適用於感應器的遙測

如果針對 Azure Digital Twins 執行個體的管理 API 進行下列呼叫，您將能夠判斷指定的比對器是否適用於指定的感應器。

```plaintext
GET https://yourManagementApiUrl/api/v1.0/matchers/yourMatcherIdentifier/evaluate/yourSensorIdentifier?enableLogging=true
```

| 自訂屬性名稱 | 取代為 |
| --- | --- |
| *yourManagementApiUrl* | 管理 API 的完整 URL 路徑  |
| *yourMatcherIdentifier* | 您想要評估之比對器的識別碼 |
| *yourSensorIdentifier* | 您想要評估之感應器的識別碼 |

回應：

```JavaScript
{
    "success": true,
    "logs": [
        "$.dataType: \"Motion\" Equals \"Motion\" => True"
    ]
}
```

### <a name="check-what-a-sensor-will-trigger"></a>檢查感應器將會觸發的項目

如果針對 Azure Digital Twins 執行個體的管理 API 進行下列呼叫，您可以判斷所指定感應器內送遙測資料觸發之使用者定義函式的識別碼：

```plaintext
GET https://yourManagementApiUrl/api/v1.0/sensors/yourSensorIdentifier/matchers?includes=UserDefinedFunctions
```

| 自訂屬性名稱 | 取代為 |
| --- | --- |
| *yourManagementApiUrl* | 管理 API 的完整 URL 路徑  |
| *yourSensorIdentifier* | 將傳送遙測資料之感應器的識別碼 |

回應：

```JavaScript
[
    {
        "id": "48a64768-797e-4832-86dd-de625f5f3fd9",
        "name": "MotionMatcher",
        "spaceId": "2117b3e1-b6ce-42c1-9b97-0158bef59eb7",
        "conditions": [
            {
                "id": "024a067a-414f-415b-8424-7df61392541e",
                "target": "Sensor",
                "path": "$.dataType",
                "value": "\"Motion\"",
                "comparison": "Equals"
            }
        ],
        "userDefinedFunctions": [
            {
                "id": "373d03c5-d567-4e24-a7dc-f993460120fc",
                "spaceId": "2117b3e1-b6ce-42c1-9b97-0158bef59eb7",
                "name": "Motion User-Defined Function",
                "disabled": false
            }
        ]
    }
]
```

### <a name="issue-with-receiving-notifications"></a>接收通知的問題

如果未收到所觸發使用者定義函式的通知，請確定您的拓撲物件類型參數符合將使用之識別碼的類型。

**錯誤**範例：

```JavaScript
var customNotification = {
    Message: 'Custom notification that will not work'
};

sendNotification(telemetry.SensorId, "Space", JSON.stringify(customNotification));
```

當拓樸物件類型為 'Space' 時，使用的識別碼指的是感應器，因此會發生這種情況。

**正確**範例：

```JavaScript
var customNotification = {
    Message: 'Custom notification that will work'
};

sendNotification(telemetry.SensorId, "Sensor", JSON.stringify(customNotification));
```

避免此問題最簡單的方式是針對中繼資料物件使用 `Notify` 方法。

範例：

```JavaScript
function process(telemetry, executionContext) {
    var sensorMetadata = getSensorMetadata(telemetry.SensorId);

    var customNotification = {
        Message: 'Custom notification'
    };

    // Short-hand for above methods where object type is known from metadata.
    sensorMetadata.Notify(JSON.stringify(customNotification));
}
```

## <a name="common-diagnostic-exceptions"></a>常見診斷例外狀況

如果您啟用診斷設定，可能會遇到下列常見的例外狀況：

1. **節流**：如果使用者定義函式超出[服務限制](./concepts-service-limits.md)一文中所述的執行速度限制，便會發生節流情況。 在限制到期之前，節流作業不需要進行任何進一步的作業，就可以成功執行。

1. **找不到資料**：如果使用者定義函式嘗試存取不存在的中繼資料，作業將會失敗。

1. **未獲授權**：如果使用者定義函式還未設定角色指派，或缺少足夠的權限存取拓撲中特定的中繼資料，作業將會失敗。

## <a name="next-steps"></a>後續步驟

了解如何在 Azure Digital Twins 中啟用[監視和記錄](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)。
