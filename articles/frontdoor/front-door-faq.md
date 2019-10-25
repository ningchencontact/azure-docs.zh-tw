---
title: Azure Front 門板服務-Front 的常見問題 |Microsoft Docs
description: 此頁面提供 Azure Front 服務常見問題的解答
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
ms.openlocfilehash: 4001b3ed8d7a92d8a751486ab4fd3a097d032373
ms.sourcegitcommit: ec2b75b1fc667c4e893686dbd8e119e7c757333a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/23/2019
ms.locfileid: "72809730"
---
# <a name="frequently-asked-questions-for-azure-front-door-service"></a>Azure Front 開門服務的常見問題

本文會回答有關 Azure Front 服務特性和功能的常見問題。 如果您找不到問題的答案，可透過下列管道 (依先後順序) 和我們連絡：

1. 本文的留言區。
2. [Azure Front 門板服務 UserVoice](https://feedback.azure.com/forums/217313-networking?category_id=345025)。
3. **Microsoft 支援服務：** 若要建立新的支援要求，請在**Azure 入口網站的 [說明] 索引**標籤上，選取 [說明 **+ 支援**] 按鈕，然後選取 [**新增支援要求**]。

## <a name="general"></a>一般

### <a name="what-is-azure-front-door-service"></a>Azure 前端服務是什麼？

Azure Front 門板服務是應用程式傳遞網路（ADN）即服務，為您的應用程式提供各種第7層負載平衡功能。 它提供動態的網站加速（DSA）以及具有近乎即時容錯移轉的全域負載平衡。 這是高可用性且可調整的服務，完全由 Azure 管理。

### <a name="what-features-does-azure-front-door-service-support"></a>Azure Front 門板服務支援哪些功能？

Azure Front p 服務支援動態網站加速（DSA）、SSL 卸載和端對端 SSL、Web 應用程式防火牆、以 cookie 為基礎的會話親和性、url 路徑型路由、免費憑證和多個網域管理，以及其他專案。 如需支援功能的完整清單，請參閱[Azure Front 門板服務的總覽](front-door-overview.md)。

### <a name="what-is-the-difference-between-azure-front-door-service-and-azure-application-gateway"></a>Azure 前端服務與 Azure 應用程式閘道之間的差異為何？

雖然 Front 和應用程式閘道都是第7層（HTTP/HTTPS）負載平衡器，但主要的差異在於前端是一個全域服務，而應用程式閘道是區域服務。 雖然 Front 門板可以在不同的縮放單位/叢集/戳記單位之間進行負載平衡，但應用程式閘道可讓您在您的 Vm/容器和縮放單位內進行負載平衡。

### <a name="when-should-we-deploy-an-application-gateway-behind-front-door"></a>我們何時應該將應用程式閘道部署在 Front 後方？

主要案例為何應該使用前端應用程式閘道：

- Front 門板只能在全域層級執行路徑型負載平衡，但如果您想要在其虛擬網路（VNET）內進一步平衡流量負載，則應該使用應用程式閘道。
- 因為 Front 門無法在 VM/容器層級運作，所以無法進行連線清空。 不過，應用程式閘道可讓您清空連接。 
- 在 AFD 背後的應用程式閘道之後，您可以在其虛擬網路（VNET）內達到 100% SSL 卸載並僅路由傳送 HTTP 要求。
- Front 和應用程式閘道都支援會話親和性。 雖然 Front 門可以將使用者會話的後續流量導向至指定區域中的相同叢集或後端，應用程式閘道可以將流量導向到叢集中的相同伺服器。  

### <a name="can-we-deploy-azure-load-balancer-behind-front-door"></a>我們可以在前門後方部署 Azure Load Balancer 嗎？

Azure Front 門板服務需要公用 VIP 或公開可用的 DNS 名稱，才能將流量路由傳送至。 在 Front 門後方部署 Azure Load Balancer 是常見的使用案例。

### <a name="what-protocols-does-azure-front-door-service-support"></a>Azure Front 入口服務支援哪些通訊協定？

Azure Front 開門服務支援 HTTP、HTTPS 和 HTTP/2。

### <a name="how-does-azure-front-door-service-support-http2"></a>Azure Front 門板服務如何支援 HTTP/2？

只有連線到 Azure Front 門板服務的用戶端可以使用 HTTP/2 通訊協定支援。 後端集區中後端的通訊是透過 HTTP/1.1。 預設會啟用 HTTP/2 支援。

### <a name="what-resources-are-supported-today-as-part-of-backend-pool"></a>目前支援哪些資源做為後端集區的一部分？

後端集區可以由儲存體、Web 應用程式、Kubernetes 實例或任何其他具有公用連線的自訂主機名稱組成。 Azure Front 開門服務需要透過公用 IP 或可公開解析的 DNS 主機名稱來定義後端。 後端集區的成員可以跨區域、區域，甚至是在 Azure 外部，只要它們具有公用連線能力。

### <a name="what-regions-is-the-service-available-in"></a>哪些區域提供此服務？

Azure Front 門板服務是一種全域服務，並不會系結至任何特定的 Azure 區域。 在建立 Front 門時，您唯一需要指定的位置是資源群組位置，基本上是指定將儲存資源群組中繼資料的位置。 Front 門板資源本身會建立為全域資源，而且設定會全域部署到所有 Pop （目前狀態點）。 

### <a name="what-are-the-pop-locations-for-azure-front-door-service"></a>Azure Front 服務的 POP 位置為何？

Azure Front 開門服務與來自 Microsoft 的 Azure CDN 具有相同的 POP （存在點）位置清單。 如需 Pop 的完整清單，請參閱[Microsoft 的 AZURE CDN POP 位置](https://docs.microsoft.com/azure/cdn/cdn-pop-locations)。

### <a name="is-azure-front-door-service-a-dedicated-deployment-for-my-application-or-is-it-shared-across-customers"></a>Azure Front 門板服務是應用程式專用的部署，還是跨客戶共用？

Azure Front 門板服務是全域散發的多租使用者服務。 因此，Front 的基礎結構會在其所有客戶之間共用。 不過，藉由建立 Front 門板設定檔，您可以定義應用程式所需的特定設定，而不會影響到其他 Front 設定的任何變更。

### <a name="is-http-https-redirection-supported"></a>是否支援 HTTP->HTTPS 重新導向？

可以。 事實上，Azure Front 門板服務支援主機、路徑和查詢字串重新導向，以及 URL 重新導向的一部分。 深入瞭解[URL](front-door-url-redirect.md)重新導向。 

### <a name="in-what-order-are-routing-rules-processed"></a>路由規則的處理順序為何？

您 Front 的路線不會排序，而且會根據最符合的順序來選取特定的路由。 深入瞭解[Front 門板如何比對要求與路由規則](front-door-route-matching.md)。

### <a name="how-do-i-lock-down-the-access-to-my-backend-to-only-azure-front-door"></a>如何? 將我的後端存取權鎖定為僅限 Azure Front 大門嗎？

若要鎖定您的應用程式，使其只接受來自特定 Front 的流量，您必須為後端設定 IP Acl，然後針對 Azure Front 門所傳送的標頭 ' X-轉送-主機 '，限制其接受的值集合。 這些步驟的詳細說明如下：

- 設定後端的 IP 執行 acl，以接受來自 Azure 前端的後端 IP 位址空間和 Azure 基礎結構服務的流量。 我們正致力於與[AZURE IP 範圍和服務](https://www.microsoft.com/download/details.aspx?id=56519)標籤整合，但現在您可以參考如下的 IP 範圍：
 
    - Front 門板的**IPv4**後端 IP 空間： `147.243.0.0/16`
    - Front 門板的**IPv6**後端 IP 空間： `2a01:111:2050::/44`
    - 透過虛擬化主機 IP 位址的 Azure[基本基礎結構服務](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations)： `168.63.129.16` 和 `169.254.169.254`

    > [!WARNING]
    > 前端的後端 IP 空間可能會在稍後變更，不過我們會在這之前，確保我們已整合[AZURE IP 範圍和服務](https://www.microsoft.com/download/details.aspx?id=56519)標籤。 我們建議您訂閱[AZURE IP 範圍和服務](https://www.microsoft.com/download/details.aspx?id=56519)標籤，以進行任何變更或更新。

-   篩選前門所傳送的連入標頭 '**X-轉送-主機**' 的值。 標頭唯一允許的值應該是所有前端主機，如 Front 門板設定中所定義。事實上，只有您想要接受流量的主機名稱（在您的這個特定後端）。
    - 範例-假設您的 Front 門板設定具有下列前端主機 _`contoso.azurefd.net`_ （A）、 _`www.contoso.com`_ （B）、_ （C）和 _`notifications.contoso.com`_ （D）。 假設您有兩個後端 X 和 Y。 
    - 後端 X 應該只接受主機名稱 A 和 B 的流量。後端 Y 可以接受 A、C 和 D 的流量。
    - 因此，在後端 X 上，您應該只接受將標頭 '**X-轉送主機**' 設為 _`contoso.azurefd.net`_ 或 _`www.contoso.com`_ 的流量。 針對其他所有專案，後端 X 應拒絕流量。
    - 同樣地，在後端 Y 上，您應該只接受標頭為「**X 轉送-主機**」的流量設定為 [ _`contoso.azurefd.net`_ ]、[ _`api.contoso.com`_ ] 或 [ _`notifications.contoso.com`_ ]。 針對其他所有專案，後端 Y 應該會拒絕流量。

### <a name="can-the-anycast-ip-change-over-the-lifetime-of-my-front-door"></a>可以在我的 Front 生命週期內變更任意傳播 IP 嗎？

前端的前端任意傳播 IP 通常不會變更，而且在 Front 的存留期間可能會保持靜態。 不過，**不保證**相同。 請不要對 IP 採取任何直接相依性。

### <a name="does-azure-front-door-service-support-static-or-dedicated-ips"></a>Azure 前端服務是否支援靜態或專用 Ip？

否，Azure Front 開門服務目前不支援靜態或專用的前端任意傳播 Ip。 

### <a name="does-azure-front-door-service-support-x-forwarded-for-headers"></a>Azure Front 門板服務是否支援 x 轉送-適用于標頭？

是，Azure Front 門板服務支援 X 轉送的、X 轉送的主機和 X 轉送的協定標頭。 針對 X 轉送-如果標頭已存在，則 Front 門會在其上附加用戶端通訊端 IP。 否則，它會以用戶端通訊端 IP 作為值來新增標頭。 若為 X 轉送主機和 X 轉送-Proto，則會覆寫值。

深入瞭解[Front 支援的 HTTP 標頭](front-door-http-headers-protocol.md)。  

### <a name="how-long-does-it-take-to-deploy-an-azure-front-door-service-does-my-front-door-still-work-when-being-updated"></a>部署 Azure Front 服務需要多久的時間？ 我的 Front 門在更新時仍可運作嗎？

新的 Front 門板建立或現有 Front 的任何更新約需要3到5分鐘的時間進行全域部署。 這表示在大約3到5分鐘內，您的 Front 門板設定會在全球所有的 Pop 上部署。

注意-自訂 SSL 憑證更新需要大約30分鐘的時間來進行全域部署。

## <a name="configuration"></a>組態

### <a name="can-azure-front-door-load-balance-or-route-traffic-within-a-virtual-network"></a>Azure Front 門板是否可以在虛擬網路內進行負載平衡或路由流量？

Azure Front 門板（AFD）需要公用 IP 或可公開解析的 DNS 名稱來路由傳送流量。 因此，答案無法直接在虛擬網路內進行路由傳送，但是在其間使用應用程式閘道或 Azure Load Balancer 會解決此案例。

### <a name="what-are-the-various-timeouts-and-limits-for-azure-front-door-service"></a>Azure Front 服務有哪些不同的超時和限制？

深入瞭解所有已記載的[Azure Front 開門服務的超時和限制](https://docs.microsoft.com/azure/azure-subscription-service-limits#azure-front-door-service-limits)。

## <a name="performance"></a>效能

### <a name="how-does-azure-front-door-service-support-high-availability-and-scalability"></a>Azure Front 門板服務如何支援高可用性和擴充性？

Azure Front 門板服務是全域散發的多租使用者平臺，具有大量容量，可滿足您應用程式的擴充性需求。 Front 門板是從 Microsoft 全球網路的邊緣提供的全域負載平衡功能，可讓您故障處理整個應用程式，甚至是跨區域或不同雲端的個別微服務。

## <a name="ssl-configuration"></a>SSL 設定

### <a name="what-tls-versions-are-supported-by-azure-front-door-service"></a>Azure Front 服務支援哪些 TLS 版本？

2019年9月之後建立的所有 Front 設定檔都使用 TLS 1.2 做為預設的最小值。

Front 門板支援 TLS 版本1.0、1.1 和1.2。 尚不支援 TLS 1.3。

### <a name="what-certificates-are-supported-on-azure-front-door-service"></a>Azure Front 服務支援哪些憑證？

若要啟用 HTTPS 通訊協定以在 Front 門板自訂網域上安全地傳遞內容，您可以選擇使用 Azure Front 服務所管理的憑證，或使用您自己的憑證。
「前端管理」選項會透過 Digicert 布建標準 SSL 憑證，並將其儲存在前門的 Key Vault 中。 如果您選擇使用自己的憑證，則可以從支援的 CA 上架憑證，而且可以是標準 SSL、擴充驗證憑證，甚至是萬用字元憑證。 不支援自我簽署憑證。 瞭解[如何為自訂網域啟用 HTTPS](https://aka.ms/FrontDoorCustomDomainHTTPS)。

### <a name="does-front-door-support-autorotation-of-certificates"></a>Front 門板是否支援憑證的 autorotation？

對於 [Front 門板受控憑證] 選項，憑證是由 Front 門 autorotated。 如果您使用的是 Front 的受控憑證，並看到憑證到期日少於60天，請提出支援票證。
</br>針對您自己的自訂 SSL 憑證，不支援 autorotation。 與第一次為指定的自訂網域設定的方式類似，您必須將 Front 大門指向您 Key Vault 中的正確憑證版本，並確定 Front 的服務主體仍然可以存取 Key Vault。 這個由 Front 門板更新的憑證推出作業是不可部分完成的，不會造成任何實際執行的影響，因為憑證的主體名稱或 SAN 不會變更。

### <a name="what-are-the-current-cipher-suites-supported-by-azure-front-door-service"></a>Azure Front Service 目前支援哪些加密套件？

以下是 Azure Front 服務所支援的目前加密套件：

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

### <a name="does-azure-front-door-service-also-support-re-encryption-of-traffic-to-the-backend"></a>Azure Front 開門服務是否也支援對後端的流量重新加密？

是，Azure Front 門板服務支援 SSL 卸載和端對端 SSL，這會重新加密後端的流量。 事實上，由於與後端的連線是透過其公用 IP 進行，因此建議您將 Front 門板設定為使用 HTTPS 做為轉送通訊協定。

### <a name="can-i-configure-ssl-policy-to-control-ssl-protocol-versions"></a>是否可設定 SSL 原則來控制 SSL 通訊協定版本嗎？

您可以透過[azure REST API](https://docs.microsoft.com/rest/api/frontdoorservice/frontdoor/frontdoors/createorupdate#minimumtlsversion)，在 azure Front 中設定最低的 TLS 版本。 目前，您可以選擇1.0 和1.2。

### <a name="can-i-configure-front-door-to-only-support-specific-cipher-suites"></a>我可以將 Front 門板設定為僅支援特定的加密套件嗎？

否，不支援為特定的加密套件設定 Front 門。 

## <a name="diagnostics-and-logging"></a>診斷和記錄

### <a name="what-types-of-metrics-and-logs-are-available-with-azure-front-door-service"></a>Azure Front 服務提供哪些類型的計量和記錄？

如需記錄和其他診斷功能的詳細資訊，請參閱[適用于 Front 的監視計量和記錄](front-door-diagnostics.md)。

### <a name="what-is-the-retention-policy-on-the-diagnostics-logs"></a>診斷記錄的保留原則為何？

診斷記錄會送至客戶儲存體帳戶，而客戶可以根據其喜好來設定保留原則。 診斷記錄也可以傳送至事件中樞或 Azure 監視器記錄。 如需詳細資訊，請參閱[Azure Front 門板服務診斷](front-door-diagnostics.md)。

### <a name="how-do-i-get-audit-logs-for-azure-front-door-service"></a>如何? 取得 Azure Front 服務的審核記錄嗎？

Audit 記錄適用于 Azure Front Service。 在入口網站中，按一下您 Front 入口的功能表分頁中的 [**活動記錄**]，以存取 audit 記錄。 

### <a name="can-i-set-alerts-with-azure-front-door-service"></a>我可以使用 Azure Front 服務設定警示嗎？

是，Azure Front 開門服務支援警示。 您可以針對計量設定警示。 

## <a name="next-steps"></a>後續步驟

- 了解如何[建立 Front Door](quickstart-create-front-door.md)。
- 了解 [Front Door 的運作方式](front-door-routing-architecture.md)。
