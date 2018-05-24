---
title: 監視 Azure 事件格線訊息傳遞
description: 說明如何監視 Azure 事件格線訊息的傳遞。
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/30/2018
ms.author: tomfitz
ms.openlocfilehash: f9719bb1f1563c55537c7ef32278411a2034bd75
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/18/2018
---
# <a name="monitor-event-grid-message-delivery"></a>監視事件格線訊息傳遞 

本文說明如何使用入口網站來查看事件傳遞的狀態。

Event Grid 提供持久的傳遞。 它針對每個訂用帳戶傳遞每則訊息至少一次。 事件會立即傳送到每個訂用帳戶的已註冊 Webhook。 如果 Webhook 在第一次嘗試傳遞的 60 秒內未確認接收事件，Event Grid 會重試傳遞事件。

如需事件傳遞和重試的相關資訊，請參閱[事件格線訊息傳遞與重試](delivery-and-retry.md)。

## <a name="delivery-metrics"></a>傳遞計量

入口網站會顯示用於傳遞事件訊息之狀態的計量。

對於主題，計量為：

* **發行成功**：已將事件成功傳送至主題，並以 2xx 回應來處理。
* **發行失敗**：已將事件傳送至主題，但遭到拒絕且有錯誤碼。
* **不相符**：已將事件成功發佈至主題，但與事件訂閱不符。 已捨棄事件。

對於訂閱，計量為：

* **傳遞成功**：已將事件成功傳遞至訂閱的端點，並已收到 2xx 回應。
* **傳遞失敗**：已將事件傳送至訂閱的端點，但收到了 4xx 或 5xx 回應。
* **已過期事件**：事件並未傳遞且已傳送所有的重試嘗試。 已捨棄事件。
* **已比對的事件**：主題中的事件已由事件訂閱加以比對。

## <a name="event-subscription-status"></a>事件訂閱狀態

若要查看事件訂閱的計量，請在可用服務中搜尋**事件格線訂閱**並加以選取。

![搜尋事件訂閱](./media/monitor-event-delivery/select-event-subscriptions.png)

依事件的類型、訂閱及位置進行篩選。 針對要檢視的訂閱選取 [計量]。

![篩選事件訂閱](./media/monitor-event-delivery/filter-events.png)

檢視事件主題和訂閱的計量。

![檢視事件計量](./media/monitor-event-delivery/subscription-metrics.png)

## <a name="custom-event-status"></a>自訂事件狀態

如果您已發行自訂主題，就可以檢視它的計量。 選取包含該主題的資源群組，並選取該主題。

![選取自訂主題](./media/monitor-event-delivery/select-custom-topic.png)

檢視自訂事件主題的計量。

![檢視事件計量](./media/monitor-event-delivery/custom-topic-metrics.png)

## <a name="next-steps"></a>後續步驟

* 如需事件傳遞和重試的相關資訊，請參閱[事件格線訊息傳遞與重試](delivery-and-retry.md)。
* 如需 Event Grid 的簡介，請參閱[關於 Event Grid](overview.md)。
* 若要快速地開始使用 Event Grid，請參閱[使用 Azure Event Grid 建立和路由傳送自訂事件](custom-event-quickstart.md)。
