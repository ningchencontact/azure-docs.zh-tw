---
title: 通訊協定支援 Azure 前端服務中的 HTTP 標頭 |Microsoft Docs
description: 本文說明前端服務支援的 HTTP 標頭通訊協定。
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
ms.openlocfilehash: 92e8435e4336c68982e4becc2a95f99b2c776c0e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "60736630"
---
# <a name="protocol-support-for-http-headers-in-azure-front-door-service"></a>在 Azure 前端服務中的 HTTP 標頭的通訊協定支援
本文概述前端服務的呼叫路徑的組件所支援的通訊協定 （請見圖）。 下列各節提供前端服務所支援的 HTTP 標頭的詳細資訊。

![Azure Front Door Service HTTP 標頭通訊協定][1]

>[!IMPORTANT]
>前端服務不保證這裡不記錄任何 HTTP 標頭。

## <a name="client-to-front-door-service"></a>前端服務用戶端
前端服務會接受來自傳入要求的大多數標頭但不修改。 一些保留的標頭會移除從傳入要求傳送，包括標頭具有 X-FD-* 前置詞。

## <a name="front-door-service-to-backend"></a>前端到後端的服務

除非移除，因為限制，前端服務會包含從傳入要求的標頭。 前端也會加入下列標頭：

| 標頭  | 範例和描述 |
| ------------- | ------------- |
| Via |  透過：1.1 Azure </br> 前端將用戶端的 HTTP 版本，後面接著*Azure*做為 Via 標頭的值。 這表示用戶端的 HTTP 版本但該大門中繼的收件者的用戶端與後端之間的要求。  |
| X-Azure-ClientIP | X-Azure-ClientIP:127.0.0.1 </br> 表示正在處理的要求相關聯的用戶端 IP 位址。 比方說，來自 proxy 的要求可能會將 X-轉送標頭來指出原始呼叫端的 IP 位址。 |
| X-Azure-SocketIP |  X-Azure-SocketIP:127.0.0.1 </br> 表示與目前的要求是來自 TCP 連接相關聯的通訊端 IP 位址。 要求的用戶端 IP 位址可能會無法等於其通訊端 IP 位址，因為它將會任意覆寫使用者。|
| X-Azure-Ref |  X Azure 參考：0zxV+XAAAAABKMMOjBv2NT4TY6SQVjC0zV1NURURHRTA2MTkANDM3YzgyY2QtMzYwYS00YTU0LTk0YzMtNWZmNzA3NjQ3Nzgz </br> 識別由前端提供服務的要求唯一的參考字串。 它用來存取記錄檔中搜尋和重要的疑難排解。|
| X-Azure-RequestChain |  X Azure RequestChain： 躍點 = 1 </br> 標頭，前端會使用來偵測要求迴圈，而且使用者不應該相依於它。 |
| X-Forwarded-For | X 轉送的：127.0.0.1 </br> (XFF) HTTP 標頭欄位通常會識別用戶端連接到 web 伺服器，透過 HTTP proxy 或負載平衡器的原始 IP 位址。 如果沒有現有的 XFF 標頭，大門附加到其中的用戶端通訊端 IP，或加入 XFF 標頭，以用戶端通訊端的 IP。 |
| X-Forwarded-Host | X 轉送主機： contoso.azurefd.net </br> X 轉送主機 HTTP 標頭欄位是用來識別原始的主應用程式主機的 HTTP 要求標頭中的用戶端所要求的常見方式。 這是因為大門的主機名稱可能不同的處理要求的後端伺服器。 |
| X-Forwarded-Proto | X 轉送 Proto: http </br> X 轉送 Proto HTTP 標頭欄位通常用來識別 HTTP 要求的原始通訊協定，因為大門，根據組態，可能會與後端使用 HTTPS 進行通訊。 這是 true，即使反向 proxy 的要求為 HTTP。 |

## <a name="front-door-service-to-client"></a>用戶端前端服務

從後端傳送至前端的任何標頭也傳遞給用戶端時。 以下是從前端傳送至用戶端的標頭。

| 標頭  | 範例 |
| ------------- | ------------- |
| X-Azure-Ref |  *X Azure 參考：0zxV+XAAAAABKMMOjBv2NT4TY6SQVjC0zV1NURURHRTA2MTkANDM3YzgyY2QtMzYwYS00YTU0LTk0YzMtNWZmNzA3NjQ3Nzgz* </br> 這是唯一的參考字串，可識別由 Front Door 提供服務的要求。 這是很重要的疑難排解，因為它用來存取記錄檔中搜尋。|

## <a name="next-steps"></a>後續步驟

- [建立前端](quickstart-create-front-door.md)
- [前端的運作方式](front-door-routing-architecture.md)

<!--Image references-->
[1]: ./media/front-door-http-headers-protocol/front-door-protocol-summary.png