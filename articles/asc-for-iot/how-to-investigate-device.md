---
title: IoT 裝置調查指南預覽的 azure 資訊安全中心 |Microsoft Docs
description: 此作法指南說明如何使用 IoT 的 Azure 資訊安全中心調查可疑的 IoT 裝置，使用 Log Analytics。
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: b18b48ae-b445-48f8-9ac0-365d6e065b64
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/18/2019
ms.author: mlottner
ms.openlocfilehash: 884d001a65962d5e7e6e52dd47ce6ad7e02e1057
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/07/2019
ms.locfileid: "67618128"
---
# <a name="investigate-a-suspicious-iot-device"></a>調查可疑的 IoT 裝置

> [!IMPORTANT]
> 適用於 IoT 的 Azure 資訊安全中心目前為公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

可疑的可疑活動，或指示存在裝置遭到入侵的參與的 IoT 裝置時，azure 資訊安全中心 (ASC) IoT 服務警示和辨識項會提供清楚的指示。 

在本指南中，使用調查所提供的建議來協助判斷您組織的潛在風險，決定如何修復，並了解預防類似攻擊的最佳方法。  

> [!div class="checklist"]
> * 尋找您的裝置資料
> * 使用 kql 查詢進行調查


## <a name="how-can-i-access-my-data"></a>如何存取我的資料？

根據預設，適用於 IoT 的 ASC 會在 Log Analytics 工作區中儲存您的安全性警示和建議。 您也可以選擇儲存未經處理的安全性資料。

若要找出用來儲存資料的 Log Analytics 工作區：

1. 開啟 IoT 中樞， 
1. 底下**安全性**，按一下**概觀**，然後選取**設定**。
1. 變更 Log Analytics 工作區的組態詳細資料。 
1. 按一下 [儲存]  。 

下列組態，執行下列步驟來存取儲存在 Log Analytics 工作區中的資料：

1. 在 IoT 中樞內選取並按一下適用於 IoT 的 ASC 警示。 
1. 按一下 [進一步調查]  。 
1. 選取 [若要查看哪些裝置有此警示，請按一下這裡並檢視 DeviceId 資料行]  。

## <a name="investigation-steps-for-suspicious-iot-devices"></a>可疑 IoT 裝置的調查步驟

若要存取深入解析和關於您 IoT 裝置的未經處理資料，請移至您的 Log Analytics 工作區[存取您的資料](#how-can-i-access-my-data)。

檢查並調查下列詳細資料，並使用下列 kql 查詢活動的裝置資料。

### <a name="related-alerts"></a>相關的警示

若要了解同一時間前後是否觸發了其他警示，請使用下列 kql 查詢：

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
請使用這項資料來了解︰ 
  1. 哪些使用者可以存取此裝置？
  2. 具有存取權的使用者有權限等級，如預期般運作嗎？ 

### <a name="open-ports"></a>開啟連接埠

若要查明哪些裝置中的連接埠目前正在使用中或已使用，請使用下列 kql 查詢： 

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
  1. 裝置上目前作用中的接聽通訊端有哪些？
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
  1. 有哪些使用者登入過此裝置？
  2. 登入的使用者應該能夠登入？
  3. 登入過的使用者是從預期還是非預期的 IP 位址進行連線的？
  
### <a name="process-list"></a>處理程序清單

若要找出處理序清單是否如預期般，使用下列 kql 查詢： 

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

  1. 裝置上是否執行了任何可疑的處理序？
  2. 這些處理序是不是由適當的使用者來執行？
  3. 是否有任何命令列執行包含正確且預期中的引數？

## <a name="next-steps"></a>後續步驟

在調查裝置並更加了解您的風險之後，請考慮[設定自訂警示](quickstart-create-custom-alerts.md)來改善您的 IoT 解決方案安全性態勢。 如果您還沒有裝置代理程式，請考慮[部署安全性代理程式](how-to-deploy-agent.md)或[變更現有裝置代理程式的組態](how-to-agent-configuration.md)，以改善您的結果。 