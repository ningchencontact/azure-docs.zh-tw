---
title: 了解 Azure IoT 中樞的雲端到裝置傳訊 | Microsoft Docs
description: 此開發人員指南會討論如何使用 IoT 中樞雲端到裝置傳訊。 它包含訊息生命週期和組態選項的相關資訊。
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/15/2018
ms.openlocfilehash: 0fc1b65a4ba1c8a3d76b48206d6a4703035e05bc
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67055309"
---
# <a name="send-cloud-to-device-messages-from-an-iot-hub"></a>從 IoT 中樞傳送雲端到裝置訊息

若要傳送單向通知到裝置應用程式，從解決方案後端，從 IoT 中樞傳送雲端到裝置訊息到您的裝置。 如需其他支援的 Azure IoT 中樞的雲端到裝置選項的討論，請參閱 <<c0> [ 雲端到裝置通訊指引](iot-hub-devguide-c2d-guidance.md)。

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

您將雲端到裝置訊息透過面向服務的端點，傳送 */訊息/devicebound*。 裝置再收到透過裝置專用的端點，訊息 */devices/{deviceid}/files/訊息/devicebound*。

若要針對每個雲端到裝置訊息，以單一裝置，設定您的 IoT 中樞**要**屬性設 */devices/{deviceid}/files/訊息/devicebound*。

每個裝置佇列最多會保留 50 則雲端到裝置訊息。 嘗試將訊息傳送至相同裝置會導致錯誤。

## <a name="the-cloud-to-device-message-life-cycle"></a>雲端到裝置訊息生命週期

若要保證在至少為一次訊息傳遞，IoT 中樞會保存每個裝置佇列中的雲端到裝置訊息。 若要從佇列移除訊息為 IoT 中樞，裝置必須明確地認可*完成*。 此方法保證連線失敗和裝置故障能夠恢復。

生命週期狀態圖會顯示在下圖：

![雲端到裝置訊息生命週期](./media/iot-hub-devguide-messages-c2d/lifecycle.png)

當 IoT 中樞服務傳送訊息至裝置時，服務會將訊息狀態設定為*已加入佇列的*。 當裝置想要*接收*訊息，IoT 中樞*鎖定*藉由將狀態設定為訊息*看不見*。 此狀態會讓其他執行緒開始接收其他訊息在裝置上。 當裝置執行緒完成處理訊息時，它會通知 IoT 中樞*完成*訊息。 IoT 中樞接著會將狀態設定為*已完成*。

裝置也可以︰

* *拒絕*訊息，這會導致 IoT 中樞，將它設定為*寄*狀態。 透過訊息佇列遙測傳輸 (MQTT) 通訊協定連線的裝置無法拒絕雲端到裝置訊息。

* *放棄*訊息，這會導致將訊息放回佇列中，以將狀態設定為 IoT 中樞*已加入佇列的*。 透過 MQTT 通訊協定連線的裝置無法放棄雲端到裝置訊息。

執行緒可能無法處理訊息，且不會通知 IoT 中樞。 在此情況下，訊息會自動從轉換*不可見*回*加入佇列*狀態之後*可視性*逾時 (或*鎖定*逾時)。 此逾時的預設值是一分鐘。

**最大傳遞計數**在 IoT 中樞上的屬性會決定訊息可以轉換之間最大次數*加入佇列*並*看不見*狀態。 IoT 中樞將訊息的狀態設定該轉換次數之後,*寄*。 同樣地，IoT 中樞設定訊息的狀態*寄*的到期時間之後。 如需詳細資訊，請參閱 <<c0> [ 存留時間](#message-expiration-time-to-live)。

[如何使用 IoT 中樞的雲端到裝置訊息傳送](iot-hub-csharp-csharp-c2d.md)文章會說明如何從雲端傳送雲端到裝置訊息，並在裝置上接收它們。

裝置時遺失訊息不會影響應用程式邏輯，正常情況下完成雲端到裝置訊息。 這個範例可能是已保存在本機的訊息內容或已成功執行作業時。 訊息可能也會攜帶暫時性資訊，遺失不會影響應用程式的功能。 對於長時間執行的工作，您有時可以：

* 裝置已保存在本機儲存體中的工作描述之後，請完成雲端到裝置訊息。

* 在作業進度的各個階段，利用一或多個裝置到雲端訊息來通知解決方案後端。

## <a name="message-expiration-time-to-live"></a>訊息到期 (存留時間)

每個雲端到裝置的訊息都有到期時間。 這次是由下列其中一項設定：

* **ExpiryTimeUtc**服務中的屬性
* IoT 中樞，使用預設*存留時間*指定為 IoT 中樞屬性

請參閱 [雲端到裝置的設定選項](#cloud-to-device-configuration-options)。

利用訊息到期，並避免將訊息傳送至已中斷連線的裝置的常見方式是設定簡短*存留時間*值。 這個做法可達到相同的結果與維護裝置連線狀態，但它會更有效率。 當您要求訊息通知時，IoT 中樞會通知您的裝置：

* 能夠接收訊息。
* 未上線或已失敗。

## <a name="message-feedback"></a>訊息意見反應

當您傳送雲端到裝置訊息時，服務可以要求傳遞該訊息的最終狀態的相關的每個訊息意見反應。 您可以設定**iothub ack**正在傳送給下列四個值之一的雲端到裝置訊息中的應用程式屬性：

| Ack 屬性值 | 行為 |
| ------------ | -------- |
| None     | IoT 中樞不會產生意見反應訊息 （預設行為）。 |
| 正面 | 如果雲端到裝置訊息達到*已完成*狀態，IoT 中樞會產生意見反應訊息。 |
| 負數 | 如果雲端到裝置訊息達到*寄*狀態，IoT 中樞會產生意見反應訊息。 |
| 完整     | IoT 中樞在任一情況下產生意見反應訊息。 |

如果**Ack**值是*完整*，並不會收到意見反應訊息，就表示意見反應訊息已過期。 服務無法得知原始訊息發生了什麼事。 實際上，服務應該確保它可以在回饋到期之前對其進行處理。 最大的到期時間兩天，充裕的時間可以讓服務再次執行發生錯誤。

中所述[端點](iot-hub-devguide-endpoints.md)，IoT 中樞傳遞透過面向服務的端點的意見反應 */messages/servicebound/feedback*，做為訊息。 接收意見反應的語意與雲端到裝置訊息的接收語意相同。 可能的話，訊息意見反應會放入單一訊息中，其格式如下：

| 屬性     | 描述 |
| ------------ | ----------- |
| EnqueuedTime | 時間戳記，指出中樞收到意見反應訊息時 |
| UserId       | `{iot hub name}` |
| ContentType  | `application/vnd.microsoft.iothub.feedback.json` |

主體是記錄的 JSON 序列化陣列，而每筆記錄都具有下列屬性︰

| 屬性           | 描述 |
| ------------------ | ----------- |
| EnqueuedTimeUtc    | 表示訊息的結果發生的時間戳記 （例如，中樞收到意見反應訊息或原始訊息已過期） |
| OriginalMessageId  | *MessageId*與此意見反應資訊相關之雲端到裝置訊息 |
| StatusCode         | 必要的字串，在 IoT 中樞所產生的意見反應訊息中使用： <br/> 「成功」  <br/> *Expired* <br/> *DeliveryCountExceeded* <br/> *已拒絕* <br/> *清除* |
| 描述        | 字串值*StatusCode* |
| deviceId           | *DeviceId*的意見反應資訊相關之雲端到裝置訊息的目標裝置 |
| DeviceGenerationId | *DeviceGenerationId*的意見反應資訊相關之雲端到裝置訊息的目標裝置 |

要使其意見反應與原始訊息相互關聯的雲端到裝置訊息，服務必須指定*MessageId*。

意見反應訊息的主體是由下列程式碼所示：

```json
[
  {
    "OriginalMessageId": "0987654321",
    "EnqueuedTimeUtc": "2015-07-28T16:24:48.789Z",
    "StatusCode": 0,
    "Description": "Success",
    "DeviceId": "123",
    "DeviceGenerationId": "abcdefghijklmnopqrstuvwxyz"
  },
  {
    ...
  },
  ...
]
```

## <a name="cloud-to-device-configuration-options"></a>雲端到裝置組態選項

每個 IoT 中樞都會針對雲端到裝置傳訊公開下列設定選項：

| 屬性                  | 描述 | 範圍和預設值 |
| ------------------------- | ----------- | ----------------- |
| defaultTtlAsIso8601       | 雲端到裝置訊息的預設 TTL | ISO_8601 間隔最多 2 天的時間 （最小為 1 分鐘）;預設值：1 小時 |
| maxDeliveryCount          | 雲端到裝置每一裝置佇列的最大傳遞計數 | 1 到 100。預設值：10 |
| feedback.ttlAsIso8601     | 服務繫結的意見反應訊息保留期 | ISO_8601 間隔最多 2 天的時間 （最小為 1 分鐘）;預設值：1 小時 |
| feedback.maxDeliveryCount | 意見反應佇列的最大傳遞計數 | 1 到 100。預設值：100 |

如需如何設定這些設定選項的詳細資訊，請參閱[建立 IoT 中樞](iot-hub-create-through-portal.md)。

## <a name="next-steps"></a>後續步驟

如需您可以使用來接收雲端到裝置訊息之 Sdk 的資訊，請參閱 < [Azure IoT Sdk](iot-hub-devguide-sdks.md)。

若要嘗試接收雲端到裝置訊息，請參閱[傳送雲端到裝置](iot-hub-csharp-csharp-c2d.md)教學課程。
