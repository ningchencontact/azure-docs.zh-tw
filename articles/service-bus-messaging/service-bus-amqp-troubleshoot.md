---
title: 針對 Azure 服務匯流排中的 AMQP 錯誤進行疑難排解 |Microsoft Docs
description: 提供一份 AMQP 錯誤，您可能會收到使用 Azure 服務匯流排時，而且會導致這些錯誤。
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.assetid: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/03/2019
ms.author: aschhab
ms.openlocfilehash: 85d24a9e7c753ec5dba80c4f259dd3fb51d9c14b
ms.sourcegitcommit: 9f4eb5a3758f8a1a6a58c33c2806fa2986f702cb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2019
ms.locfileid: "58910041"
---
# <a name="amqp-errors-in-azure-service-bus"></a>Azure 服務匯流排中 AMQP 錯誤
本文章提供一些與 Azure 服務匯流排使用 AMQP 時，您會收到的錯誤。 也就是服務的所有標準的行為。 您可以避免它們藉由傳送/接收呼叫連線/連結，這會自動重新建立連線/連結。

## <a name="link-is-closed"></a>連結已關閉 
AMQP 連線和連結位於作用中但不是包含呼叫時，您會看到下列錯誤 （例如，傳送或接收） 會使用 10 分鐘的連結。 因此，連結已關閉。 連接仍為開啟。

```
amqp:link:detach-forced:The link 'G2:7223832:user.tenant0.cud_00000000000-0000-0000-0000-00000000000000' is force detached by the broker due to errors occurred in publisher(link164614). Detach origin: AmqpMessagePublisher.IdleTimerExpired: Idle timeout: 00:10:00. TrackingId:00000000000000000000000000000000000000_G2_B3, SystemTracker:mynamespace:Topic:MyTopic, Timestamp:2/16/2018 11:10:40 PM
```

## <a name="connection-is-closed"></a>連接已關閉
在連接中的所有連結都已都關閉，因為沒有任何活動 （閒置），而且未在 5 分鐘內建立新連結時，您可以看到 AMQP 連接上的下列錯誤。

```
Error{condition=amqp:connection:forced, description='The connection was inactive for more than the allowed 300000 milliseconds and is closed by container 'LinkTracker'. TrackingId:00000000000000000000000000000000000_G21, SystemTracker:gateway5, Timestamp:2019-03-06T17:32:00', info=null}
```

## <a name="link-is-not-created"></a>不建立連結 
建立新的 AMQP 連線，但不是會建立 AMQP 連線的 1 分鐘內建立一個連結時，您會看到此錯誤。

```
Error{condition=amqp:connection:forced, description='The connection was inactive for more than the allowed 60000 milliseconds and is closed by container 'LinkTracker'. TrackingId:0000000000000000000000000000000000000_G21, SystemTracker:gateway5, Timestamp:2019-03-06T18:41:51', info=null}
```

## <a name="next-steps"></a>後續步驟

若要深入了解 AMQP 和服務匯流排，請造訪下列連結：

* [服務匯流排 AMQP 概觀]
* [AMQP 1.0 通訊協定指南]
* [在適用於 Windows Server 的服務匯流排的 AMQP]

[服務匯流排 AMQP 概觀]: service-bus-amqp-overview.md
[AMQP 1.0 通訊協定指南]: service-bus-amqp-protocol-guide.md
[在適用於 Windows Server 的服務匯流排的 AMQP]: https://docs.microsoft.com/previous-versions/service-bus-archive/dn282144(v=azure.100)
