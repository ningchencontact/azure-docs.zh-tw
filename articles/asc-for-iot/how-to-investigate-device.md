---
title: IoT 裝置調查指南預覽的 ASC |Microsoft Docs
description: 此作法指南說明如何使用 iot ASC 調查可疑的 IoT 裝置，使用 Log Analytics。
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: b18b48ae-b445-48f8-9ac0-365d6e065b64
ms.service: ascforiot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2019
ms.author: mlottner
ms.openlocfilehash: 032315d1b618b9716e23c8433c6ec1bf64b7e77d
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2019
ms.locfileid: "58580534"
---
# <a name="investigate-a-suspicious-iot-device"></a>調查可疑的 IoT 裝置

> [!IMPORTANT]
> Iot 的 ASC 目前處於公開預覽狀態。
> 此預覽版的服務等級協定，不提供，且不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

可疑的可疑活動，或指示存在裝置遭到入侵的參與的 IoT 裝置時，ASC IoT 服務警示和辨識項會提供清楚的指示。 

在本指南中，使用調查所提供的建議來協助判斷您組織的潛在風險，決定如何修復，並了解預防類似攻擊的最佳方法。  

> [!div class="checklist"]
> * 尋找您的裝置資料
> * 調查使用 kql 查詢


## <a name="how-can-i-access-my-data"></a>如何存取我的資料？

根據預設，iot ASC 會在您的 Log Analytics 工作區中儲存您的安全性警示和建議。 您也可以選擇儲存您的原始安全性資料。

若要找出您的 Log Analytics 工作區來儲存資料：

1. 開啟您的 IoT 中樞 
1. 按一下 **安全性**，然後選取**設定**。
1. 變更您的 Log Analytics 工作區的組態詳細資料。 
1. 按一下 [檔案] 。 

下列組態，執行下列命令來存取儲存在您的 Log Analytics 工作區中的資料：

1. 選取並按一下 ASC 中您的 IoT 中樞的 IoT 警示。 
1. 按一下 **進一步調查**。 
1. 選取 **來查看哪些裝置有此警示，請按一下這裡並檢視 DeviceId 欄**。

## <a name="investigation-steps-for-suspicious-iot-devices"></a>可疑的 IoT 裝置的調查步驟

若要存取深入解析和關於您 IoT 裝置的未經處理資料，請移至您的 Log Analytics 工作區[存取您的資料](#how-can-i-access-my-data)。

檢查並調查下列詳細資料，並使用下列 kql 查詢活動的裝置資料。

### <a name="related-alerts"></a>相關的警示

若要了解其他警示，是否已觸發相同的時間使用周圍下列 kql 查詢：

  ~~~
  let device = "YOUR_DEVICE_ID";
  let hub = "YOUR_HUB_NAME";
  SecurityAlert
  | where ExtendedProperties contains device and ResourceId contains tolower(hub)
  | project TimeGenerated, AlertName, AlertSeverity, Description, ExtendedProperties
  ~~~

### <a name="users-with-access"></a>使用者的存取

若要了解哪些使用者可以存取此裝置，請使用下列 kql 查詢： 

  ~~~
  let device = "YOUR_DEVICE_ID";
  let hub = "YOUR_HUB_NAME";
  SecurityIoTRawEvent
  | where
     DeviceId == device and AssociatedResourceId contains tolower(hub)
     and RawEventName == "LocalUsers"
  | project
     TimestampLocal=extractjson("$.TimestampLocal", EventDetails, typeof(datetime)),
     GroupNames=extractjson("$.GroupNames", EventDetails, typeof(string)),
     UserName=extractjson("$.UserName", EventDetails, typeof(string))
  | summarize FirstObserved=min(TimestampLocal) by GroupNames, UserName
  ~~~
您可以使用這項資料，了解︰ 
  1. 哪些使用者有裝置的存取權？
  2. 具有存取權的使用者有權限等級，如預期般運作嗎？ 

### <a name="open-ports"></a>開啟連接埠

若要尋找裝置中的哪些連接埠目前正在使用中或所使用的逾時，請使用下列 kql 查詢： 

  ~~~
  let device = "YOUR_DEVICE_ID";
  let hub = "YOUR_HUB_NAME";
  SecurityIoTRawEvent
  | where
     DeviceId == device and AssociatedResourceId contains tolower(hub)
     and RawEventName == "ListeningPorts"
     and extractjson("$.LocalPort", EventDetails, typeof(int)) <= 1024 // avoid short-lived TCP ports (Ephemeral)
  | project
     TimestampLocal=extractjson("$.TimestampLocal", EventDetails, typeof(datetime)),
     Protocol=extractjson("$.Protocol", EventDetails, typeof(string)),
     LocalAddress=extractjson("$.LocalAddress", EventDetails, typeof(string)),
     LocalPort=extractjson("$.LocalPort", EventDetails, typeof(int)),
     RemoteAddress=extractjson("$.RemoteAddress", EventDetails, typeof(string)),
     RemotePort=extractjson("$.RemotePort", EventDetails, typeof(string))
  | summarize MinObservedTime=min(TimestampLocal), MaxObservedTime=max(TimestampLocal), AllowedRemoteIPAddress=makeset(RemoteAddress), AllowedRemotePort=makeset(RemotePort) by Protocol, LocalPort
  ~~~

    Use this data to discover:
  1. 目前作用中裝置上的接聽通訊端？
  2. 若允許目前作用中接聽通訊端的資訊嗎？
  3. 是否有任何可疑的遠端位址，連線到裝置？

### <a name="user-logins"></a>使用者登入

若要了解登入裝置的使用者，請使用下列 kql 查詢： 
 
  ~~~
  let device = "YOUR_DEVICE_ID";
  let hub = "YOUR_HUB_NAME";
  SecurityIoTRawEvent
  | where
     DeviceId == device and AssociatedResourceId contains tolower(hub)
     and RawEventName == "Login"
     // filter out local, invalid and failed logins
     and EventDetails contains "RemoteAddress"
     and EventDetails !contains '"RemoteAddress":"127.0.0.1"'
     and EventDetails !contains '"UserName":"(invalid user)"'
     and EventDetails !contains '"UserName":"(unknown user)"'
     //and EventDetails !contains '"Result":"Fail"'
  | project
     TimestampLocal=extractjson("$.TimestampLocal", EventDetails, typeof(datetime)),
     UserName=extractjson("$.UserName", EventDetails, typeof(string)),
     LoginHandler=extractjson("$.Executable", EventDetails, typeof(string)),
     RemoteAddress=extractjson("$.RemoteAddress", EventDetails, typeof(string)),
     Result=extractjson("$.Result", EventDetails, typeof(string))
  | summarize CntLoginAttempts=count(), MinObservedTime=min(TimestampLocal), MaxObservedTime=max(TimestampLocal), CntIPAddress=dcount(RemoteAddress), IPAddress=makeset(RemoteAddress) by UserName, Result, LoginHandler
  ~~~

    Use the query results to discover:
  1. 哪些使用者已登入裝置？
  2. 登入的使用者應該能夠登入？
  3. 登入的使用者未從預期或非預期的 IP 位址連接嗎？
  
### <a name="process-list"></a>處理程序清單

若要尋找處理程序清單是否為預期使用下列 kql 查詢： 

  ~~~
  let device = "YOUR_DEVICE_ID";
  let hub = "YOUR_HUB_NAME";
  SecurityIoTRawEvent
  | where
     DeviceId == device and AssociatedResourceId contains tolower(hub)
     and RawEventName == "ProcessCreate"
  | project
     TimestampLocal=extractjson("$.TimestampLocal", EventDetails, typeof(datetime)),
     Executable=extractjson("$.Executable", EventDetails, typeof(string)),
     UserId=extractjson("$.UserId", EventDetails, typeof(string)),
     CommandLine=extractjson("$.CommandLine", EventDetails, typeof(string))
  | join kind=leftouter (
     // user UserId details
     SecurityIoTRawEvent
     | where
        DeviceId == device and AssociatedResourceId contains tolower(hub)
        and RawEventName == "LocalUsers"
     | project
        UserId=extractjson("$.UserId", EventDetails, typeof(string)),
        UserName=extractjson("$.UserName", EventDetails, typeof(string))
     | distinct UserId, UserName
  ) on UserId
  | extend UserIdName = strcat("Id:", UserId, ", Name:", UserName)
  | summarize CntExecutions=count(), MinObservedTime=min(TimestampLocal), MaxObservedTime=max(TimestampLocal), ExecutingUsers=makeset(UserIdName), ExecutionCommandLines=makeset(CommandLine) by Executable
  ~~~

    Use the query results to discover:

  1. 已有任何可疑的處理序，在裝置上執行？
  2. 處理程序是由適當的使用者執行？
  3. 沒有任何命令列執行包含的正確和預期引數？

## <a name="next-steps"></a>後續步驟
調查裝置，並獲得進一步了解您的風險之後, 您可能要考慮[設定自訂警示](quickstart-create-custom-alerts.md)來改善您的 IoT 解決方案安全性狀態。 如果您還沒有裝置代理程式，請考慮[部署安全性代理程式](how-to-deploy-agent.md)或是[變更現有裝置代理程式的組態](how-to-agent-configuration.md)來改善您的結果。 