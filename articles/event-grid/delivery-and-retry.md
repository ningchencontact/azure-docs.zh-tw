---
title: Azure Event Grid 傳遞與重試
description: 說明 Azure Event Grid 如何傳遞事件，以及如何處理未傳遞的訊息。
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/09/2018
ms.author: tomfitz
ms.openlocfilehash: 8eb6717369b48289bd31dcd1972ce275bc550c77
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/18/2018
---
# <a name="event-grid-message-delivery-and-retry"></a>Event Grid 訊息傳遞與重試 

本文章說明未確認傳遞時，Azure Event Grid 如何處理事件。

Event Grid 提供持久的傳遞。 它針對每個訂用帳戶傳遞每則訊息至少一次。 事件會立即傳送到每個訂用帳戶的已註冊 Webhook。 如果 Webhook 在第一次嘗試傳遞的 60 秒內未確認接收事件，Event Grid 會重試傳遞事件。 

目前，事件方格會將每個事件個別傳送給訂閱者。 訂閱者會收到包含單一事件的陣列。

## <a name="message-delivery-status"></a>訊息傳遞狀態

Event Grid 使用 HTTP 回應碼以確認接收事件。 

### <a name="success-codes"></a>成功碼

下列 HTTP 回應碼表示事件已成功傳遞到您的 Webhook。 Event Grid 會將傳遞視為完成。

- 200 確定
- 202 已接受

### <a name="failure-codes"></a>失敗碼

下列 HTTP 回應碼表示事件傳遞嘗試失敗。 

- 400 不正確的要求
- 401 未經授權
- 404 找不到
- 408 要求逾時
- 414 URI 太長
- 500 內部伺服器錯誤
- 503 服務無法使用
- 504 閘道逾時

如果事件格線收到錯誤，指出端點目前無法使用，則它會嘗試重新傳送事件。 

## <a name="retry-intervals-and-duration"></a>重試間隔和持續時間

Event Grid 針對事件傳遞使用指數輪詢重試原則。 如果您的 Webhook 沒有回應，或是傳回失敗碼，Event Grid 會於下列排程重試傳遞：

1. 10 秒
2. 30 秒
3. 1 分鐘
4. 5 分鐘
5. 10 分鐘
6. 30 分鐘
7. 1 小時

Event Grid 會將小型隨機加入所有重試間隔。 一個小時之後，事件傳遞會每小時重試一次。

根據預設，事件格線會讓所有未在 24 小時內傳遞的事件變成過期事件。

## <a name="next-steps"></a>後續步驟

* 若要檢視事件傳遞的狀態，請參閱[監視 Event Grid 訊息傳遞](monitor-event-delivery.md)。
* 如需 Event Grid 的簡介，請參閱[關於 Event Grid](overview.md)。
* 若要快速地開始使用 Event Grid，請參閱[使用 Azure Event Grid 建立和路由傳送自訂事件](custom-event-quickstart.md)。