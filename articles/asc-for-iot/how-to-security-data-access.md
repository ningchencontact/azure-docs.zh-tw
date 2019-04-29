---
title: 使用 Azure 資訊安全中心的 IoT 預覽存取資料 |Microsoft Docs
description: 深入了解如何使用 iot 的 Azure 資訊安全中心時存取您的安全性警示和建議資料。
services: asc-for-iot
ms.service: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: fbd96ddd-cd9f-48ae-836a-42aa86ca222d
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/25/2019
ms.author: mlottner
ms.openlocfilehash: 0788a62de701cb85a65c7053006e28cf4fffe5cb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61357198"
---
# <a name="access-your-security-data"></a>存取您的安全性資料 

> [!IMPORTANT]
> 適用於 IoT 的 Azure 資訊安全中心目前為公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

IoT 的 azure 資訊安全中心 (ASC) 會儲存安全性警示、 建議和未經處理的安全性資料 （如果您選擇將它儲存） 在您的 Log Analytics 工作區中。

## <a name="log-analytics"></a>Log Analytics

若要設定在使用 Log Analytics 工作區：

1. 開啟 IoT 中樞。
1. 按一下 **安全性**
2. 按一下 **設定**，並變更您的 Log Analytics 工作區設定。

若要存取您的 Log Analytics 工作區設定之後：

1. 選擇 iot 在 ASC 警示或建議。 
2. 按一下 **進一步調查**，然後按一下**若要查看哪些裝置有此警示，請按一下這裡並檢視 DeviceId 欄**。

如需從 Log Analytics 查詢資料的詳細資訊，請參閱[開始使用 Log Analytics 中的查詢](https://docs.microsoft.com//azure/log-analytics/query-language/get-started-queries)。

## <a name="security-alerts"></a>安全性警示

安全性警示會儲存在_AzureSecurityOfThings.SecurityAlert_ IoT 解決方案 ASC 設定 Log Analytics 工作區中的資料表。

我們提供一些有用的查詢，可協助您立即開始探索安全性警示。

### <a name="sample-records"></a>範例記錄

選取幾個隨機資料錄

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

| TimeGenerated           | IoTHubId                                                                                                       | deviceId      | AlertSeverity | DisplayName                           | 描述                                             | ExtendedProperties                                                                                                                                                             |
|-------------------------|----------------------------------------------------------------------------------------------------------------|---------------|---------------|---------------------------------------|---------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 2018-11-18T18:10:29.000 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | 高          | 成功的暴力密碼破解攻擊           | 在裝置上的暴力密碼破解強制攻擊成功。        |    { "Full Source Address": "[\"10.165.12.18:\"]", "User Names": "[\"\"]", "DeviceId":"IoT-Device-Linux" }                                                                       |
| 2018-11-19T12:40:31.000 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | 高          | 成功在裝置上的本機登入      | 偵測到成功的本機登入裝置     | { "Remote Address": "?", "Remote Port": "", "Local Port": "", "Login Shell": "/bin/su", "Login Process Id":"28207", "User Name": "attacker", "DeviceId":"IoT-Device-Linux" } |
| 2018-11-19T12:40:31.000 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | 高          | 在裝置上的本機登入嘗試失敗  | 偵測到裝置的本機登入失敗的嘗試 |  { "Remote Address": "?", "Remote Port": "", "Local Port": "", "Login Shell": "/bin/su", "Login Process Id":"22644", "User Name": "attacker", "DeviceId":"IoT-Device-Linux" } |

### <a name="device-summary"></a>裝置摘要

選取 IoT 中樞、 裝置、 警示嚴重性、 警示類型所偵測到過去一週的獨特的安全性警示數目。

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

| IoTHubId                                                                                                       | deviceId      | AlertSeverity | DisplayName                           | 計數 |
|----------------------------------------------------------------------------------------------------------------|---------------|---------------|---------------------------------------|-----|
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | 高          | 成功的暴力密碼破解攻擊           | 9   |   
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | 中        | 在裝置上的本機登入嘗試失敗  | 242 |    
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | 高          | 成功在裝置上的本機登入      | 31  |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | 中        | 密碼編譯錢幣來比喻 Miner                     | 4   |

### <a name="iot-hub-summary"></a>摘要的 IoT 中樞

選取不同的裝置已在過去一週，透過 IoT 中樞、 警示嚴重性、 警示類型的警示數

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

| IoTHubId                                                                                                       | AlertSeverity | DisplayName                           | CntDevices |
|----------------------------------------------------------------------------------------------------------------|---------------|---------------------------------------|------------|
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | 高          | 成功的暴力密碼破解攻擊           | 1          |    
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | 中        | 在裝置上的本機登入嘗試失敗  | 1          | 
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | 高          | 成功在裝置上的本機登入      | 1          |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | 中        | 密碼編譯錢幣來比喻 Miner                     | 1          |

## <a name="security-recommendations"></a>安全性建議

安全性建議會儲存在_AzureSecurityOfThings.SecurityRecommendation_ IoT 解決方案 ASC 設定 Log Analytics 工作區中的資料表。

我們提供了一些有用的查詢，可協助您開始探索安全性建議。

### <a name="sample-records"></a>範例記錄

選取幾個隨機資料錄

```
// Select a few random records
//
SecurityRecommendation
| project 
    TimeGenerated, 
    IoTHubId=AssessedResourceId, 
    DeviceId,
    RecommendationSeverity,
    RecommendationState,
    RecommendationDisplayName,
    Description,
    RecommendationAdditionalData
| take 2
```
    
| TimeGenerated | IoTHubId | deviceId | RecommendationSeverity | RecommendationState | RecommendationDisplayName | 描述 | RecommendationAdditionalData |
|---------------|----------|----------|------------------------|---------------------|---------------------------|-------------|------------------------------|
| 2019-03-22T10:21:06.060 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | 中 | Active | 找不到輸入鏈結中的寬鬆的防火牆規則 | 防火牆有規則包含允許廣泛 IP 位址或連接埠範圍的寬鬆模式 | {"Rules":"[{\"SourceAddress\":\"\",\"SourcePort\":\"\",\"DestinationAddress\":\"\",\"DestinationPort\":\"1337\"}]"} |
| 2019-03-22T10:50:27.237 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | 中 | Active | 找不到輸入鏈結中的寬鬆的防火牆規則 | 防火牆有規則包含允許廣泛 IP 位址或連接埠範圍的寬鬆模式 | {"Rules":"[{\"SourceAddress\":\"\",\"SourcePort\":\"\",\"DestinationAddress\":\"\",\"DestinationPort\":\"1337\"}]"} |

### <a name="device-summary"></a>裝置摘要

選取 IoT 中樞、 裝置、 建議嚴重性和類型的不同作用中的安全性建議數目。

```
// Select number of distinct active security recommendations by 
//   IoT hub, device, recommendation severity and type
//
SecurityRecommendation
| extend IoTHubId=AssessedResourceId
| summarize CurrentState=arg_max(RecommendationState, DiscoveredTimeUTC) by IoTHubId, DeviceId, RecommendationSeverity, RecommendationDisplayName
| where CurrentState == "Active"
| summarize Cnt=count() by IoTHubId, DeviceId, RecommendationSeverity
```

| IoTHubId                                                                                                       | deviceId      | RecommendationSeverity | 計數 |
|----------------------------------------------------------------------------------------------------------------|---------------|------------------------|-----|
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | 高          | 2   |    
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | 中        | 1 |  
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | 高          | 1  |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | 中        | 4   |


## <a name="next-steps"></a>後續步驟

- 閱讀 IoT ASC[概觀](overview.md)
- 了解 ASC IoT[架構](architecture.md)
- 了解和探索[ASC IoT 警示](concept-security-alerts.md)
- 了解和探索[ASC IoT 建議](concept-recommendations.md)