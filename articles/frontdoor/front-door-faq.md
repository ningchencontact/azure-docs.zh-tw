---
title: Azure 的前端服務-大門的常見問題集 |Microsoft Docs
description: 此頁面提供有關 Azure 前端服務常見問題的解答
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/08/2019
ms.author: sharadag
ms.openlocfilehash: b033f463722ddb3a0b7beabdf659900e7d7188df
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/22/2019
ms.locfileid: "67330873"
---
# <a name="frequently-asked-questions-for-azure-front-door-service"></a>Azure 前端服務常見問題的解答

這篇文章會回答有關 Azure 前端服務特性與功能的常見問題。 如果您找不到問題的答案，可透過下列管道 (依先後順序) 和我們連絡：

1. 本文的留言區。
2. [Azure 的前端服務 UserVoice](https://feedback.azure.com/forums/217313-networking?category_id=345025)。
3. **Microsoft 支援服務：** 若要建立新的支援要求，在 Azure 入口網站的 [說明]  索引標籤上，選取 [說明 + 支援]  按鈕，然後選取 [新增支援要求]  。

## <a name="general"></a>一般

### <a name="what-is-azure-front-door-service"></a>Azure 前端服務是什麼？

Azure 的前端服務是應用程式傳遞網路 (ADN) 做為服務，提供各種第 7 層負載平衡功能的應用程式。 它會提供動態網站加速 (DSA) 以及全域負載平衡及近乎即時的容錯移轉。 它是高度可用且可調整服務，這完全由 Azure 管理。

### <a name="what-features-does-azure-front-door-service-support"></a>Azure 前端服務支援哪些功能？

Azure 的前端服務支援動態網站加速 (DSA)、 SSL 卸載和端對端 SSL、 Web 應用程式防火牆、 cookie 型工作階段親和性、 url 路徑型路由、 可用的憑證和多個網域管理和其他項目。 如需支援之功能的完整清單，請參閱 < [Azure 前端服務概觀](front-door-overview.md)。

### <a name="what-is-the-difference-between-azure-front-door-service-and-azure-application-gateway"></a>什麼是 Azure 前端服務和 Azure 應用程式閘道之間的差異？

前端和應用程式閘道是第 7 (HTTP/HTTPS) 負載平衡器的層，而主要的差異是大門是全域的服務，而應用程式閘道是一項區域性服務。 雖然大門可以跨區域您不同的縮放單位/叢集/戳記單位之間的平衡負載，應用程式閘道可讓您縮放單位內為您的 Vm/容器等之間進行負載平衡。

### <a name="when-should-we-deploy-an-application-gateway-behind-front-door"></a>何時我們應該部署應用程式閘道背後大門？

其中一個應該使用應用程式閘道前端背後的原因的重要案例如下：

- 前端可以執行以路徑為基礎的負載平衡只能在全域層級，但如果有一位想流量進行負載平衡更進一步地在他們的虛擬網路 (VNET) 內，則他們應該使用應用程式閘道。
- 因為大門無法在 VM/容器層級運作，因此無法執行連線清空。 不過，應用程式閘道可讓您執行連線清空。 
- 使用應用程式閘道背後 AFD，其中一個可達到 100 %ssl 卸載，並將路由只在他們的虛擬網路 (VNET) 內的 HTTP 要求。
- 前端和應用程式閘道支援工作階段親和性。 前端可以直接從使用者工作階段的後續流量至相同的叢集或指定區域中的後端，而應用程式閘道可以直接使相似化到相同的伺服器，在叢集內的流量。  

### <a name="can-we-deploy-azure-load-balancer-behind-front-door"></a>我們可以部署 Azure 負載平衡器前端嗎？

Azure 的前端服務必須將流量路由到公用 VIP 或公開可用的 DNS 名稱。 部署 Azure Load Balancer 後方大門是一個常見使用案例。

### <a name="what-protocols-does-azure-front-door-service-support"></a>Azure 前端服務支援哪些通訊協定？

Azure 的前端服務支援 HTTP、 HTTPS 和 HTTP/2。

### <a name="how-does-azure-front-door-service-support-http2"></a>Azure 前端服務如何支援 HTTP/2？

只連線到 Azure 前端服務的用戶端使用 HTTP/2 通訊協定支援。 後端集區中的後端的通訊是透過 HTTP/1.1。 預設會啟用 HTTP/2 支援。

### <a name="what-resources-are-supported-today-as-part-of-backend-pool"></a>目前支援哪些資源做為後端集區的一部分？

後端集區是由儲存體、 Web 應用程式、 Kubernetes 執行個體或任何其他自訂主機名稱具有公用連線能力所組成。 Azure 的前端服務會需要所定義的後端，透過公用 IP 或可公開解析的 DNS 主機名稱。 後端集區的成員可以在跨越多個區域，區域，甚至是 Azure 或外部，只要它們有公用的連線。

### <a name="what-regions-is-the-service-available-in"></a>哪些區域提供此服務？

Azure 的前端服務是一種全域服務，並不會繫結至任何特定的 Azure 區域。 您需要建立大門時指定的唯一位置是資源群組位置，基本上這指定的資源群組的中繼資料的儲存位置。 前端資源本身會建立為全域資源，並設定全域部署到所有 Pop （存在點）。 

### <a name="what-are-the-pop-locations-for-azure-front-door-service"></a>什麼是 Azure 前端服務的 POP 位置？

Azure 的前端服務都來自 Microsoft 的 Azure CDN 的 POP （存在點） 的位置相同的清單。 如我們 Pop 的完整清單，請參閱[從 Microsoft Azure CDN POP 位置](https://docs.microsoft.com/azure/cdn/cdn-pop-locations)。

### <a name="is-azure-front-door-service-a-dedicated-deployment-for-my-application-or-is-it-shared-across-customers"></a>是 Azure 前端服務專用的部署我的應用程式，或為所有客戶共用嗎？

Azure 的前端服務是分散在世界各地的多租用戶服務。 因此，大門的基礎結構的所有客戶共用。 不過，藉由建立前端設定檔，定義您的應用程式所需的特定設定，而且沒有您的前端所做的變更會影響其他前端組態。

### <a name="is-http-https-redirection-supported"></a>是否支援 HTTP->HTTPS 重新導向？

是。 事實上，Azure 前端服務支援的主機、 路徑和查詢字串重新導向，以及組件的 URL 重新導向。 深入了解[URL 重新導向](front-door-url-redirect.md)。 

### <a name="in-what-order-are-routing-rules-processed"></a>路由規則處理順序為何？

未排序您的前端的路由和特定的路由根據最符合項目。 深入了解[如何大門符合要求的路由規則](front-door-route-matching.md)。

### <a name="how-do-i-lock-down-the-access-to-my-backend-to-only-azure-front-door-service"></a>我該如何以我的後端，以 Azure 前端服務鎖定的存取？

您可以設定執行 IP Acl 的您的後端接受僅來自 Azure 前端服務流量。 您可以限制您的應用程式，以接受連入連線，只能從 Azure 前端服務的後端 IP 空間。 我們正在努力整合[Azure IP 範圍和服務標籤](https://www.microsoft.com/download/details.aspx?id=56519)不過現在您可以直接參考，如下所示的 IP 範圍：
 
- **IPv4** - `147.243.0.0/16`
- **IPv6** - `2a01:111:2050::/44`

> [!WARNING]
> 我們的後端 IP 空間可能會稍後變更，不過，我們會確認之前發生這種情況，我們會在使用已整合[Azure IP 範圍和服務標籤](https://www.microsoft.com/download/details.aspx?id=56519)。 我們建議您訂閱[Azure IP 範圍和服務標籤](https://www.microsoft.com/download/details.aspx?id=56519)是否有任何變更或更新。 

### <a name="can-the-anycast-ip-change-over-the-lifetime-of-my-front-door"></a>任一傳播 IP 可以變更我的大門存留期嗎？

您前端的任一傳播 FRONTEND-IP 通常不應該變更，並可能會維持不變的大門存留期。 不過，還有**不保證**相同。 Ip 才請任何直接依存性。  

### <a name="does-azure-front-door-service-support-static-or-dedicated-ips"></a>Azure 前端服務是否支援靜態或專用的 Ip？

否，Azure 前端服務目前不支援靜態或專用的前端任一傳播 Ip。 

### <a name="does-azure-front-door-service-support-x-forwarded-for-headers"></a>Azure 前端服務是否支援 x 轉送的標頭？

是，Azure 前端服務支援的 X 轉送的、 X-轉送-主機和 X 轉送 Proto 標頭。 如果 X 轉送的標頭已經存在，則大門附加到其中的用戶端通訊端 IP。 否則，它會新增與用戶端通訊端 IP 標頭的值。 如需 X 轉送主機和 X 轉送 Proto，值會覆寫。

深入了解[大門支援的 HTTP 標頭](front-door-http-headers-protocol.md)。  

### <a name="how-long-does-it-take-to-deploy-an-azure-front-door-service-does-my-front-door-still-work-when-being-updated"></a>部署 Azure 前端服務需要多久的時間？ 我的大門仍然運作時正在更新？

建立新的大門或現有的前端的任何更新需要大約 3 到 5 分鐘的時間全域部署。 這表示在大約 3 到 5 分鐘內，您的前端設定將會部署我們 Pop 的所有全域。

附註-自訂 SSL 憑證更新需要大約 30 分鐘，若要部署到全球。

## <a name="configuration"></a>組態

### <a name="can-azure-front-door-load-balance-or-route-traffic-within-a-virtual-network"></a>Azure 前端的負載平衡或路由流量是否可以在虛擬網路？

Azure 大門 」 (AFD) 需要公用 IP 或將流量路由傳送至可公開解析的 DNS 名稱。 因此，答案是沒有 AFD 直接無法路由傳送虛擬網路內，但之間使用的應用程式閘道或 Azure Load Balancer 會解決此案例。

### <a name="what-are-the-various-timeouts-and-limits-for-azure-front-door-service"></a>各種不同的逾時和 Azure 前端服務的限制為何？

了解所有記載[逾時 Azure 前端服務和限制](https://docs.microsoft.com/azure/azure-subscription-service-limits#azure-front-door-service-limits)。

## <a name="performance"></a>效能

### <a name="how-does-azure-front-door-service-support-high-availability-and-scalability"></a>如何 Azure 前端服務支援高可用性和延展性？

Azure 的前端服務是容量的分散在世界各地的多租用戶平台有很大的磁碟區，以符合您的應用程式的延展性需求。 經由 Microsoft 的全球網路的邊緣，前端會提供全域負載平衡功能，可讓您容錯移轉您整個應用程式或甚至是個別的微服務中，跨區域或不同的雲端。

## <a name="ssl-configuration"></a>SSL 設定

### <a name="what-tls-versions-are-supported-by-azure-front-door-service"></a>TLS 版本支援 Azure 前端服務？

前門支援 TLS 1.0、 1.1 和 1.2 版。 尚未支援 TLS 1.3。

### <a name="what-certificates-are-supported-on-azure-front-door-service"></a>Azure 前端服務支援哪些憑證？

若要啟用 HTTPS 通訊協定來安全地傳遞大門的自訂網域上的內容，您可以選擇使用受 Azure 前端服務的憑證，或使用您自己的憑證。
前門 Digicert 透過標準的 SSL 憑證管理選項佈建，並儲存在 Front 門的金鑰保存庫。 如果您選擇使用您自己的憑證，則您可以上架支援之 CA 的憑證，可以是標準的 SSL、 延伸的驗證憑證或甚至是萬用字元憑證。 不支援自我簽署的憑證。 了解[如何啟用自訂網域 HTTPS](https://aka.ms/FrontDoorCustomDomainHTTPS)。

### <a name="does-front-door-support-auto-rotation-of-certificates"></a>前門是否支援自動輪替的憑證？

您自己自訂的 SSL 憑證，不支援自動輪替。 類似於如何安裝第一次時指定的自訂網域，您需要以點大門至正確的憑證版本您金鑰保存庫中及確保前端的服務主體仍有 Key Vault 存取權。 此更新的憑證首度發行作業的大門完全不可部分完成，且不會造成任何提供主體名稱的影響實際或不會變更憑證的 SAN。
</br>受管理的大門憑證選項時，憑證會自動旋轉的大門。

### <a name="what-are-the-current-cipher-suites-supported-by-azure-front-door-service"></a>目前 Azure 大門 Service 所支援的加密套件有哪些？

以下是目前 Azure 大門 Service 所支援的加密套件：

- TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA
- TLS_RSA_WITH_AES_256_GCM_SHA384
- TLS_RSA_WITH_AES_128_GCM_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA256
- TLS_RSA_WITH_AES_128_CBC_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA
- TLS_RSA_WITH_AES_128_CBC_SHA

### <a name="does-azure-front-door-service-also-support-re-encryption-of-traffic-to-the-backend"></a>Azure 前端服務是否也支援後端流量重新加密？

是，Azure 前端服務支援 SSL 卸載，以及可重新加密至後端流量的端對端 SSL。 事實上，由於後端的連線會透過公用 IP，建議您設定您要使用 HTTPS 做為轉送通訊協定的大門。

### <a name="can-i-configure-ssl-policy-to-control-ssl-protocol-versions"></a>是否可設定 SSL 原則來控制 SSL 通訊協定版本嗎？

否，目前大門不支援以拒絕特定的 TLS 版本，也可以設定最小的 TLS 版本。 

### <a name="can-i-configure-front-door-to-only-support-specific-cipher-suites"></a>可以設定為僅支援特定的加密套件的大門嗎？

否，不支援設定針對特定的加密套件的大門。 

## <a name="diagnostics-and-logging"></a>診斷和記錄

### <a name="what-types-of-metrics-and-logs-are-available-with-azure-front-door-service"></a>哪些類型的計量和記錄檔與 Azure 前端服務可用？

如需記錄檔和其他診斷功能，請參閱 <<c0> [ 大門監視計量與記錄](front-door-diagnostics.md)。

### <a name="what-is-the-retention-policy-on-the-diagnostics-logs"></a>診斷記錄的保留原則為何？

診斷記錄會送至客戶儲存體帳戶，而客戶可以根據其喜好來設定保留原則。 診斷記錄也可以傳送至事件中樞或 Azure 監視器記錄。 如需詳細資訊，請參閱 < [Azure 前端服務診斷](front-door-diagnostics.md)。

### <a name="how-do-i-get-audit-logs-for-azure-front-door-service"></a>如何取得 Azure 前端服務的稽核記錄？

稽核記錄可供 Azure 前端服務。 在入口網站中，按一下**活動記錄檔**您大門，來存取稽核記錄檔的功能表刀鋒視窗中。 

### <a name="can-i-set-alerts-with-azure-front-door-service"></a>我要設定與 Azure 前端服務的警示？

是，Azure 前端服務支援的警示。 您可以針對計量設定警示。 

## <a name="next-steps"></a>後續步驟

- 了解如何[建立 Front Door](quickstart-create-front-door.md)。
- 了解 [Front Door 的運作方式](front-door-routing-architecture.md)。