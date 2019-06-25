---
title: 監視計量與 Azure 前端服務中的記錄檔 |Microsoft Docs
description: 這篇文章描述 Azure 前端服務支援的存取記錄與不同的計量
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
ms.openlocfilehash: 16770ea0a320b3d9f081cc21a102ab050a6467f6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "60736769"
---
# <a name="monitoring-metrics-and-logs-in-azure-front-door-service"></a>監視計量與 Azure 前端服務中的記錄

使用 Azure 前端服務，您可以透過下列方式監視資源：

- **計量**。 應用程式閘道目前有七個計量，可用來檢視效能計數器。
- **記錄檔**。 活動和診斷記錄可讓效能、 存取和儲存，或從監視等用途的資源取用的其他資料。

### <a name="metrics"></a>度量

計量是可讓您在入口網站中檢視效能計數器的特定 Azure 資源的功能。 以下是可用的 「 大門 」 計量：

| 計量 | 計量顯示名稱 | 單位 | 維度 | 描述 |
| --- | --- | --- | --- | --- |
| RequestCount | 要求計數 | 計數 | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | 由 Front Door 提供服務的用戶端要求數。  |
| RequestSize | 要求大小 | 位元組 | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | 從用戶端傳送到 Front Door 之要求的位元組數。 |
| ResponseSize | 回應大小 | 位元組 | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | 從 Front Door 傳送到用戶端之回應的位元組數。 |
| TotalLatency | 延遲總計 | 毫秒 | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | 從用戶端要求接收的大門，直到用戶端認可最後一個回應位元組從大門，計算時間。 |
| BackendRequestCount | 後端要求計數 | 計數 | HttpStatus</br>HttpStatusGroup</br>後端 | 從 Front Door 傳送至後端的要求數。 |
| BackendRequestLatency | 後端要求延遲 | 毫秒 | 後端 | 從 Front Door 傳送要求到後端一直到 Front Door 從後端接收最後回應位元組所經歷的時間。 |
| BackendHealthPercentage | 後端健康情況百分比 | 百分比 | 後端</br>BackendPool | 從 Front Door 到後端的健康情況探查成功百分比。 |
| WebApplicationFirewallRequestCount | Web 應用程式防火牆要求計數 | 計數 | PolicyName</br>RuleName</br>動作 | Front Door 應用程式層安全性處理的用戶端要求數。 |

## <a name="activity-log"></a>活動記錄

活動記錄可在前端服務上進行作業的相關資訊。 它們也可以決定內容、 對象，以及何時任何寫入作業 （put、 post 或 delete） 前端服務上。

>[!NOTE]
>活動記錄不包含讀取 (get) 作業。 它們也不包含您使用 Azure 入口網站或原始管理 API 執行的作業。

在您的前端服務或 Azure 資源在 Azure 監視器中的所有記錄檔的存取活動記錄。 檢視活動記錄：

1. 選取您的前端執行個體。
2. 選取 **活動記錄**。

    ![活動記錄檔](./media/front-door-diagnostics/activity-log.png)

3. 選擇要篩選的範圍，然後按**套用**。

## <a name="diagnostic-logging"></a>診斷記錄
診斷記錄會提供作業和對稽核和疑難排解很重要的錯誤相關的豐富資訊。 診斷記錄與活動記錄不同。

活動記錄可深入了解對 Azure 資源的作業。 診斷記錄檔提供深入了解您的資源執行的作業。 如需詳細資訊，請參閱 < [Azure 監視器診斷記錄](../azure-monitor/platform/diagnostic-logs-overview.md)。

![診斷記錄](./media/front-door-diagnostics/diagnostic-log.png)

若要為您的前端服務設定診斷記錄檔：

1. 選取您的 Azure 前端服務。

2. 選擇**診斷設定**。

3. 選取 [開啟診斷]  。 封存診斷記錄連同計量的儲存體帳戶、 串流至事件中樞，或將它們傳送至 Azure 監視器記錄檔。

前端服務目前會提供診斷記錄檔 （每小時批次處理）。 診斷記錄會提供每個項目都有下列結構描述中的個別 API 要求：

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

- [建立前端的設定檔](quickstart-create-front-door.md)
- [前端的運作方式](front-door-routing-architecture.md)
