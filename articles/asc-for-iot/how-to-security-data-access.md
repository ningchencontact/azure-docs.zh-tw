---
title: 存取資料的 IoT 預覽使用 ASC |Microsoft Docs
description: 深入了解如何使用 IoT ASC 時存取您的安全性警示和建議資料。
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: fbd96ddd-cd9f-48ae-836a-42aa86ca222d
ms.service: ascforiot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/25/2019
ms.author: mlottner
ms.openlocfilehash: e394f6025f7898aad7dde7b1acefd9f95029a554
ms.sourcegitcommit: cf971fe82e9ee70db9209bb196ddf36614d39d10
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2019
ms.locfileid: "58541988"
---
# <a name="access-your-security-data"></a>存取您的安全性資料 

> [!IMPORTANT]
> Iot 的 ASC 目前處於公開預覽狀態。
> 此預覽版的服務等級協定，不提供，且不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

Iot 的 ASC 儲存安全性警示、 建議和未經處理的安全性資料 （如果您選擇將它儲存） 在您的 Log Analytics 工作區中。

## <a name="log-analytics"></a>Log Analytics

若要設定在使用 Log Analytics 工作區：

1. 開啟 IoT 中樞。
1. 按一下 **安全性**
2. 按一下 **設定**，並變更您的 Log Analytics 工作區設定。

若要存取您的 Log Analytics 工作區設定之後：

1. 在 ASC for IoT 中選擇警示。 
2. 按一下 **進一步調查**，然後按一下**若要查看哪些裝置有此警示，請按一下這裡並檢視 DeviceId 欄**。

如需從 Log Analytics 查詢資料的詳細資訊，請參閱[開始使用 Log Analytics 中的查詢](https://docs.microsoft.com//azure/log-analytics/query-language/get-started-queries)。

## <a name="security-alerts"></a>安全性警示

安全性警示會儲存在**ASCforIoT.SecurityAlert**您設定的 Log Analytics 工作區中的資料表。

使用下列的基本 kql 查詢來開始探索安全性警示。

### <a name="sample-records-query"></a>記錄查詢範例

若要隨機選取一些記錄： 

```
// Select a few random records
//
SecurityAlert
| project 
    TimeGenerated, 
    IoTHubId=ResourceId, 
    DeviceId=tostring(parse_json(ExtendedProperties)["DeviceId"]),
    AlertSeverity, 
    DisplayName,
    Description,
    ExtendedProperties
| take 3
```

#### <a name="sample-query-results"></a>查詢結果範例 

| TimeGenerated           | IoTHubId                                                                                                       | deviceId      | AlertSeverity | DisplayName                           | 描述                                             | ExtendedProperties                                                                                                                                                             |
|-------------------------|----------------------------------------------------------------------------------------------------------------|---------------|---------------|---------------------------------------|---------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 2018-11-18T18:10:29.000 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | 高          | 成功的暴力密碼破解攻擊           | 在裝置上的暴力密碼破解強制攻擊成功。        |    { "Full Source Address": "[\"10.165.12.18:\"]", "User Names": "[\"\"]", "DeviceId":"IoT-Device-Linux" }                                                                       |
| 2018-11-19T12:40:31.000 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | 高          | 成功在裝置上的本機登入      | 偵測到成功的本機登入裝置     | { "Remote Address": "?", "Remote Port": "", "Local Port": "", "Login Shell": "/bin/su", "Login Process Id":"28207", "User Name": "attacker", "DeviceId":"IoT-Device-Linux" } |
| 2018-11-19T12:40:31.000 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | 高          | 在裝置上的本機登入嘗試失敗  | 偵測到裝置的本機登入失敗的嘗試 |  { "Remote Address": "?", "Remote Port": "", "Local Port": "", "Login Shell": "/bin/su", "Login Process Id":"22644", "User Name": "attacker", "DeviceId":"IoT-Device-Linux" } |

### <a name="device-summary-query"></a>裝置摘要查詢

使用此 kql 查詢，以選取幾個不同的安全性警示的偵測到 IoT 中樞、 裝置、 警示嚴重性、 警示類型的最後一週。

```
// Select number of distinct security alerts detected last week by 
//   IoT hub, device, alert severity, alert type
//
SecurityAlert
| where TimeGenerated > ago(7d)
| summarize Cnt=dcount(SystemAlertId) by
    IoTHubId=ResourceId, 
    DeviceId=tostring(parse_json(ExtendedProperties)["DeviceId"]),
    AlertSeverity,
    DisplayName
```

#### <a name="device-summary-query-results"></a>裝置摘要查詢結果

| IoTHubId | deviceId| AlertSeverity| DisplayName | 計數 |
|----------|---------|------------------|---------|---------|
|/subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | 高          | 成功的暴力密碼破解攻擊           | 9   |    
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | 中        | 在裝置上的本機登入嘗試失敗  | 242 |    
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | 高          | 成功在裝置上的本機登入      | 31  |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | 中        | 密碼編譯錢幣來比喻 Miner                     | 4   |
|

### <a name="iot-hub-summary"></a>IoT 中樞摘要

使用此 kql 查詢選取過去一週中，有警示，IoT 中樞、 警示嚴重性、 警示類型的不同裝置的數目：

```
// Select number of distinct devices which had alerts in the last week, by 
//   IoT hub, alert severity, alert type
//
SecurityAlert
| where TimeGenerated > ago(7d)
| extend DeviceId=tostring(parse_json(ExtendedProperties)["DeviceId"])
| summarize CntDevices=dcount(DeviceId) by
    IoTHubId=ResourceId, 
    AlertSeverity,
    DisplayName
```

#### <a name="iot-hub-summary-query-results"></a>IoT 中樞摘要查詢結果

| IoTHubId                                                                                                       | AlertSeverity | DisplayName                           | CntDevices |
|----------------------------------------------------------------------------------------------------------------|---------------|---------------------------------------|------------|
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | 高          | 成功的暴力密碼破解攻擊           | 1          |    
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | 中        | 在裝置上的本機登入嘗試失敗  | 1          | 
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | 高          | 成功在裝置上的本機登入      | 1          |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | 中        | 密碼編譯錢幣來比喻 Miner                     | 1          |



## <a name="next-steps"></a>後續步驟

- 閱讀 IoT ASC[概觀](overview.md)
- 了解 ASC IoT[架構](architecture.md)
- 了解和探索[ASC IoT 警示](concept-security-alerts.md)
