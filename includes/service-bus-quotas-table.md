---
title: 包含檔案
description: 包含檔案
services: service-bus-messaging
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 12/13/2018
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: f48ad6ca74e6ce10148d66549fea16bc74015b2a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66171225"
---
下表列出特定 Azure 服務匯流排傳訊的配額資訊。 如需定價資訊及其他配額的服務匯流排，請參閱[服務匯流排定價](https://azure.microsoft.com/pricing/details/service-bus/)。

| 配額名稱 | `Scope` | 注意 | 值 |
| --- | --- | --- | --- |
| 每個 Azure 訂用帳戶的基本或標準命名空間的最大數目 |命名空間 |Azure 入口網站會拒絕後續對於其他基本或標準的命名空間的要求。 |100|
| 每個 Azure 訂用帳戶的進階命名空間的最大數目 |命名空間 |入口網站會拒絕後續對於更多進階命名空間的要求。 |25 |
| 佇列或主題大小 |實體 |在建立佇列或主題時定義。 <br/><br/> 後續內送訊息會遭到拒絕，並呼叫程式碼會收到例外狀況。 |1、2、3、4 GB 或 5 GB。<br /><br />在 Premium SKU 和標準 SKU[分割](/azure/service-bus-messaging/service-bus-partitioning)啟用，佇列或主題大小上限是 80 GB。 |
| 命名空間上的並行連線數目 |命名空間 |其他連接的後續要求都會遭到拒絕，並呼叫程式碼會收到例外狀況。 REST 作業都不會計入並行 TCP 連線。 |NetMessaging：1,000.<br /><br />AMQP：5,000. |
| 接收要求在佇列、 主題或訂用帳戶實體上的並行數目。 |實體 |後續接收要求會遭到拒絕，並呼叫程式碼會收到例外狀況。 這個配額套用至一個主題的所有訂用帳戶的並行接收作業數目合計。 |5,000 |
| 每個命名空間的佇列或主題的數目 |命名空間 |後續在命名空間上建立新主題或佇列的要求都會遭到拒絕。 因此，如果透過 [Azure 入口網站][Azure portal]設定，則會產生錯誤訊息。 如果從管理 API 進行呼叫，則呼叫端程式碼會收到例外狀況。 |基本或標準層次的 1,000。 主題和佇列的命名空間中的總數必須小於或等於 1,000。 <br/><br/>針對優質層次，每個傳訊單位 (MU) 1,000。 最大限制是 4,000。 |
| 數目[分割佇列或主題](/azure/service-bus-messaging/service-bus-partitioning)每個命名空間 |命名空間 |後續要求在命名空間上建立新分割主題或佇列會遭到拒絕。 因此，如果透過 [Azure 入口網站][Azure portal]設定，則會產生錯誤訊息。 如果從管理 API，例外狀況呼叫**QuotaExceededException**接收呼叫程式碼。 |基本和標準層級：100。<br/><br/>不支援分割的實體[Premium](../articles/service-bus-messaging/service-bus-premium-messaging.md)層。<br/><br />每個資料分割的佇列或主題會計入每個命名空間的 1,000 個實體的配額。 |
| 任何傳訊實體路徑的大小上限︰佇列或主題 |實體 |- |260 個字元。 |
| 任何傳訊實體名稱的大小上限︰命名空間、訂用帳戶或訂用帳戶規則 |實體 |- |50 個字元。 |
| [訊息 ID](/dotnet/api/microsoft.azure.servicebus.message.messageid) 的大小上限 | 實體 |- | 128 |
| 訊息的大小上限[工作階段識別碼](/dotnet/api/microsoft.azure.servicebus.message.sessionid) | 實體 |- | 128 |
| 佇列、 主題或訂用帳戶實體的訊息大小 |實體 |超出這些配額的內送訊息會遭到拒絕，並呼叫程式碼會收到例外狀況。 |訊息大小上限：256 KB[標準層](../articles/service-bus-messaging/service-bus-premium-messaging.md)，1 MB[進階層](../articles/service-bus-messaging/service-bus-premium-messaging.md)。 <br /><br />由於系統額外負荷，所以此限制小於這些值。<br /><br />標頭大小上限︰64 KB。<br /><br />屬性包中的標頭屬性的最大數目：**位元組/int。MaxValue**。<br /><br />屬性包中的屬性大小上限︰沒有明確限制。 受限於標頭大小上限。 |
| 佇列、 主題或訂用帳戶實體的訊息屬性大小 |實體 | 例外狀況**SerializationException**產生。 |訊息屬性大小上限為每個屬性是 32000。 所有屬性的累計大小不得超過 64,000。 這項限制會套用到整個標頭[BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage)，其中包含使用者屬性和系統屬性，例如[SequenceNumber](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sequencenumber)，[標籤](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.label)，和[MessageId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.messageid)。 |
| 每個主題的訂用帳戶數目 |實體 |後續要求建立主題的其他訂用帳戶會遭到拒絕。 因此，如果透過入口網站設定，則會顯示錯誤訊息。 如果從管理 API 進行呼叫，則呼叫端程式碼會收到例外狀況。 |標準和進階層：每個訂用帳戶計算的配額 1,000 個實體，也就是佇列、 主題和訂用帳戶，每個命名空間。 |
| 每個主題的 SQL 篩選器數目 |實體 |後續要求建立主題的其他篩選器都會遭到拒絕，並呼叫程式碼會收到例外狀況。 |2,000 |
| 每個主題的相互關聯篩選器數目 |實體 |後續要求建立主題的其他篩選器都會遭到拒絕，並呼叫程式碼會收到例外狀況。 |100,000 |
| SQL 篩選條件或動作的大小 |命名空間 |後續要求建立其他篩選器都會遭到拒絕，並呼叫程式碼會收到例外狀況。 |篩選條件字串的長度上限︰1024 (1K)。<br /><br />規則動作字串的長度上限︰1024 (1K)。<br /><br />每個規則動作的運算式數目上限︰32. |
| 每個命名空間、佇列或主題的 [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) 規則數目 |實體、命名空間 |後續要求建立其他規則都會遭到拒絕，並呼叫程式碼會收到例外狀況。 |規則數目上限︰12. <br /><br /> 在服務匯流排命名空間上設定的規則可套用到該命名空間中的所有佇列和主題。 |
| 每個交易的訊息數目 | Transaction | 其他內送訊息會遭到拒絕，並收到例外狀況，指出 「 無法傳送超過 100 個訊息在單一交易中 」 時所呼叫的程式碼。 | 100 <br /><br /> 適用於 **Send()** 和 **SendAsync()** 作業。 |

[Azure portal]: https://portal.azure.com
