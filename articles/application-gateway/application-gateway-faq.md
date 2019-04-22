---
title: 關於 Azure 應用程式閘道的常見問題集
description: 尋找有關 Azure 應用程式閘道常見問題的解答。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.workload: infrastructure-services
ms.date: 3/20/2019
ms.author: victorh
ms.openlocfilehash: 3c8a2fe9f4486fe4d33754b58f4e7ebec1b3252d
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/17/2019
ms.locfileid: "59682945"
---
# <a name="frequently-asked-questions-about-application-gateway"></a>關於應用程式閘道的常見問題集

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="general"></a>一般

### <a name="what-is-application-gateway"></a>什麼是應用程式閘道？

Azure 應用程式閘道會以服務形式提供應用程式傳遞控制器 (ADC)。 它提供各種第 7 層負載平衡功能的應用程式。 此服務是高度可用、 可擴充且完全受控的 Azure。

### <a name="what-features-does-application-gateway-support"></a>應用程式閘道支援哪些功能？

應用程式閘道支援自動調整、 SSL 卸載，和端對端 SSL、 web 應用程式防火牆 (WAF)、 cookie 型工作階段同質、 URL 路徑型路由、 多站台裝載及其他功能。 如需完整的支援功能清單，請參閱[應用程式閘道簡介](application-gateway-introduction.md)。

### <a name="how-do-application-gateway-and-azure-load-balancer-differ"></a>應用程式閘道和 Azure Load Balancer 有何不同？

應用程式閘道是第 7 層負載平衡器，這表示它只適用於 web 流量 （HTTP、 HTTPS、 WebSocket 和 HTTP/2）。 它支援功能，例如 SSL 終止、 cookie 型工作階段親和性及循環配置資源負載平衡流量。 流量進行負載平衡器平衡負載 4 層 （TCP 或 UDP）。

### <a name="what-protocols-does-application-gateway-support"></a>應用程式閘道支援哪些通訊協定？

應用程式閘道支援 HTTP、HTTPS、HTTP/2 和 WebSocket。

### <a name="how-does-application-gateway-support-http2"></a>應用程式閘道如何支援 HTTP/2？

請參閱[HTTP/2 支援](https://docs.microsoft.com/azure/application-gateway/configuration-overview#http2-support)。

### <a name="what-resources-are-supported-as-part-of-a-backend-pool"></a>支援哪些資源做為後端集區的一部分？

請參閱[支援後端資源](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#backend-pool)。

### <a name="in-what-regions-is-application-gateway-available"></a>在哪些區域可應用程式閘道？

應用程式閘道適用於全域 Azure 的所有區域。 它也會提供[Azure 中國 21Vianet](https://www.azure.cn/)並[Azure Government](https://azure.microsoft.com/overview/clouds/government/)。

### <a name="is-this-deployment-dedicated-for-my-subscription-or-is-it-shared-across-customers"></a>就此部署會專供我的訂用帳戶，或為所有客戶共用嗎？

應用程式閘道是您虛擬網路中專用的部署。

### <a name="does-application-gateway-support-http-to-https-redirection"></a>應用程式閘道支援 HTTP 至 HTTPS 重新導向功能？

支援重新導向。 請參閱[應用程式閘道重新導向概觀](application-gateway-redirect-overview.md)。

### <a name="in-what-order-are-listeners-processed"></a>接聽程式的處理順序為何？

請參閱[接聽程式處理的順序](https://docs.microsoft.com/azure/application-gateway/configuration-overview#order-of-processing-listeners)。

### <a name="where-do-i-find-the-application-gateway-ip-and-dns"></a>我在哪裡找到應用程式閘道 IP 和 DNS？

如果您使用公用 IP 位址作為端點，您會發現公用 IP 位址資源的 IP 和 DNS 資訊。 或者，在入口網站應用程式閘道的 [概觀] 頁面上找到它。 如果您使用內部 IP 位址，尋找 [概觀] 頁面上的資訊。

### <a name="what-are-the-settings-for-keep-alive-timeout-and-tcp-idle-timeout"></a>Keep Alive 逾時和 TCP 閒置逾時的設定有哪些？

 在應用程式閘道 v1 SKU 的 Keep Alive 逾時是 120 秒。 V2 SKU 的 Keep Alive 逾時是 75 秒。 TCP 閒置逾時是應用程式閘道的前端虛擬 IP (VIP) 的 4 分鐘預設值。

### <a name="does-the-ip-or-dns-name-change-over-the-lifetime-of-the-application-gateway"></a>不會變更應用程式閘道的存留期間的 IP 或 DNS 名稱嗎？

如果您停止並啟動應用程式閘道，VIP 可能會變更。 但是，應用程式閘道相關聯的 DNS 名稱不會變更閘道的存留期。 因為 DNS 名稱不會變更，您應該使用 CNAME 別名，並指向應用程式閘道的 DNS 位址。

### <a name="does-application-gateway-support-static-ip"></a>應用程式閘道是否支援靜態 IP？

是，應用程式閘道 v2 SKU 支援靜態公用 IP 位址。 v1 SKU 支援靜態內部 IP。

### <a name="does-application-gateway-support-multiple-public-ips-on-the-gateway"></a>應用程式閘道是否在閘道上支援多個公用 IP？

應用程式閘道支援只有一個公用 IP 位址。

### <a name="how-large-should-i-make-my-subnet-for-application-gateway"></a>應用程式閘道的子網路應該要多大？

請參閱[應用程式閘道子網路大小的考量](https://docs.microsoft.com/azure/application-gateway/configuration-overview#size-of-the-subnet)。

### <a name="can-i-deploy-more-than-one-application-gateway-resource-to-a-single-subnet"></a>可以將多個應用程式閘道資源部署到單一子網路嗎？

是。 除了多個指定的應用程式閘道部署的執行個體，您可以佈建另一個現有子網路，其中包含不同的應用程式閘道資源的唯一應用程式閘道資源。

在單一子網路無法支援 Standard_v2 和標準的應用程式閘道在一起。

### <a name="does-application-gateway-support-x-forwarded-for-headers"></a>應用程式閘道是否支援 x-forwarded-for 標頭？

是。 請參閱[要求修改](https://docs.microsoft.com/azure/application-gateway/how-application-gateway-works#modifications-to-the-request)。

### <a name="how-long-does-it-take-to-deploy-an-application-gateway-will-my-application-gateway-work-while-its-being-updated"></a>部署應用程式閘道需要多久的時間？ 在更新時，我的應用程式閘道運作正常？

新的應用程式閘道 v1 SKU 部署最多可能需要 20 分鐘的時間來佈建。 執行個體大小或計數的變更不會干擾性，以及閘道會在這段期間維持使用中。

使用 v2 SKU 的部署可能需要長達 6 分鐘來佈建。

### <a name="can-i-use-exchange-server-as-a-backend-with-application-gateway"></a>可以使用 Exchange Server 做為後端應用程式閘道使用嗎？

沒有。 應用程式閘道不支援電子郵件通訊協定，例如 SMTP、 IMAP 和 POP3。 

## <a name="performance"></a>效能

### <a name="how-does-application-gateway-support-high-availability-and-scalability"></a>應用程式閘道如何支援高可用性和延展性？

當您部署了兩個或多個執行個體時，應用程式閘道 v1 SKU 支援高可用性案例。 Azure 會將這些執行個體會分散以確保執行個體不在同一時間的所有失敗的更新和容錯網域。 v1 SKU 可藉由新增多個相同閘道的執行個體來分攤負載，以支援延展性。

v2 SKU 會自動確保將新執行個體分散在各個容錯網域和更新網域。 如果您選擇的區域備援，最新的執行個體會也會散佈在可用性區域 」，以提供區域性失敗復原能力。

### <a name="how-do-i-achieve-a-dr-scenario-across-datacenters-by-using-application-gateway"></a>如何封存 DR 案例跨資料中心使用應用程式閘道？

使用流量管理員 」 將流量分散到不同資料中心內的多個應用程式閘道。

### <a name="does-application-gateway-support-autoscaling"></a>應用程式閘道是否支援自動調整？

是，應用程式閘道 v2 SKU 支援自動調整規模。 如需詳細資訊，請參閱 <<c0> [ 自動調整和區域備援應用程式閘道 （公開預覽）](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant)。

### <a name="does-manual-scale-up-or-scale-down-cause-downtime"></a>執行手動相應增加或相應減少會造成停機嗎？

沒有。 執行個體已分散於升級網域和容錯網域。

### <a name="does-application-gateway-support-connection-draining"></a>應用程式閘道是否支援連線清空？

是。 您可以設定連線清空變更不會中斷的後端集區中的成員。 此設定可讓您繼續傳送現有連線至其先前的目的地直到該連接會關閉，或可設定的逾時到期。 清空連線會等到只有目前的執行中連線完成。 應用程式閘道不會知道應用程式工作階段狀態。

### <a name="can-i-change-instance-size-from-medium-to-large-without-disruption"></a>是否可在中斷的情況下，將執行個體大小從中型變成大型？

是。 Azure 會將執行個體分散更新和容錯網域，以確保執行個體不會失敗，所有在相同的時間。 應用程式閘道支援調整的方式為藉由新增相同閘道的多個執行個體來分攤負載。

## <a name="configuration"></a>組態

### <a name="is-application-gateway-always-deployed-in-a-virtual-network"></a>應用程式閘道一律部署在虛擬網路中？

是。 應用程式閘道一律部署在虛擬網路子網路。 這個子網路可以只包含應用程式閘道。 如需詳細資訊，請參閱 <<c0> [ 虛擬網路和子網路的需求](https://docs.microsoft.com/azure/application-gateway/configuration-overview#azure-virtual-network-and-dedicated-subnet)。

### <a name="can-application-gateway-communicate-with-instances-outside-of-its-virtual-network-or-outside-of-its-subscription"></a>應用程式閘道可以與其虛擬網路外部或其訂用帳戶外的執行個體通訊？

只要具有 IP 連線能力時，應用程式閘道可以位於虛擬網路外部的執行個體通訊。 應用程式閘道也可以在訂用帳戶外的執行個體進行通訊。 如果您打算使用內部 Ip 做為後端集區成員時，使用[虛擬網路對等互連](../virtual-network/virtual-network-peering-overview.md)或是[Azure VPN 閘道](../vpn-gateway/vpn-gateway-about-vpngateways.md)。

### <a name="can-i-deploy-anything-else-in-the-application-gateway-subnet"></a>是否可以在應用程式閘道子網路中部署其他任何項目？

沒有。 但是，您可以部署其他應用程式閘道子網路中。

### <a name="are-network-security-groups-supported-on-the-application-gateway-subnet"></a>為支援的應用程式閘道子網路上的網路安全性群組？

請參閱[網路安全性群組中的應用程式閘道子網路](https://docs.microsoft.com/azure/application-gateway/configuration-overview#network-security-groups-on-the-application-gateway-subnet)。

### <a name="does-the-application-gateway-subnet-support-user-defined-routes"></a>應用程式閘道子網路是否支援使用者定義的路由？

請參閱[支援的應用程式閘道子網路中的使用者定義路由](https://docs.microsoft.com/azure/application-gateway/configuration-overview#user-defined-routes-supported-on-the-application-gateway-subnet)。

### <a name="what-are-the-limits-on-application-gateway-can-i-increase-these-limits"></a>應用程式閘道的限制為何？ 是否可以增加這些限制？

請參閱[應用程式閘道限制](../azure-subscription-service-limits.md#application-gateway-limits)。

### <a name="can-i-simultaneously-use-application-gateway-for-both-external-and-internal-traffic"></a>可以針對外部和內部流量同時使用應用程式閘道？

是。 應用程式閘道支援一個內部 IP 和每個應用程式閘道的一個外部 IP。

### <a name="does-application-gateway-support-virtual-network-peering"></a>支援虛擬網路對等應用程式閘道？

是。 虛擬網路對等互連可協助在其他虛擬網路的負載平衡流量。

### <a name="can-i-talk-to-on-premises-servers-when-theyre-connected-by-expressroute-or-vpn-tunnels"></a>我可與通訊內部部署伺服器連上經由 ExpressRoute 或 VPN 通道嗎？

是，只有流量允許即可。

### <a name="can-one-backend-pool-serve-many-applications-on-different-ports"></a>一個後端集區可以提供許多不同的連接埠上的應用程式嗎？

支援微服務架構。 若要在不同的連接埠上探查，您需要設定多個 HTTP 設定。

### <a name="do-custom-probes-support-wildcards-or-regex-on-response-data"></a>自訂探查是否支援使用萬用字元或 regex 回應資料？

沒有。 

### <a name="how-are-routing-rules-processed-in-application-gateway"></a>路由規則處理應用程式閘道中的方式為何？

請參閱[順序處理規則](https://docs.microsoft.com/azure/application-gateway/configuration-overview#order-of-processing-rules)。

### <a name="for-custom-probes-what-does-the-host-field-signify"></a>適用於自訂探查，哪些沒有 [主機] 欄位表示？

[主機] 欄位指定探查来送達時已設定多站台上的應用程式閘道的名稱。 否則，請使用 '127.0.0.1'。 這個值是虛擬機器主機名稱不同。 其格式是\<通訊協定\>://\<主機\>:\<連接埠\>\<路徑\>。

### <a name="can-i-whitelist-application-gateway-access-to-only-a-few-source-ips"></a>我可以將一些來源 Ip 的應用程式閘道存取的允許清單嗎？

是。 請參閱[限制存取特定的來源 Ip](https://docs.microsoft.com/azure/application-gateway/configuration-overview#whitelist-application-gateway-access-to-a-few-source-ips)。

### <a name="can-i-use-the-same-port-for-both-public-facing-and-private-facing-listeners"></a>可以使用相同的連接埠公開和私用對向的接聽程式嗎？

沒有。

## <a name="configuration---ssl"></a>設定-SSL

### <a name="what-certificates-does-application-gateway-support"></a>應用程式閘道支援哪些憑證？

應用程式閘道支援自我簽署的憑證、 憑證授權單位 (CA) 憑證、 驗證 (EV) 憑證和萬用字元憑證。

### <a name="what-cipher-suites-does-application-gateway-support"></a>何種加密套件沒有應用程式閘道支援？

應用程式閘道支援下列加密套件。 

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

如需如何自訂 SSL 選項的詳細資訊，請參閱[設定 SSL 原則版本和應用程式閘道上的加密套件](application-gateway-configure-ssl-policy-powershell.md)。

### <a name="does-application-gateway-support-reencryption-of-traffic-to-the-backend"></a>應用程式閘道是否支援後端流量重新加密？

是。 應用程式閘道支援 SSL 卸載和端對端 SSL 後, 端流量。

### <a name="can-i-configure-ssl-policy-to-control-ssl-protocol-versions"></a>可以設定 SSL 原則來控制 SSL 通訊協定版本嗎？

是。 您可以設定為拒絕 TLS1.0、 TLS1.1 和 TLS1.2 的應用程式閘道。 根據預設，SSL 2.0 和 3.0 都已停用，而不是可設定。

### <a name="can-i-configure-cipher-suites-and-policy-order"></a>可以設定加密套件和原則的順序嗎？

是。 您可以在應用程式閘道[設定的加密套件](application-gateway-ssl-policy-overview.md)。 若要定義自訂原則，請啟用至少一個下列加密套件。 

* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 
* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
* TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
* TLS_RSA_WITH_AES_128_GCM_SHA256
* TLS_RSA_WITH_AES_256_CBC_SHA256
* TLS_RSA_WITH_AES_128_CBC_SHA256

應用程式閘道會使用 SHA256 進行後端管理。

### <a name="how-many-ssl-certificates-does-application-gateway-support"></a>應用程式閘道支援多少個 SSL 憑證？

應用程式閘道支援最多 100 個 SSL 憑證。

### <a name="how-many-authentication-certificates-for-backend-reencryption-does-application-gateway-support"></a>應用程式閘道支援多少個驗證憑證的後端重新加密？

應用程式閘道支援最多 10 個驗證憑證。 預設值為 5。

### <a name="does-application-gateway-natively-integrate-with-azure-key-vault"></a>不會使用 Azure Key Vault 以原生方式整合應用程式閘道？

沒有。

### <a name="how-do-i-configure-https-listeners-for-com-and-net-sites"></a>如何設定 HTTPS 接聽程式的.com 與.net 的網站？ 

多個網域 （主機型） 路由，您可以建立多站台接聽程式、 設定使用 HTTPS 做為通訊協定的接聽項和接聽程式相關聯的路由規則。 如需詳細資訊，請參閱 <<c0> [ 使用應用程式閘道裝載多個站台](https://docs.microsoft.com/azure/application-gateway/multiple-site-overview)。 

## <a name="configuration---web-application-firewall-waf"></a>設定-web 應用程式防火牆 (WAF)

### <a name="does-the-waf-sku-offer-all-the-features-available-in-the-standard-sku"></a>WAF SKU 是否提供標準 SKU 中可用的所有功能？

是。 WAF 支援標準 SKU 中的所有功能。

### <a name="which-crs-versions-does-application-gateway-support"></a>應用程式閘道支援 CRS 版本有哪些？

應用程式閘道支援 CRS [2.2.9](application-gateway-crs-rulegroups-rules.md#owasp229) 和 CRS [3.0](application-gateway-crs-rulegroups-rules.md#owasp30)。

### <a name="how-do-i-monitor-waf"></a>如何監視 WAF？

透過診斷記錄的監視 WAF。 如需詳細資訊，請參閱 <<c0> [ 應用程式閘道的診斷記錄和度量](application-gateway-diagnostics.md)。

### <a name="does-detection-mode-block-traffic"></a>偵測模式是否會封鎖流量？

沒有。 偵測模式只會記錄觸發 WAF 規則的流量。

### <a name="can-i-customize-waf-rules"></a>我可以自訂 WAF 規則嗎？

是。 如需詳細資訊，請參閱 <<c0> [ 自訂 WAF 規則群組與規則](application-gateway-customize-waf-rules-portal.md)。

### <a name="what-rules-are-currently-available-for-waf"></a>Waf 目前提供哪些規則？

WAF 目前支援 CRS [2.2.9](application-gateway-crs-rulegroups-rules.md#owasp229)並[3.0](application-gateway-crs-rulegroups-rules.md#owasp30)。 這些規則可提供針對大部分的 Open Web Application Security Project (OWASP) 所識別的前 10 個弱點的基準安全性： 

* SQL 注入保护
* 跨網站指令碼保護
* 保護來抵禦常見 web 攻擊，例如命令插入、 HTTP 要求走私、 HTTP 回應分割和遠端檔案包含攻擊
* 防範 HTTP 通訊協定違規
* 防止 HTTP 协议异常行为，例如缺少主机用户代理和接受标头
* 防範 Bot、編目程式和掃描器
* 偵測一般應用程式錯誤組態 （也就是 Apache、 IIS 和等等）

如需詳細資訊，請參閱 < [OWASP 前 10 個弱點](https://www.owasp.org/index.php/Top10#OWASP_Top_10_for_2013)。

### <a name="does-waf-support-ddos-protection"></a>WAF 是否支援 DDoS 保護？

是。 您可以讓應用程式閘道部署所在的虛擬網路的 DDoS 保護。 此設定可確保 Azure DDoS 保護服務也會保護應用程式閘道虛擬 IP (VIP)。

## <a name="diagnostics-and-logging"></a>診斷和記錄

### <a name="what-types-of-logs-does-application-gateway-provide"></a>應用程式閘道提供哪些類型的記錄檔？

應用程式閘道提供三個記錄檔： 

* **ApplicationGatewayAccessLog**:存取記錄檔包含提交至應用程式閘道前端的每個要求。 資料會縮小和相應放大包含呼叫端的 IP、 要求 URL、 回應延遲、 傳回碼和位元組。存取記錄檔會收集每隔 300 秒。 它包含每個應用程式閘道的一筆記錄。
* **ApplicationGatewayPerformanceLog**:效能記錄檔會擷取每個應用程式閘道的效能資訊。 資訊包括輸送量，以位元組為單位，總計要求提供服務，失敗的要求計數，且狀況良好和狀況不良的後端執行個體計數。
* **ApplicationGatewayFirewallLog**:您設定具有 WAF 的應用程式閘道，此防火牆記錄包含透過偵測模式或防止模式所記錄的要求。

如需詳細資訊，請參閱 <<c0> [ 後端健康情況、 診斷記錄和計量應用程式閘道的](application-gateway-diagnostics.md)。

### <a name="how-do-i-know-if-my-backend-pool-members-are-healthy"></a>如何知道我的後端集區成員狀態良好？

使用 PowerShell cmdlet 來確認健康情況`Get-AzApplicationGatewayBackendHealth`或入口網站。 如需詳細資訊，請參閱[應用程式閘道診斷](application-gateway-diagnostics.md)。

### <a name="whats-the-retention-policy-for-the-diagnostic-logs"></a>診斷記錄的保留原則為何？

診斷記錄檔會送至客戶的儲存體帳戶。 客戶可以設定根據其喜好設定的保留原則。 診斷記錄也會傳送到事件中樞或 Azure 監視器記錄檔。 如需詳細資訊，請參閱[應用程式閘道診斷](application-gateway-diagnostics.md)。

### <a name="how-do-i-get-audit-logs-for-application-gateway"></a>如何取得應用程式閘道的稽核記錄？

在入口網站中，應用程式閘道，在功能表刀鋒視窗上選取**活動記錄檔**存取稽核記錄檔。 

### <a name="can-i-set-alerts-with-application-gateway"></a>是否可以設定應用程式閘道的警示？

是。 應用程式閘道中設定警示的計量。 如需詳細資訊，請參閱 <<c0> [ 應用程式閘道計量](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics#metrics)並[接收警示通知](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)。

### <a name="how-do-i-analyze-traffic-statistics-for-application-gateway"></a>如何分析應用程式閘道的流量統計資料？

您可以檢視及分析數種方式存取記錄檔。 使用 Azure 監視器記錄檔、 Excel、 Power BI，等等。

您也可以使用 Resource Manager 範本來安裝並執行熱門[GoAccess](https://goaccess.io/)記錄分析器應用程式閘道存取記錄檔。 GoAccess 提供寶貴的 HTTP 流量統計資料等非重複訪客、 要求的檔案、 主機、 作業系統、 瀏覽器中，HTTP 狀態碼。 如需詳細資訊，請在 GitHub 中，請參閱 < [Resource Manager 範本資料夾中的讀我檔案](https://aka.ms/appgwgoaccessreadme)。

### <a name="what-could-cause-backend-health-to-return-an-unknown-status"></a>何種狀況可能導致後端健康狀態傳回不明的狀態？

通常，您會看到不明的狀態時的網路安全性群組 (NSG)、 自訂的 DNS 或應用程式閘道子網路上的使用者定義路由 (UDR) 會封鎖對後端存取。 如需詳細資訊，請參閱 <<c0> [ 後端健康情況、 診斷記錄和計量應用程式閘道的](application-gateway-diagnostics.md)。

## <a name="next-steps"></a>後續步驟

若要深入了解應用程式閘道，請參閱[什麼是 Azure 應用程式閘道？](overview.md)。
