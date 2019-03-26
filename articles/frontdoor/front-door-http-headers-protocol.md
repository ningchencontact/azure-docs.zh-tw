---
title: Azure 的前端服務-HTTP 標頭支援 |Microsoft Docs
description: 本文可協助您了解 Front Door 支援的 HTTP 標頭通訊協定
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: 40bfdfc3837da12f62864433508482a65def291c
ms.sourcegitcommit: 280d9348b53b16e068cf8615a15b958fccad366a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/25/2019
ms.locfileid: "58407312"
---
# <a name="azure-front-door-service---http-headers-support"></a>Azure 的前端服務-HTTP 標頭支援
本文件概述 Azure Front Door Service 以下圖所述呼叫路徑的各種組件支援的通訊協定。 下列各節針對 Front Door 支援的 HTTP 標頭提供更多見解。

![Azure Front Door Service HTTP 標頭通訊協定][1]

>[!WARNING]
>Azure Front Door Service 不會對這裡未記載的任何 HTTP 標頭提供保證。

## <a name="1-client-to-front-door"></a>1.用戶端至 Front Door
Front Door 會接受來自傳入要求的大多數標頭 (但不會加以修改)，不過，有一些保留的標頭將會從傳入要求中移除 (如果已傳送)。 這包括具有下列前置詞的標頭：
 - X-FD-*

## <a name="2-front-door-to-backend"></a>2.Front Door 至後端

Front Door 會包含來自傳入要求的標頭，除非這些標頭由於上述的限制而遭到移除。 Front Door 也會新增下列標題：

| 頁首  | 範例和描述 |
| ------------- | ------------- |
| Via |  *透過：1.1 Azure* </br> 前端會將透過標頭後面接著 'Azure' 做為值的用戶端的 HTTP 版本。 它會新增至指出用戶端的 HTTP 版本但該 Azure 大門中繼的收件者的用戶端與後端之間的要求。  |
| X-Azure-ClientIP | *X Azure ClientIP:127.0.0.1* </br> 表示正在處理的要求相關聯的 「 用戶端 」 網際網路通訊協定位址。 比方說，來自 proxy 的要求可能會將 X-轉送標頭來指出原始呼叫端的 IP 位址。 |
| X-Azure-SocketIP | *X-Azure-SocketIP:127.0.0.1* </br> 表示 TCP 連線，來自目前要求相關聯的通訊端網際網路通訊協定位址。 要求的用戶端 IP 位址可能會無法等於其通訊端 IP 位址，因為它將會任意覆寫使用者。|
| X-Azure-Ref | *X Azure 參考：0zxV+XAAAAABKMMOjBv2NT4TY6SQVjC0zV1NURURHRTA2MTkANDM3YzgyY2QtMzYwYS00YTU0LTk0YzMtNWZmNzA3NjQ3Nzgz* </br> 這是唯一的參考字串，可識別由 Front Door 提供服務的要求。 這對於疑難排解極為重要，因為它用來搜尋存取記錄。|
| X-Azure-RequestChain |  *X Azure RequestChain： 躍點 = 1* </br> 這是 Front Door 用來偵測要求迴圈的標頭，使用者不應該相依於它。 |
| X-Forwarded-For | *X 轉送的：127.0.0.1* </br> (XFF) HTTP 標頭欄位是常見的方法，用來識別連線到 web 伺服器透過 HTTP proxy 或負載平衡器的用戶端的原始 IP 位址。 如果有現有的 XFF 標頭，則前端會將用戶端通訊端 IP 附加至其他加入 XFF 標頭的用戶端通訊端 ip。 |
| X-Forwarded-Host | *X 轉送主機： contoso.azurefd.net* </br> X 轉送主機 HTTP 標頭欄位是常見的方法，用來識別原始的主機，因為前端的主機名稱可能不同的處理要求的後端伺服器中的主應用程式 HTTP 要求標頭中，用戶端要求。 |
| X-Forwarded-Proto | *X 轉送 Proto: http* </br> X 轉送 Proto HTTP 標頭欄位會識別 HTTP 要求的原始通訊協定，因為根據組態大門可能與後端使用 HTTPS，即使反向 proxy 的要求為 HTTP 通訊的常見方法。 |

## <a name="3-front-door-to-client"></a>3.Front Door 至用戶端

以下是從 Front Door 傳送至用戶端的標頭。 從後端傳送至 Front Door 的任何標頭也會傳遞給用戶端。

| 頁首  | 範例 |
| ------------- | ------------- |
| X-Azure-Ref |  *X Azure 參考：0zxV+XAAAAABKMMOjBv2NT4TY6SQVjC0zV1NURURHRTA2MTkANDM3YzgyY2QtMzYwYS00YTU0LTk0YzMtNWZmNzA3NjQ3Nzgz* </br> 這是唯一的參考字串，可識別由 Front Door 提供服務的要求。 這對於疑難排解極為重要，因為它用來搜尋存取記錄。|

## <a name="next-steps"></a>後續步驟

- 了解如何[建立 Front Door](quickstart-create-front-door.md)。
- 了解 [Front Door 的運作方式](front-door-routing-architecture.md)。

<!--Image references-->
[1]: ./media/front-door-http-headers-protocol/front-door-protocol-summary.png