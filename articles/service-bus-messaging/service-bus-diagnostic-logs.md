---
title: Azure 服務匯流排診斷記錄 | Microsoft Docs
description: 了解如何為 Azure 中的「服務匯流排」設定診斷記錄。
keywords: ''
documentationcenter: .net
services: service-bus-messaging
author: axisc
manager: timlt
editor: spelluru
ms.assetid: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 01/23/2019
ms.author: aschhab
ms.openlocfilehash: 41e0bdc1f04c9491ebe939f46b59ae4eb2bc7ab6
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/18/2019
ms.locfileid: "72592425"
---
# <a name="enable-diagnostic-logs-for-service-bus"></a>啟用服務匯流排的診斷記錄

當您開始使用 Azure 服務匯流排命名空間時，您可能會想要監視建立、刪除或存取命名空間的方式和時間。 本文提供所有可用的作業/診斷記錄的總覽。

Azure 服務匯流排目前支援活動/作業記錄，可捕捉在 Azure 服務匯流排命名空間上執行的**管理作業**。 具體而言，這些記錄會擷取作業類型，包括建立佇列、使用的資源，以及作業狀態。

## <a name="operational-logs-schema"></a>作業記錄結構描述

所有記錄都會以 JavaScript 物件標記法（JSON）格式儲存在下列2個位置。

- **AzureActivity** -顯示在入口網站上或透過 Azure Resource Manager 範本部署，針對您的命名空間執行的作業/動作記錄。
- **AzureDiagnostics** -使用 API，或透過語言 SDK 上的管理用戶端，顯示針對您的命名空間進行的作業/動作的記錄。

作業記錄 JSON 字串包括下表所列的元素：

| Name | 描述 |
| ------- | ------- |
| ActivityId | 內部識別碼，用來識別指定的活動 |
| EventName | 作業名稱 |
| ResourceId | Azure Resource Manager 資源識別碼 |
| SubscriptionId | 訂用帳戶 ID |
| EventTimeString | 作業時間 |
| EventProperties | 作業屬性 |
| 狀態 | 作業狀態 |
| 呼叫者 | 作業呼叫者 (Azure 入口網站或管理用戶端) |
| 類別 | OperationalLogs |

以下是作業記錄 JSON 字串的範例：

```json
{
  "ActivityId": "6aa994ac-b56e-4292-8448-0767a5657cc7",
  "EventName": "Create Queue",
  "resourceId": "/SUBSCRIPTIONS/1A2109E3-9DA0-455B-B937-E35E36C1163C/RESOURCEGROUPS/DEFAULT-SERVICEBUS-CENTRALUS/PROVIDERS/MICROSOFT.SERVICEBUS/NAMESPACES/SHOEBOXEHNS-CY4001",
  "SubscriptionId": "1a2109e3-9da0-455b-b937-e35e36c1163c",
  "EventTimeString": "9/28/2016 8:40:06 PM +00:00",
  "EventProperties": "{\"SubscriptionId\":\"1a2109e3-9da0-455b-b937-e35e36c1163c\",\"Namespace\":\"shoeboxehns-cy4001\",\"Via\":\"https://shoeboxehns-cy4001.servicebus.windows.net/f8096791adb448579ee83d30e006a13e/?api-version=2016-07\",\"TrackingId\":\"5ee74c9e-72b5-4e98-97c4-08a62e56e221_G1\"}",
  "Status": "Succeeded",
  "Caller": "ServiceBus Client",
  "category": "OperationalLogs"
}
```

## <a name="what-eventsoperations-are-captured-in-operational-logs"></a>作業記錄中會捕捉哪些事件/作業？

作業記錄會捕捉在 Azure 服務匯流排命名空間上執行的所有管理作業。 因為 Azure 服務匯流排上執行的資料作業量很大，所以不會捕捉資料作業。

> [!NOTE]
> 若要更好地追蹤資料作業，我們建議使用用戶端追蹤。

下列管理作業會在操作記錄中加以捕獲- 

| Scope | 作業|
|-------| -------- |
| 命名空間 | <ul> <li> 建立命名空間</li> <li> 更新命名空間 </li> <li> 刪除命名空間 </li>  </ul> | 
| 佇列 | <ul> <li> 建立佇列</li> <li> 更新佇列</li> <li> 刪除佇列 </li> </ul> | 
| 話題 | <ul> <li> 建立主題 </li> <li> 更新主題 </li> <li> 刪除主題 </li> </ul> |
| Subscription | <ul> <li> 建立訂用帳戶 </li> <li> 更新訂用帳戶 </li> <li> 刪除訂用帳戶 </li> </ul> |

> [!NOTE]
> 目前，在操作記錄中不會追蹤**讀取**作業。

## <a name="how-to-enable-operational-logs"></a>如何啟用操作記錄？

預設會停用操作記錄。 若要啟用診斷記錄，請執行下列步驟：

1. 在  [Azure 入口網站](https://portal.azure.com)中，流覽至您的 Azure 服務匯流排命名空間，然後在 **監視**] 底下，按一下 [**診斷設定**。

   ![瀏覽到診斷記錄的刀鋒視窗](./media/service-bus-diagnostic-logs/image1.png)

2. 按一下 [**新增診斷設定**] 以設定診斷設定。  

   ![開啟診斷記錄](./media/service-bus-diagnostic-logs/image2.png)

3. 設定診斷設定
   1. 輸入**名稱**以識別診斷設定。
   2. 選擇診斷的目的地。
      - 如果您選擇 [**儲存體帳戶**]，則需要設定將儲存診斷的儲存體帳戶。
      - 如果您選擇**事件中樞**，則需要設定適當的事件中樞，以將診斷設定串流至其中。
      - 如果您挑選**Log analytics**，您必須指定要將診斷傳送到哪個 log analytics 實例。
    3. 檢查**OperationalLogs**。

       ![變更診斷記錄狀態](./media/service-bus-diagnostic-logs/image3.png)

4. 按一下 [儲存]。


新的設定大約會在 10 分鐘內生效。 之後，記錄就會顯示在 [診斷記錄] 刀鋒視窗上已設定的封存目標中。

如需設定診斷的詳細資訊，請參閱 [Azure 診斷記錄概觀](../azure-monitor/platform/diagnostic-logs-overview.md)。

## <a name="next-steps"></a>後續步驟

請參閱下列連結以深入了解服務匯流排：

* [服務匯流排簡介](service-bus-messaging-overview.md)
* [開始使用服務匯流排](service-bus-dotnet-get-started-with-queues.md)
