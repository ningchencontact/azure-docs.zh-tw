---
title: Azure 應用程式閘道的常見問題集
description: 本頁提供 Azure 應用程式閘道相關常見問題的解答
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.workload: infrastructure-services
ms.date: 3/20/2019
ms.author: victorh
ms.openlocfilehash: c40f372d3574f940e475a6626f998adae37a6d61
ms.sourcegitcommit: 04716e13cc2ab69da57d61819da6cd5508f8c422
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/02/2019
ms.locfileid: "58851162"
---
# <a name="frequently-asked-questions-for-application-gateway"></a>應用程式閘道的常見問題集

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="general"></a>一般

### <a name="what-is-application-gateway"></a>什麼是應用程式閘道？

「Azure 應用程式閘道」是以服務方式提供的「應用程式傳遞控制器」(ADC)，可為您的應用程式提供各種第 7 層負載平衡功能。 它提供高度可用並可調整的服務，且完全由 Azure 管理。

### <a name="what-features-does-application-gateway-support"></a>應用程式閘道支援哪些功能？

「應用程式閘道」支援自動調整規模、SSL 卸載和端對端 SSL、「Web 應用程式防火牆」、以 Cookie 為依據的工作階段親和性、URL 路徑型路由，以及多站台裝載等功能。 如需完整的支援功能清單，請參閱[應用程式閘道簡介](application-gateway-introduction.md)。

### <a name="what-is-the-difference-between-application-gateway-and-azure-load-balancer"></a>應用程式閘道與 Azure Load Balancer 之間的差異為何？

應用程式閘道是第 7 層負載平衡器，這表示它適用於 web 流量 (HTTP/HTTPS/WebSocket/HTTP/2)。 它支援功能，例如 SSL 終止、以 cookie 為基礎的工作階段親和性，以及用於平衡流量負載的循環配置資源。 「負載平衡器」會平衡第 4 層 (TCP/UDP) 的流量負載。

### <a name="what-protocols-does-application-gateway-support"></a>應用程式閘道支援哪些通訊協定？

應用程式閘道支援 HTTP、HTTPS、HTTP/2 和 WebSocket。

### <a name="how-does-application-gateway-support-http2"></a>應用程式閘道如何支援 HTTP/2？

請參閱[HTTP/2 支援](https://docs.microsoft.com/azure/application-gateway/configuration-overview#http2-support)若要了解如何應用程式閘道支援 HTTP/2 通訊協定。

### <a name="what-resources-are-supported-today-as-part-of-backend-pool"></a>目前支援哪些資源做為後端集區的一部分？

請參閱[支援後端資源](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#backend-pool)若要了解應用程式閘道支援哪些資源。

### <a name="what-regions-is-the-service-available-in"></a>哪些區域提供此服務？

應用程式閘道適用於全域 Azure 的所有區域。 它也適用於 [Azure China 21Vianet](https://www.azure.cn/) 和 [Azure Government](https://azure.microsoft.com/overview/clouds/government/)

### <a name="is-this-a-dedicated-deployment-for-my-subscription-or-is-it-shared-across-customers"></a>這是我的訂用帳戶專用的部署，還是所有客戶共用？

應用程式閘道是您虛擬網路中專用的部署。

### <a name="is-http-https-redirection-supported"></a>是否支援 HTTP->HTTPS 重新導向？

支援重新導向。 若要深入了解，請參閱[應用程式閘道重新導向概觀](application-gateway-redirect-overview.md)。

### <a name="in-what-order-are-listeners-processed"></a>接聽程式的處理順序為何？

請參閱[順序處理接聽程式](https://docs.microsoft.com/azure/application-gateway/configuration-overview#order-of-processing-listeners)。

### <a name="where-do-i-find-application-gateways-ip-and-dns"></a>哪裡可找到應用程式閘道的 IP 和 DNS？

使用公用 IP 位址作為端點時，可以在入口網站中應用程式閘道的公用 IP 位址資源上或 [概觀] 頁面上找到此資訊。 如需內部 IP 位址，可以在 [概觀] 頁面上找到這項資訊。

### <a name="what-is-keep-alive-timeout-and-tcp-idle-timeout-setting-on-application-gateway"></a>应用程序网关上的 Keep-Alive 超时和 TCP 空闲超时的设置是什么？

V1 sku 的保持連線逾時是 120 秒。 v2 sku 的保持連線逾時是 75 秒。 TCP 閒置逾時是前端應用程式閘道的 VIP 上的 4 分鐘預設值。

### <a name="does-the-ip-or-dns-name-change-over-the-lifetime-of-the-application-gateway"></a>IP 或 DNS 名稱是否會在應用程式閘道的存留期內變更？

如果將應用程式閘道停止後又啟動，VIP 可能會變更。 與應用程式閘道相關聯的 DNS 名稱在閘道的存留期內不會變更。 基於這個理由，建議使用 CNAME 別名，並將它指向應用程式閘道的 DNS 位址。

### <a name="does-application-gateway-support-static-ip"></a>應用程式閘道是否支援靜態 IP？

是，應用程式閘道 v2 SKU 支援靜態公用 IP 位址。 v1 SKU 支援靜態內部 IP。

### <a name="does-application-gateway-support-multiple-public-ips-on-the-gateway"></a>應用程式閘道是否在閘道上支援多個公用 IP？

應用程式閘道上只支援一個公用 IP 位址。

### <a name="how-large-should-i-make-my-subnet-for-application-gateway"></a>應用程式閘道的子網路應該要多大？

請參閱[應用程式閘道子網路大小的考量](https://docs.microsoft.com/azure/application-gateway/configuration-overview#size-of-the-subnet)若要了解您的部署所需的子網路大小。

### <a name="q-can-i-deploy-more-than-one-application-gateway-resource-to-a-single-subnet"></a>問： 可以將多個應用程式閘道資源部署到單一子網路嗎？

可以，除了具有指定應用程式閘道部署的多個執行個體以外，您可以將另一個唯一的應用程式閘道資源佈建到含有不同應用程式閘道資源的現有子網路。

不支援在相同子網路上混合使用 Standard_v2 和標準應用程式閘道。

### <a name="does-application-gateway-support-x-forwarded-for-headers"></a>應用程式閘道是否支援 x-forwarded-for 標頭？

是。 請參閱[要求修改](https://docs.microsoft.com/azure/application-gateway/how-application-gateway-works#modifications-to-the-request)若要了解支援的應用程式閘道的 x 轉送的 」 標頭。

### <a name="how-long-does-it-take-to-deploy-an-application-gateway-does-my-application-gateway-still-work-when-being-updated"></a>部署應用程式閘道需要多久的時間？ 進行更新時，我的應用程式閘道是否仍有作用？

新的應用程式閘道 v1 SKU 部署最多可能需要 20 分鐘的時間來佈建。 執行個體大小/計數的變更不會產生干擾，而閘道會在這段期間保持作用中。

V2 SKU 部署可能需要 5 到 6 分鐘的時間來佈建。

### <a name="can-exchange-server-be-used-as-backend-with-application-gateway"></a>Exchange 伺服器可用來當做應用程式閘道的後端嗎？

否，應用程式閘道不支援電子郵件通訊協定，例如 SMTP、 IMAP 和 POP3。 

## <a name="performance"></a>效能

### <a name="how-does-application-gateway-support-high-availability-and-scalability"></a>應用程式閘道如何支援高可用性和延展性？

如果您已部署 2 個以上的執行個體，應用程式閘道 v1 SKU 便可支援高可用性案例。 Azure 會將這些執行個體分散於更新和容錯網域，確保所有執行個體不會同時失敗。 v1 SKU 可藉由新增多個相同閘道的執行個體來分攤負載，以支援延展性。

v2 SKU 會自動確保將新執行個體分散在各個容錯網域和更新網域。 如果已選擇區域備援，則也會將最新的執行個體分散在各個可用性區域，以提供區域失敗復原能力。

### <a name="how-do-i-achieve-dr-scenario-across-data-centers-with-application-gateway"></a>如何利用應用程式閘道跨越資料中心封存 DR 案例？

客戶可以使用流量管理員，將流量分散於不同資料中心內的多個應用程式閘道。

### <a name="is-autoscaling-supported"></a>是否支援自動調整規模？

是，應用程式閘道 v2 SKU 支援自動調整規模。 如需詳細資訊，請參閱[自動調整規模和區域備援應用程式閘道 (公開預覽)](application-gateway-autoscaling-zone-redundant.md)。

### <a name="does-manual-scale-updown-cause-downtime"></a>手動相應增加/相應減少會造成停機嗎？

不會停機。 執行個體已分散於升級網域和容錯網域。

### <a name="does-application-gateway-support-connection-draining"></a>應用程式閘道是否支援連線清空？

是。 您可以設定連線清空來變更後端集區內的成員而不會中斷運作。 這可讓現有的連線持續傳送到先前的目的地，直到該連線關閉或可設定的逾時過期。 連線清空只會等候目前執行中的連線完成。 應用程式閘道並不會知道應用程式的工作階段狀態。

### <a name="can-i-change-instance-size-from-medium-to-large-without-disruption"></a>是否可在中斷的情況下，將執行個體大小從中型變成大型？

是，Azure 會將執行個體分散於更新和容錯網域，確保所有執行個體不會同時失敗。 應用程式閘道支援調整的方式為藉由新增相同閘道的多個執行個體來分攤負載。

## <a name="configuration"></a>組態

### <a name="is-application-gateway-always-deployed-in-a-virtual-network"></a>應用程式閘道一律部署在虛擬網路中？

是，應用程式閘道一律部署在虛擬網路子網路中。 這個子網路只包含應用程式閘道。 請參閱[虛擬網路和子網路的需求](https://docs.microsoft.com/azure/application-gateway/configuration-overview#azure-virtual-network-and-dedicated-subnet)若要了解應用程式閘道子網路的考量。

### <a name="can-application-gateway-communicate-with-instances-outside-of-the-virtual-network-it-is-in-or-outside-of-the-subscription-it-is-in"></a>應用程式閘道可以與外部虛擬網路中或在訂用帳戶外的執行個體通訊？

應用程式閘道可以與在虛擬網路外部或，訂用帳戶外的執行個體通訊，只要 IP 連線能力。 如果您打算使用內部 IP 做為後端集區成員，則需要 [VNET 對等互連](../virtual-network/virtual-network-peering-overview.md)或 [VPN 閘道](../vpn-gateway/vpn-gateway-about-vpngateways.md)。

### <a name="can-i-deploy-anything-else-in-the-application-gateway-subnet"></a>是否可以在應用程式閘道子網路中部署其他任何項目？

否，但是您可以在子網路中部署其他應用程式閘道。

### <a name="are-network-security-groups-supported-on-the-application-gateway-subnet"></a>應用程式閘道子網路是否支援網路安全性群組？

請參閱[應用程式閘道子網路的網路安全性群組限制](https://docs.microsoft.com/azure/application-gateway/configuration-overview#network-security-groups-on-the-application-gateway-subnet)深入了解應用程式閘道子網路支援網路安全性群組。

### <a name="are-user-defined-routes-supported-on-the-application-gateway-subnet"></a>應用程式閘道子網路是否支援使用者定義路由？

請參閱[使用者定義的路由限制](https://docs.microsoft.com/azure/application-gateway/configuration-overview#user-defined-routes-supported-on-the-application-gateway-subnet)若要了解應用程式閘道子網路支援使用者定義的路由。

### <a name="what-are-the-limits-on-application-gateway-can-i-increase-these-limits"></a>應用程式閘道的限制為何？ 是否可以增加這些限制？

請參閱[應用程式閘道限制](../azure-subscription-service-limits.md#application-gateway-limits)來檢視限制。

### <a name="can-i-use-application-gateway-for-both-external-and-internal-traffic-simultaneously"></a>是否可以同時對外部和內部流量使用應用程式閘道？

是，「應用程式閘道」支援每個應用程式閘道擁有一個內部 IP 和一個外部 IP。

### <a name="is-vnet-peering-supported"></a>是否支援 VNet 對等互連？

是，支援 VNet 對等互連，這對於平衡其他虛擬網路中的流量負載很有幫助。

### <a name="can-i-talk-to-on-premises-servers-when-they-are-connected-by-expressroute-or-vpn-tunnels"></a>我可與經由 ExpressRoute 或 VPN 通道連線的內部部署伺服器通訊嗎？

是，只有流量允許即可。

### <a name="can-i-have-one-backend-pool-serving-many-applications-on-different-ports"></a>是否可有一個為不同連接埠上的許多應用程式提供服務的後端集區？

支援微服務架構。 您必須進行多項 http 設定，以在不同的連接埠上探查。

### <a name="do-custom-probes-support-wildcardsregex-on-response-data"></a>自訂探查是否支援回應資料使用萬用字元/regex？

自訂探查不支援回應資料使用萬用字元或 regex。

### <a name="how-are-rules-processed"></a>規則的處理方式為何？

請參閱[順序處理規則](https://docs.microsoft.com/azure/application-gateway/configuration-overview#order-of-processing-rules)若要了解如何路由規則會在應用程式閘道中的程序。

### <a name="what-does-the-host-field-for-custom-probes-signify"></a>自訂探查的 [主機] 欄位表示什麼？

主機欄位指定探查要送達的名稱。 只有當應用程式閘道上設定多站台時適用，否則請使用 '127.0.0.1'。 此值與 VM 主機名稱不同，其格式為 \<protocol\>://\<host\>:\<port\>\<path\>。

### <a name="can-i-whitelist-application-gateway-access-to-a-few-source-ips"></a>我可以將一些來源 IP 的應用程式閘道存取列入允許清單嗎？

是。 請參閱[限制存取特定的來源 Ip](https://docs.microsoft.com/azure/application-gateway/configuration-overview#whitelist-application-gateway-access-to-a-few-source-ips)若要了解如何確保該只有列入允許清單的來源 Ip 可以存取應用程式閘道。

### <a name="can-the-same-port-be-used-for-both-public-and-private-facing-listeners"></a>是否可以在面對公眾和面對私人的接聽程式使用同一個連接埠？

不行，不支援此方式。

## <a name="configuration---ssl"></a>設定-SSL

### <a name="what-certificates-are-supported-on-application-gateway"></a>應用程式閘道支援哪些憑證？

支持自签名证书、CA 证书、EV 证书和通配符证书。

### <a name="what-are-the-current-cipher-suites-supported-by-application-gateway"></a>應用程式閘道目前支援哪些加密套件？

以下是「應用程式閘道」目前支援的加密套件。 若要了解如何自訂 SSL 選項，請參閱[在應用程式閘道上設定 SSL 原則版本和加密套件](application-gateway-configure-ssl-policy-powershell.md)。

- TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA
- TLS_DHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_DHE_RSA_WITH_AES_256_CBC_SHA
- TLS_DHE_RSA_WITH_AES_128_CBC_SHA
- TLS_RSA_WITH_AES_256_GCM_SHA384
- TLS_RSA_WITH_AES_128_GCM_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA256
- TLS_RSA_WITH_AES_128_CBC_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA
- TLS_RSA_WITH_AES_128_CBC_SHA
- TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA
- TLS_DHE_DSS_WITH_AES_256_CBC_SHA256
- TLS_DHE_DSS_WITH_AES_128_CBC_SHA256
- TLS_DHE_DSS_WITH_AES_256_CBC_SHA
- TLS_DHE_DSS_WITH_AES_128_CBC_SHA
- TLS_RSA_WITH_3DES_EDE_CBC_SHA
- TLS_DHE_DSS_WITH_3DES_EDE_CBC_SHA

### <a name="does-application-gateway-also-support-re-encryption-of-traffic-to-the-backend"></a>應用程式閘道是否也支援後端流量的重新加密？

是，應用程式閘道支援 SSL 卸載，以及可重新加密後端流量的端對端 SSL。

### <a name="can-i-configure-ssl-policy-to-control-ssl-protocol-versions"></a>是否可設定 SSL 原則來控制 SSL 通訊協定版本嗎？

是，您可以將應用程式閘道設定為拒絕 TLS1.0、TLS1.1 和 TLS1.2。 SSL 2.0 和 3.0 都已預設停用並，無法加以設定。

### <a name="can-i-configure-cipher-suites-and-policy-order"></a>可以設定加密套件和原則的順序嗎？

是，支援[加密套件的設定](application-gateway-ssl-policy-overview.md)。 定義自訂原則時，必須啟用至少一個下列的加密套件。 應用程式閘道使用 SHA256 進行後端管理。

* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 
* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
* TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
* TLS_RSA_WITH_AES_128_GCM_SHA256
* TLS_RSA_WITH_AES_256_CBC_SHA256
* TLS_RSA_WITH_AES_128_CBC_SHA256

### <a name="how-many-ssl-certificates-are-supported"></a>支援多少個 SSL 憑證？

最多支援 100 個 SSL 憑證。

### <a name="how-many-authentication-certificates-for-backend-re-encryption-are-supported"></a>針對後端重新加密支援多少個驗證憑證？

最多支援 10 個驗證憑證 (預設值為 5)。

### <a name="does-application-gateway-integrate-with-azure-key-vault-natively"></a>應用程式閘道是否原本就與 Azure Key Vault 整合？

不，它並未與 Azure Key Vault 整合。

### <a name="how-to-configure-https-listeners-for-com-and-net-sites"></a>如何設定 HTTPS 接聽程式的.com 與.net 的網站？ 

多個網域 （主機型） 路由，您可以建立多站台接聽程式、 選擇 HTTPS 接聽程式組態中的通訊協定和路由規則相關聯的接聽程式。 如需詳細資訊，請參閱 <<c0> [ 裝載應用程式閘道的多個站台](https://docs.microsoft.com/azure/application-gateway/multiple-site-overview)。 

## <a name="configuration---web-application-firewall-waf"></a>設定-Web 應用程式防火牆 (WAF)

### <a name="does-the-waf-sku-offer-all-the-features-available-with-the-standard-sku"></a>WAF SKU 是否提供適用於標準 SKU 的所有功能？

是，WAF 支援標準 SKU 中的所有功能。

### <a name="what-is-the-crs-version-application-gateway-supports"></a>應用程式閘道支援的 CRS 版本為何？

應用程式閘道支援 CRS [2.2.9](application-gateway-crs-rulegroups-rules.md#owasp229) 和 CRS [3.0](application-gateway-crs-rulegroups-rules.md#owasp30)。

### <a name="how-do-i-monitor-waf"></a>如何監視 WAF？

WAF 是透過診斷記錄功能來監視，如需診斷記錄的詳細資訊，請參閱[應用程式閘道的診斷記錄和計量](application-gateway-diagnostics.md)

### <a name="does-detection-mode-block-traffic"></a>偵測模式是否會封鎖流量？

否，偵測模式只會記錄觸發 WAF 規則的流量。

### <a name="can-i-customize-waf-rules"></a>我可以自訂 WAF 規則嗎？

是，WAF 規則是可自訂的。 如需詳細資訊，請參閱[自訂 WAF 規則群組與規則](application-gateway-customize-waf-rules-portal.md)

### <a name="what-rules-are-currently-available"></a>目前可使用哪些規則？

WAF 目前支援 CRS [2.2.9](application-gateway-crs-rulegroups-rules.md#owasp229) 和 [3.0](application-gateway-crs-rulegroups-rules.md#owasp30)，其針對 Open Web Application Security Project (OWASP) 所識別的大多數前 10 大弱點 (請參閱 [OWASP 的前 10 大弱點 (英文)](https://www.owasp.org/index.php/Top10#OWASP_Top_10_for_2013)) 提供基準安全性

* SQL 注入保护

* 跨網站指令碼保護

* 常见 Web 攻击保护，例如命令注入、HTTP 请求走私、HTTP 响应拆分和远程文件包含攻击

* 防範 HTTP 通訊協定違規

* 防止 HTTP 协议异常行为，例如缺少主机用户代理和接受标头

* 防範 Bot、編目程式和掃描器

* 偵測一般應用程式錯誤組態 (也就是 Apache、IIS 等)

### <a name="does-waf-also-support-ddos-prevention"></a>WAF 是否也支援 DDoS 預防？

是。 您可以在部署應用程式閘道的 VNet 上，啟用 DDos 保護。 這可確保使用 Azure DDoS 保護服務保護應用程式閘道 VIP。

## <a name="diagnostics-and-logging"></a>診斷和記錄

### <a name="what-types-of-logs-are-available-with-application-gateway"></a>應用程式閘道可使用哪些記錄類型？

應用程式閘道可使用三種記錄。 如需有關這些記錄及其他診斷功能的詳細資訊，請參閱[應用程式閘道的後端健康情況、診斷記錄和計量](application-gateway-diagnostics.md)。

* **ApplicationGatewayAccessLog** - 此存取記錄包含提交給應用程式閘道前端的每個要求。 此資料包含呼叫者的 IP、所要求的 URL、回應延遲、傳回碼、輸入和輸出位元組。每隔 300 秒會收集一次存取記錄檔。 此記錄會針對每一應用程式閘道執行個體包含一筆記錄。
* **ApplicationGatewayPerformanceLog** - 此效能記錄會擷取每個執行個體的效能資訊，包括提供的要求總數、輸送量 (以位元組為單位)、提供的總要求數、失敗的要求計數、狀況良好和狀況不良的後端執行個體計數。
* **ApplicationGatewayFirewallLog** - 此防火牆記錄包含透過應用程式閘道的偵測或防護模式 (依 Web 應用程式防火牆的設定) 所記錄的要求。

### <a name="how-do-i-know-if-my-backend-pool-members-are-healthy"></a>如何知道我的後端集區成員狀態良好？

您可以藉由造訪[應用程式閘道診斷](application-gateway-diagnostics.md)，使用 PowerShell Cmdlet `Get-AzApplicationGatewayBackendHealth` 或透過入口網站驗證健康狀態

### <a name="what-is-the-retention-policy-on-the-diagnostics-logs"></a>診斷記錄的保留原則為何？

診斷記錄會送至客戶儲存體帳戶，而客戶可以根據其喜好來設定保留原則。 診斷記錄也可以傳送至事件中樞或 Azure 監視器記錄。 如需詳細資料，請參閱[應用程式閘道診斷](application-gateway-diagnostics.md)。

### <a name="how-do-i-get-audit-logs-for-application-gateway"></a>如何取得應用程式閘道的稽核記錄？

稽核記錄可用於應用程式閘道。 在入口網站中，按一下**活動記錄檔**應用程式閘道來存取稽核記錄檔的功能表刀鋒視窗上。 

### <a name="can-i-set-alerts-with-application-gateway"></a>是否可以設定應用程式閘道的警示？

是，「應用程式閘道」支援警示。 您可以針對計量設定警示。 若要深入了解「應用程式閘道」計量，請參閱[應用程式閘道計量](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics#metrics)。 若要深入了解警示，請參閱[接收警示通知](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)。

### <a name="how-do-i-analyze-traffic-statistics-for-application-gateway"></a>如何分析應用程式閘道的流量統計資料？

您可以透過一些機制 (例如 Azure 監視器記錄、Excel、Power BI 等)，檢視及分析存取記錄。

我們也發佈會安裝並執行常用 [GoAccess](https://goaccess.io/) 記錄分析器的 Resource Manager 範本，該分析器適用於應用程式閘道存取記錄。 GoAccess 提供實用的 HTTP 流量統計資料，例如非重複訪客、要求的檔案、主機、作業系統、瀏覽器、HTTP 狀態碼等等。 如需詳細資訊，請參閱 [GitHub 中 Resource Manager 範本資料夾中的讀我檔案](https://aka.ms/appgwgoaccessreadme)。

### <a name="backend-health-returns-unknown-status-what-could-be-causing-this-status"></a>後端健康情況傳回不明狀態，什麼導致這個狀態？

最常見的原因是存取後端會封鎖 NSG，自訂的 DNS，或您有應用程式閘道子網路的 UDR。 若要深入了解，請參閱[應用程式閘道的後端健康情況、診斷記錄和計量](application-gateway-diagnostics.md)。

## <a name="next-steps"></a>後續步驟

若要深入了解「應用程式閘道」，請參閱[什麼是 Azure 應用程式閘道？](overview.md)
