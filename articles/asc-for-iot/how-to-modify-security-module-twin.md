---
title: 說明如何修改 IoT 模組對應項中 ASC ASC IoT 預覽如何 |Microsoft Docs
description: 了解如何修改 IoT ASC ASC 的 IoT 安全性模組對應項。
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: 1bc5dc86-0f33-4625-b3d3-f9b6c1a54e14
ms.service: ascforiot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/25/2019
ms.author: mlottner
ms.openlocfilehash: 4bb25db7feddea66e75d9dd069aaec7785faf738
ms.sourcegitcommit: cf971fe82e9ee70db9209bb196ddf36614d39d10
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2019
ms.locfileid: "58541943"
---
# <a name="modify-an-asc-for-iot-module-twin"></a>修改 ASC IoT 模組對應項

> [!IMPORTANT]
> Iot 的 ASC 目前處於公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

這篇文章說明如何修改現有的組態**AzureIoTSecurity 模組對應項**現有裝置。 

請參閱[建立的 IoT 模組 ASC](quickstart-create-security-twin.md)若要了解如何讓新裝置的新安全性模組。  

## <a name="modification-considerations"></a>修改考量

> [!IMPORTANT]
> 每個組態中的對應項設定會覆寫預設組態。 如果特定的組態不再出現在對應項組態，則會使用預設組態。 

## <a name="configuration-schema-and-validation"></a>組態結構描述和驗證 

請務必驗證您的代理程式設定，針對此[結構描述](https://github.com/Azure/asc-for-iot/schema/security_module_twin)。 如果組態物件不符合結構描述，將不會啟動代理程式。

 
如果代理程式執行時，會將組態物件變更為非有效的組態 （組態不符合結構描述），代理程式將會忽略無效的組態，並且將繼續使用目前的組態。 

## <a name="edit-a-property"></a>編輯屬性  

設定代理程式設定物件內 AzureIoTSecurity 模組對應項中的所有自訂屬性。 

若要設定屬性，加入想要的值的組態物件中的屬性索引鍵。 若要使用預設屬性值，請從組態物件中移除屬性：

```json
"desired": { //AzureIoTSecurity Module Identity Twin – desired properties section  
  "azureiot*com^securityAgentConfiguration^1*0*0": { //ASC for IoT Agent 
      // configuration section  
    "hubResourceId": "/subscriptions/82392767-31d3-4bd2-883d-9b60596f5f42/resourceGroups/myResourceGroup/providers/Microsoft.Devices/IotHubs/myIotHub",     
    "lowPriorityMessageFrequency": "PT1H",     
    "highPriorityMessageFrequency": "PT7M",    
    "eventPriorityFirewallConfiguration": "High",     
    "eventPriorityConnectionCreate": "Off" 
  } 
}, 
```

## <a name="properties"></a>properties 
下表包含所有可設定的屬性，以控制 ASC IoT 代理程式。 
          

| 名稱| 狀態 | 有效值| 預設值| 描述 |
|----------|------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------|---------------|
|highPriorityMessageFrequency|必要： false |有效值：格式為 ISO 8601 持續時間 |預設值：PT7M |傳送高優先順序訊息之前的最大時間。|
|lowPriorityMessageFrequency |必要： false|有效值：格式為 ISO 8601 持續時間 |預設值：PT5H |傳送低優先順序訊息之前的最大時間。| 
|snapshotFrequency |需要： false|有效的值： 持續時間以 ISO 8601 格式 |預設值 PT13H |建立裝置狀態的快照集的時間間隔。| 
|maxLocalCacheSizeInBytes |必要： false |有效值： |預設值：2560000，大於 8192 | 儲存體上限 （以位元組為單位） 所允許的代理程式的訊息快取。 最大允許在裝置上儲存訊息，傳送訊息之前的空間量。| 
|maxMessageSizeInBytes |必要： false |有效值：正數，大於 8192，小於 262144 |預設值：204800 |最大允許的代理程式到雲端訊息的大小。 此設定會控制每個訊息中傳送的最大資料量。 |
|eventPriority${EventName} |必要： false |有效值：最高、 最低、 關閉 |預設值： |每個代理程式的優先順序會產生事件。 | 
|

### <a name="events"></a>活動

下列事件清單會是所有事件 IoT 代理程式 ASC 可以收集從您的裝置。 您可以使用 AzureIotSecurity 模組對應項來設定其中一個這些事件會收集，並決定其在您的方案中的優先順序。 
 
|事件名稱| PropertyName | 預設值| 快照集事件| 詳細資料狀態  |
|----------|------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------|---------------|
|[診斷] 事件|eventPriorityDiagnostic| 關閉| False| 代理程式相關的診斷事件。 使用這個事件的詳細資訊記錄。| 
組態錯誤 |eventPriorityConfigurationError |低 |False |代理程式無法剖析設定。 請確認針對結構描述的組態。| 
|已卸除的事件統計資料 |eventPriorityDroppedEventsStatistics |低 |True|代理程式相關的事件統計資料。 |
|訊息統計資料|eventPriorityMessageStatistics |低 |True |代理程式相關的訊息統計資料。 |
|連接的硬體|eventPriorityConnectedHardware |低 |True |快照集的所有硬體連線到裝置。|
|接聽連接埠|eventPriorityListeningPorts |高 |True |所有開啟的接聽連接埠，在裝置上的快照集。|
| 處理程序建立 |eventPriorityProcessCreate |低 |False |稽核處理序在裝置上的建立。|
| 處理序終止|eventPriorityProcessTerminate |低 |False |稽核處理序在裝置上的終止。| 
 系統資訊 |eventPrioritySystemInformation |低 |True |例如，作業系統或 CPU 的系統資訊的快照集。|
|本機使用者| eventPriorityLocalUsers |高 |True|已註冊的本機使用者，在系統中的快照集。 |
|登入|  eventPriorityLogin |高|False|稽核登入事件，到裝置 （本機和遠端登入）。|
|建立連線 |eventPriorityConnectionCreate|低|False|稽核與裝置建立 TCP 連線。 |
|防火牆設定| eventPriorityFirewallConfiguration|低|True|裝置的防火牆組態 （防火牆規則） 的快照集。 |
|OS 基準| eventPriorityOSBaseline| 低|True|檢查的裝置 OS 基準快照集。| 
|


## <a name="next-steps"></a>後續步驟

- 閱讀 IoT ASC[概觀](overview.md)
- 了解 ASC IoT[架構](architecture.md)
- 了解和探索[ASC IoT 警示](concept-security-alerts.md)
- 了解如何存取您[安全性資料](how-to-security-data-access.md)
