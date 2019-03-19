---
title: Azure Front Door Service - 計量和記錄 | Microsoft Docs
description: 此文章可協助您了解 Azure Front Door Service 支援的不同計量和存取記錄
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/18/2018
ms.author: sharadag
ms.openlocfilehash: 5d4d591c465cae91c59e8f86ea9d3e421db0f952
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/18/2019
ms.locfileid: "58112104"
---
# <a name="monitoring-metrics-for-front-door"></a>監視 Front Door 的計量

使用 Azure 前端服務，您可以透過下列方式監視資源：
* [記錄](#diagnostic-logging)：記錄能夠儲存效能、存取和其他資料，或從資源取用記錄以便進行監視。

* [計量](#metrics)：應用程式閘道目前有七個計量，可用來檢視效能計數器。

## <a name="metrics"></a>度量

計量是某些 Azure 資源的功能，可供您在入口網站中檢視效能計數器。 針對 Front Door，有下列計量可用：

| 計量 | 計量顯示名稱 | 單位 | 維度 | 描述 |
| --- | --- | --- | --- | --- |
| RequestCount | 要求計數 | 計數 | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | 由 Front Door 提供服務的用戶端要求數。  |
| RequestSize | 要求大小 | 位元組 | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | 從用戶端傳送到 Front Door 之要求的位元組數。 |
| ResponseSize | 回應大小 | 位元組 | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | 從 Front Door 傳送到用戶端之回應的位元組數。 |
| TotalLatency | 延遲總計 | 毫秒 | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | 從 Front Door 接收用戶端要求一直到用戶端認可來自 Front Door 的最後回應位元組所經歷的時間。 |
| BackendRequestCount | 後端要求計數 | 計數 | HttpStatus</br>HttpStatusGroup</br>後端 | 從 Front Door 傳送至後端的要求數。 |
| BackendRequestLatency | 後端要求延遲 | 毫秒 | 後端 | 從 Front Door 傳送要求到後端一直到 Front Door 從後端接收最後回應位元組所經歷的時間。 |
| BackendHealthPercentage | 後端健康情況百分比 | 百分比 | 後端</br>BackendPool | 從 Front Door 到後端的健康情況探查成功百分比。 |
| WebApplicationFirewallRequestCount | Web 應用程式防火牆要求計數 | 計數 | PolicyName</br>RuleName</br> 動作 | Front Door 應用程式層安全性處理的用戶端要求數。 |

## <a name="activity-log"></a>活動記錄

活動記錄可深入了解您的前端執行的作業。 使用活動記錄檔，您可以判斷 「 內容、 對象和時間 」 任何寫入作業 (PUT、 POST、 DELETE) 在您的前端。

> [!NOTE]
> 活動記錄不會納入讀取 (GET) 作業，也不會納入透過 Azure 入口網站或原始管理 API 所執行的作業。

您可以在您的大門，存取活動記錄檔，或存取 Azure 監視器中的所有 Azure 資源的記錄。 

檢視活動記錄：

1. 選取您的前端執行個體。
2. 按一下 [活動記錄]。

    ![活動記錄](./media/front-door-diagnostics/activity-log.png)

3. 選取所需的篩選範圍，然後按一下 [套用]。

## <a name="diagnostic-logging"></a>診斷記錄
診斷記錄可提供豐富的作業與錯誤資訊，這些資訊對於稽核和疑難排解用途來說很重要。 診斷記錄與活動記錄不同。 活動記錄可讓您深入了解 Azure 資源上所執行的作業。 診斷記錄能讓您了解資源執行的作業。 深入了解[Azure 監視器診斷記錄](../azure-monitor/platform/diagnostic-logs-overview.md)。 

若要設定您的大門診斷記錄︰

1. 選取您的 APIM 服務執行個體。
2. 按一下 [診斷設定]。

    ![診斷記錄](./media/front-door-diagnostics/diagnostic-log.png)

3. 按一下 [開啟診斷]。 您可以將診斷記錄連同計量封存至儲存體帳戶、將其串流至事件中樞，或將其傳送至 Azure 監視器記錄。 

Azure 的前端服務目前提供的診斷記錄檔 （每小時批次處理） 關於個別 API 要求使用每個項目都有下列結構描述：

| 屬性  | 描述 |
| ------------- | ------------- |
| ClientIp | 發出要求之用戶端的 IP 位址。 |
| ClientPort | 提出要求的用戶端 IP 連接埠。 |
| HttpMethod | 要求使用的 HTTP 方法。 |
| HttpStatusCode | 從 proxy 傳回 HTTP 狀態碼。 |
| HttpStatusDetails | 在要求上的最終狀態。 此字串值的意義，參閱狀態參考資料表。 |
| HttpVersion | 要求或連接的類型。 |
| RequestBytes | 以位元組為單位，包括要求標頭和要求主體的 HTTP 要求訊息的大小。 |
| RequestUri | 接收之要求的 URI。 |
| ResponseBytes | 後端伺服器做為回應所傳送的位元組。  |
| RoutingRuleName | 要求已符合的路由規則的名稱。 |
| SecurityProtocol | 使用要求則為 null，如果沒有加密的 TLS/SSL 通訊協定版本。 |
| TimeTaken | 所花費的動作，以毫秒為單位的時間長度。 |
| UserAgent | 用戶端使用的瀏覽器類型 |
| TrackingReference | 識別由前端，要求的唯一參考字串也做為 X-Azure-Ref 標頭傳送，用戶端。 所需的特定要求存取記錄檔中搜尋詳細資料。 |

## <a name="next-steps"></a>後續步驟

- 了解如何[建立 Front Door](quickstart-create-front-door.md)。
- 了解 [Front Door 的運作方式](front-door-routing-architecture.md)。
