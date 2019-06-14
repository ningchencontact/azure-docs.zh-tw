---
title: 了解 Azure IoT 中樞訊息格式 | Microsoft Docs
description: 開發人員指南-說明的格式和預期的 IoT 中樞訊息的內容。
author: ash2017
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/13/2018
ms.author: asrastog
ms.openlocfilehash: e80fb136220330ddc53d513b22ebcfa19a35117b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66252804"
---
# <a name="create-and-read-iot-hub-messages"></a>建立及讀取 IoT 中樞訊息

為了支援完美的跨通訊協定互通性，IoT 中樞定義了適用於所有裝置面向通訊協定的通用訊息格式。 此訊息格式可用於[裝置到雲端的路由](iot-hub-devguide-messages-d2c.md)和[雲端到裝置](iot-hub-devguide-messages-c2d.md)的訊息。 

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

IoT 中樞使用串流訊息模式實作裝置到雲端傳訊。 IoT 中樞的裝置到雲端訊息與[事件中樞](/azure/event-hubs/)*事件*較為相似 (而不像[服務匯流排](/azure/service-bus-messaging/)*訊息*) 因為會有大量的事件傳遞給該服務，並可由多個讀取器讀取。

IoT 中樞訊息包含：

* 預先決定的*系統屬性*集如下所示。

* 一組 *應用程式屬性*。 應用程式可以定義的字串屬性字典，而且不需將訊息本文還原序列化即可加以存取。 IoT 中樞不會修改這些屬性。

* 不透明的二進位主體。

上述屬性名稱和值只能包含 ASCII 英數字元，而且 ``{'!', '#', '$', '%, '&', ''', '*', '+', '-', '.', '^', '_', '`', '|', '~'}`` 在您使用 HTTPS 通訊協定傳送裝置到雲端或雲端到裝置的訊息時也是如此。

IoT 中樞的裝置對雲端傳訊具有下列特性：

* 裝置到雲端訊息會長期保留在 IoT 中樞的預設 **messages/events** 端點，最多達七天。

* 裝置到雲端訊息最大可能為 256 KB，而且可分成數個批次以最佳化傳送。 批次最多可達 256 KB。

* IoT 中樞不允許任意進行資料分割。 裝置到雲端訊息會根據其原始的 **deviceId**進行分割。

* 如[控制 IoT 中樞的存取權](iot-hub-devguide-security.md)所述，IoT 中樞會啟用每一裝置的驗證和存取控制。

* 您可以戳記訊息會進入應用程式屬性的資訊。 如需詳細資訊，請參閱[訊息類](iot-hub-message-enrichments-overview.md)。

了解如何使用不同通訊協定傳送的訊息編碼和解碼，請參閱 [Azure IoT SDK](iot-hub-devguide-sdks.md)。

下表列出 IoT 中樞訊息中的系統屬性集合。

| 屬性 | 描述 | 是否可設定使用者？ |
| --- | --- | --- |
| message-id |使用者可設定的訊息識別碼，用於「要求-回覆」模式。 格式：區分大小寫的字串，最長為 128 個字元，可使用 ASCII 7 位元英數字元和 `{'-', ':', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '$', '''}`。 | 是 |
| sequence-number |IoT 中樞指派給每則雲端到裝置訊息的數字 (對每個裝置佇列而言都是唯一的)。 | [否] 表示 C2D 訊息；[是] 表示其他。 |
| to |[雲端到裝置](iot-hub-devguide-c2d-guidance.md) 訊息中指定的目的地。 | [否] 表示 C2D 訊息；[是] 表示其他。 |
| absolute-expiry-time |訊息到期的日期和時間。 | 是 |
| iothub-enqueuedtime |IoT 中樞收到[雲端到裝置](iot-hub-devguide-c2d-guidance.md)訊息的日期和時間。 | [否] 表示 C2D 訊息；[是] 表示其他。 |
| correlation-id |回應訊息中的字串屬性，通常包含採用「要求-回覆」模式之要求的 MessageId。 | 是 |
| user-id |用來指定訊息來源的識別碼。 當 IoT 中樞產生訊息時，它會設定為 `{iot hub name}`。 | 否 |
| iothub-ack |意見反應訊息產生器。 這個屬性是在雲端到裝置訊息中使用，可要求 IoT 中樞因為裝置取用訊息而產生意見反應訊息。 可能的值︰**none** (預設值)︰不會產生任何意見反應訊息；**positive**︰如果訊息已完成，則會收到意見反應訊息；**negative**︰如果訊息未由裝置完成就到期 (或已達到最大傳遞計數) 則會收到意見反應訊息；或者 **full**︰positive 和 negative。 <!-- robinsh For more information, see [Message feedback][lnk-feedback].--> | 是 |
| iothub-connection-device-id |由 IoT 中樞在裝置到雲端訊息上設定的識別碼。 它包含傳送訊息之裝置的 **deviceId** 。 | [否] 表示 D2C 訊息；[是] 表示其他。 |
| iothub-connection-auth-generation-id |由 IoT 中樞在裝置到雲端訊息上設定的識別碼。 它包含傳送訊息之裝置的 **generationId** (依據 [裝置身分識別屬性](iot-hub-devguide-identity-registry.md#device-identity-properties))。 | [否] 表示 D2C 訊息；[是] 表示其他。 |
| iothub-connection-auth-method |由 IoT 中樞在裝置到雲端訊息上設定的驗證方法。 這個屬性包含用來驗證傳送訊息之裝置的驗證方法的相關資訊。 <!-- ROBINSH For more information, see [Device to cloud anti-spoofing][lnk-antispoofing].--> | [否] 表示 D2C 訊息；[是] 表示其他。 |
| iothub-creation-time-utc | 在裝置上建立訊息的日期和時間。 裝置必須明確設定此值。 | 是 |

## <a name="message-size"></a>訊息大小

IoT 中樞會以不限通訊協定的方式 (只考慮實際的承載) 測量郵件大小。 大小 (以位元組為單位) 的計算方式為下列項目的總和︰

* 內文大小 (以位元組為單位)。
* 訊息系統屬性所有值的大小 (以位元組為單位)。
* 所有使用者屬性名稱和值的大小 (以位元組為單位)。

屬性名稱和值僅限使用 ASCII 字元，因此字串長度會等於大小 (以位元組為單位)。

## <a name="anti-spoofing-properties"></a>防詐騙屬性

為了避免裝置到雲端的訊息中出現裝置詐騙，IoT 中樞使用下列屬性在所有訊息上加上戳記：

* **iothub-connection-device-id**
* **iothub-connection-auth-generation-id**
* **iothub-connection-auth-method**

前兩個屬性包含來源裝置的 **deviceId** 和 **generationId** (依據[裝置身分識別屬性](iot-hub-devguide-identity-registry.md#device-identity-properties))。

**iothub-connection-auth-method** 屬性包含具有下列屬性的 JSON 序列化物件︰

```json
{
  "scope": "{ hub | device }",
  "type": "{ symkey | sas | x509 }",
  "issuer": "iothub"
}
```

## <a name="next-steps"></a>後續步驟

* 如需 IoT 中樞訊息大小限制的資訊，請參閱 [IoT 中樞配額和節流](iot-hub-devguide-quotas-throttling.md)。

* 若要了解如何以各種程式設計語言建立及讀取 IoT 中樞訊息，請參閱[快速入門](quickstart-send-telemetry-node.md)。
