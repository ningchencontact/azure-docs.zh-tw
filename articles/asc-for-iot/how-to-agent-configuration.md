---
title: 設定 IoT 代理程式預覽的 Azure 資訊安全中心 |Microsoft Docs
description: 了解如何設定 IoT 與 Azure 資訊安全中心使用的代理程式。
services: asc-for-iot
ms.service: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: f95c445a-4f0d-4198-9c6c-d01446473bd0
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/26/2019
ms.author: mlottner
ms.openlocfilehash: 1e212e752309fd8347836d462a3394da2fca4a15
ms.sourcegitcommit: d83fa82d6fec451c0cb957a76cfba8d072b72f4f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/02/2019
ms.locfileid: "58862772"
---
# <a name="tutorial-configure-security-agents"></a>教學課程：設定安全性代理程式

> [!IMPORTANT]
> IoT 的 azure 資訊安全中心目前處於公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

這篇文章說明 Azure 資訊安全中心 (ASC) IoT 安全性代理程式，若要變更這些設定 ASC IoT 安全性代理程式的方式。

> [!div class="checklist"]
> * 設定安全性代理程式
> * 藉由編輯對應項屬性變更代理程式行為
> * 探索預設組態

## <a name="agents"></a>代理程式

IoT 安全性代理程式的 ASC 從 IoT 裝置收集資料，並執行安全性動作，來減緩偵測到的弱點。 使用一組您可以自訂模組對應項屬性可控制安全性代理程式設定。 一般情況下，這些屬性的次要更新並不常見。  

ASC IoT 的安全性代理程式的對應項的組態物件為.json 格式物件。 組態物件是一組可控制來控制代理程式的行為，您可以定義的屬性。 

這些設定可協助您自訂每個案例所需的代理程式。 例如，自動排除部分事件，或是讓保持在最低層級的功率耗用量可能會藉由設定這些屬性。  

IoT 安全性代理程式設定為使用 ASC[結構描述](https://aka.ms/iot-security-github-module-schema)進行變更。  

## <a name="configuration-objects"></a>組態物件 

IoT 安全性代理程式的每個 ASC 與相關屬性位於內所需的屬性 區段中，代理程式設定物件的**azureiotsecurity**模組。 

若要修改的組態，建立和修改此物件內**azureiotsecurity**模組對應項身分識別。 

如果代理程式組態物件不存在於**azureiotsecurity**模組對應項，所有的安全性代理程式屬性值都會設為預設值。 

```json
"desired": {
  "azureiot*com^securityAgentConfiguration^1*0*0": {
  } 
}
```

請務必驗證您的代理程式組態變更，對這[結構描述](https://aka.ms/iot-security-github-module-schema)。
如果組態物件不符合結構描述，將不會啟動代理程式。

## <a name="configuration-schema-and-validation"></a>組態結構描述和驗證 

請務必驗證您的代理程式設定，針對此[結構描述](https://aka.ms/iot-security-github-module-schema)。 如果組態物件不符合結構描述，將不會啟動代理程式。

 
如果代理程式執行時，會將組態物件變更為非有效的組態 （組態不符合結構描述），代理程式將會忽略無效的組態，並且將繼續使用目前的組態。 

## <a name="editing-a-property"></a>編輯屬性 

所有的自訂屬性必須設定內的代理程式設定物件內**azureiotsecurity**模組對應項。
若要使用預設屬性值，請從組態物件中移除屬性。

### <a name="setting-a-property"></a>設定屬性

1. 在您的 IoT 中樞，找出並選取您想要變更的裝置。

1. 按一下您的裝置，然後按一下**azureiotsecurity**模組。

1. 按一下 **模組身分識別對應項**。

1. 編輯安全性模組的所需的屬性。
   
   例如，如果設定為高優先順序的連接事件，並且收集高優先順序事件每 7 分鐘，請使用下列設定。
   
   ```json
    "desired": {
      "azureiot*com^securityAgentConfiguration^1*0*0": {
        "highPriorityMessageFrequency": "PT7M",    
        "eventPriorityConnectionCreate": "High" 
      } 
    }, 
    ```

1. 按一下 [檔案] 。

### <a name="using-a-default-value"></a>使用預設值

若要使用預設屬性值，請從組態物件中移除屬性。

## <a name="default-properties"></a>預設屬性 

下表包含 IoT 安全性代理程式的 ASC 可控制的屬性。

在適當的結構描述中的預設值可[Github](https://aka.ms/iot-security-module-default)。

| 名稱| 狀態 | 有效值| 預設值| 描述 |
|----------|------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------|---------------|
|highPriorityMessageFrequency|必要： false |有效值：格式為 ISO 8601 持續時間 |預設值：PT7M |傳送高優先順序訊息之前的最大時間。|
|lowPriorityMessageFrequency |必要： false|有效值：格式為 ISO 8601 持續時間 |預設值：PT5H |傳送低優先順序訊息之前的最大時間。| 
|snapshotFrequency |需要： false|有效的值： 持續時間以 ISO 8601 格式 |預設值 PT13H |建立裝置狀態的快照集的時間間隔。| 
|maxLocalCacheSizeInBytes |必要： false |有效值： |預設值：2560000，大於 8192 | 儲存體上限 （以位元組為單位） 所允許的代理程式的訊息快取。 最大允許在裝置上儲存訊息，傳送訊息之前的空間量。| 
|maxMessageSizeInBytes |必要： false |有效值：正數，大於 8192，小於 262144 |預設值：204800 |最大允許的代理程式到雲端訊息的大小。 此設定會控制每個訊息中傳送的最大資料量。 |
|eventPriority${EventName} |必要： false |有效值：最高、 最低、 關閉 |預設值： |每個代理程式的優先順序會產生事件 | 

### <a name="supported-security-events"></a>支援的安全性事件

|事件名稱| PropertyName | 預設值| 快照集事件| 詳細資料狀態  |
|----------|------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------|---------------|
|[診斷] 事件|eventPriorityDiagnostic| 關閉| False| 代理程式相關的診斷事件。 使用這個事件的詳細資訊記錄。| 
|組態錯誤 |eventPriorityConfigurationError |低 |False |代理程式無法剖析設定。 請確認針對結構描述的組態。| 
|已卸除的事件統計資料 |eventPriorityDroppedEventsStatistics |低 |True|代理程式相關的事件統計資料。 |
|訊息統計資料|eventPriorityMessageStatistics |低 |True |代理程式相關的訊息統計資料。 |
|連接的硬體|eventPriorityConnectedHardware |低 |True |快照集的所有硬體連線到裝置。|
|接聽連接埠|eventPriorityListeningPorts |高 |True |所有開啟的接聽連接埠，在裝置上的快照集。|
|處理程序建立 |eventPriorityProcessCreate |低 |False |稽核處理序在裝置上的建立。|
|處理序終止|eventPriorityProcessTerminate |低 |False |稽核處理序在裝置上的終止。| 
|系統資訊 |eventPrioritySystemInformation |低 |True |系統資訊的快照集 (例如：作業系統或 CPU）。| 
|本機使用者| eventPriorityLocalUsers |高 |True|已註冊的本機使用者，在系統中的快照集。 |
|登入|  eventPriorityLogin |高|False|稽核登入事件的裝置 （本機和遠端登入）。|
|建立連線 |eventPriorityConnectionCreate|低|False|稽核與裝置建立 TCP 連線。 |
|防火牆設定| eventPriorityFirewallConfiguration|低|True|裝置的防火牆組態 （防火牆規則） 的快照集。 |
|OS 基準| eventPriorityOSBaseline| 低|True|檢查的裝置 OS 基準快照集。|
 

## <a name="next-steps"></a>後續步驟

- [了解 IoT 建議 ASC](concept-recommendations.md)
- [探索 IoT 警示 ASC](concept-security-alerts.md)
- [存取未經處理的安全性資料](how-to-security-data-access.md)
