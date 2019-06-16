---
title: 應用程式閘道的元件
description: 本文提供的應用程式閘道中的各種元件的相關資訊
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 02/20/2019
ms.author: absha
ms.openlocfilehash: f5dfa34760bcef23bf54d65b35e3ad8f48cc2ee5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "60831824"
---
# <a name="application-gateway-components"></a>應用程式閘道的元件

 應用程式閘道作為單一的用戶端的連絡點。 它會將應用程式的連入流量分散到多個後端集區，包括 Azure Vm、 虛擬機器擴展集、 Azure App Service，以及在內部部署/外部伺服器中。 若要將流量分散，應用程式閘道會使用這篇文章中所述的數個元件。

![中的應用程式閘道使用的元件](./media/application-gateway-components/application-gateway-components.png)

## <a name="frontend-ip-addresses"></a>前端 IP 位址

前端 IP 位址是與應用程式閘道相關聯的 IP 位址。 您可以設定應用程式閘道有公用 IP 位址、 私人 IP 位址，或兩者。 應用程式閘道支援一個公用或私人 IP 位址。 您的虛擬網路和公用 IP 位址必須位於與您的應用程式閘道相同的位置。 在建立之後，前端 IP 位址是接聽程式相關聯。

### <a name="static-versus-dynamic-public-ip-address"></a>靜態與動態公用 IP 位址

Azure 應用程式閘道 v2 SKU 支援這兩個靜態內部和靜態公用 IP 位址，雖然 v1 SKU 支援的只有靜態內部 IP 位址。 如果停止並啟動應用程式閘道，可以變更虛擬 IP (VIP) 位址。

應用程式閘道相關聯的 DNS 名稱不會變更的生命週期內的閘道。 如此一來，您應該使用 CNAME 別名，並指向應用程式閘道的 DNS 位址。

## <a name="listeners"></a>接聽程式

接聽程式是一種邏輯實體，以檢查內送連接要求。 如果通訊協定、 連接埠、 主機和要求相關聯的 IP 位址符合相同接聽程式組態相關聯的項目，接聽程式會接受要求。

您使用的應用程式閘道之前，您必須新增至少一個接聽程式。 可以有多個接聽程式連接至應用程式閘道，並可以用於相同的通訊協定。

接聽程式偵測到來自用戶端的連入要求之後，應用程式閘道會將這些要求路由到後端集區中的成員。 應用程式閘道會使用定義接聽程式接收內送要求的要求路由規則。

接聽程式支援下列的連接埠和通訊協定。

### <a name="ports"></a>連接埠

連接埠是接聽程式接聽用戶端要求的位置。 您可以設定連接埠範圍從 1 到 65502 v1 sku 和 1 至 65199 v2 sku。

### <a name="protocols"></a>通訊協定

應用程式閘道支援四種通訊協定：HTTP、 HTTPS、 HTTP/2 和 WebSocket:

- 指定 HTTP 和 HTTPS 通訊協定接聽程式組態中。
- 支援[Websocket 及 HTTP/2 通訊協定](https://docs.microsoft.com/azure/application-gateway/overview#websocket-and-http2-traffic)原生，提供並[WebSocket 支援](https://docs.microsoft.com/azure/application-gateway/application-gateway-websocket)預設會啟用。 使用者無法進行設定來選擇要啟用或停用 WebSocket 支援。 使用 HTTP 和 HTTPS 接聽程式使用 Websocket。
- HTTP/2 通訊協定支援僅適用於連線到應用程式閘道接聽程式的用戶端。 與後端伺服器集區的通訊是透過 HTTP/1.1 進行的。 預設已停用 HTTP/2 支援。 您可以選擇啟用它。

使用 SSL 終止的 HTTPS 接聽程式。 HTTPS 接聽程式會卸載應用程式閘道的加密和解密工作，讓您的 web 伺服器不負擔的額外負荷。 您的應用程式便可以專注於商務邏輯。

### <a name="custom-error-pages"></a>自訂錯誤頁面

應用程式閘道可讓您建立自訂錯誤網頁，而不是顯示預設錯誤網頁。 您可以使用自訂錯誤頁面來搭配您自己的商標和版面配置。 要求無法連線到後端時，應用程式閘道就會顯示自訂錯誤頁面。

如需詳細資訊，請參閱 <<c0> [ 應用程式閘道的自訂錯誤網頁](https://docs.microsoft.com/azure/application-gateway/custom-error)。

### <a name="types-of-listeners"></a>類型的接聽程式

有兩種類型的接聽程式：

- **基本**。 這種類型的接聽程式接聽單一網域的站台，其單一的 DNS 對應至應用程式閘道的 IP 位址。 裝載應用程式閘道的單一站台時，需要此接聽程式組態。

- **多站台**。 當您在相同的應用程式閘道執行個體上設定一個以上的 web 應用程式時需要此接聽程式組態。 它可讓您藉由將一個應用程式閘道最多 100 個網站設定為您的部署更有效率的拓撲。 每個網站都可以導向到自己的後端集區。 比方說，三個的子網域、 abc.contoso.com、 xyz.contoso.com，與 pqr.contoso.com，端點的應用程式閘道的 IP 位址。 您會建立三個多站台接聽程式，並設定個別的連接埠和通訊協定設定的每個接聽程式。

    如需詳細資訊，請參閱 <<c0> [ 多個站台裝載](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-app-overview)。

建立接聽程式之後，您會將其與要求路由規則。 此規則會判斷如何接聽程式上收到的要求應該會路由傳送至後端。

應用程式閘道會處理接聽程式在顯示的順序。 如果基本接聽程式符合傳入的要求，則會在第一次處理。 若要將流量路由至正確的後端，設定基本接聽程式之前的多站台接聽程式。

## <a name="request-routing-rules"></a>要求路由規則

要求的路由規則會是應用程式閘道的重要元件，因為它會決定如何將流量路由傳送接聽程式上。 規則繫結接聽程式、 後端伺服器集區和後端 HTTP 設定。

當接聽程式接受要求時，要求路由規則將要求轉寄到後端，或將它重新導向其他地方。 如果將要求轉送至後端，要求路由規則會定義將轉送到哪個後端伺服器集區。 此外，要求路由規則也會決定是否要重寫中要求的標頭。 一個接聽程式可以附加至一個規則。

有兩種類型的要求路由規則：

- **基本**。 相關聯的接聽程式 (例如 blog.contoso.com/*) 上的所有要求都會都轉送到相關聯的後端集區中，使用相關聯的 HTTP 設定。

- **路徑型**。 此路由的規則可讓您將要求路由至特定後端集區，根據在要求中的 URL 相關聯的接聽程式。 如果在要求 URL 的路徑符合路徑型規則中的路徑模式，此規則會將該要求路由。 它適用於路徑模式只能與 URL 路徑，而非其查詢參數。 如果接聽程式要求的 URL 路徑不符合任何路徑型規則，它會將要求路由的預設後端集區和 HTTP 設定中。

如需詳細資訊，請參閱 < [URL 型路由](https://docs.microsoft.com/azure/application-gateway/url-route-overview)。

### <a name="redirection-support"></a>重新支援

要求路由規則也可讓您將應用程式閘道上的流量重新導向。 這是泛型重新導向機制，讓您可以重新導向至和您定義使用規則來從任何連接埠。

您可以選擇另一個接聽程式 （這可協助啟用自動 HTTP 至 HTTPS 重新導向） 或外部網站的重新導向目標。 您也可以選擇要暫時或永久重新導向，或將 URI 路徑和查詢字串附加至重新導向的 URL。

如需詳細資訊，請參閱 <<c0> [ 將您的應用程式閘道上的流量重新導向](https://docs.microsoft.com/azure/application-gateway/redirect-overview)。

### <a name="rewrite-http-headers"></a>重新撰寫 HTTP 標頭

藉由使用將要求路由規則，您可以新增、 移除或更新 HTTP (S) 要求和回應標頭的要求和回應封包之間移動的用戶端和後端集區透過應用程式閘道。

標頭可以設定為靜態值或其他標頭和伺服器變數。 這有助於與重要使用案例，例如擷取用戶端 IP 位址，移除機密資訊的後端中，然後再新增更多安全性，依此類推。

如需詳細資訊，請參閱 <<c0> [ 重寫的 HTTP 標頭，您的應用程式閘道上](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers)。

## <a name="http-settings"></a>HTTP 設定

應用程式閘道會路由傳送流量 （要求路由規則，包括 HTTP 設定中所指定） 的後端伺服器所使用的連接埠號碼、 通訊協定，以及其他設定此元件所述。

連接埠和 HTTP 設定中使用的通訊協定決定應用程式閘道和後端伺服器之間的流量是否已加密 （提供端對端 SSL），或未加密。

此元件也會用來：

- 判斷使用者工作階段是否使用相同的伺服器上保持[cookie 型工作階段親和性](https://docs.microsoft.com/azure/application-gateway/overview#session-affinity)。

- 使用依正常程序移除後端集區成員[連線清空](https://docs.microsoft.com/azure/application-gateway/overview#connection-draining)。

- 建立自訂探查監視後端健康狀態、 設定要求的逾時間隔、 覆寫主機名稱和路徑，在要求中，並提供單鍵讓您輕鬆指定設定 App Service 後端的關聯。

## <a name="backend-pools"></a>後端集區

後端集區會將要求路由至後端伺服器，處理要求。 後端集區可以包含：

- NIC
- 虛擬機器擴展集
- 公用 IP 位址
- 內部 IP 位址
- FQDN
- 多租用戶後端 （例如應用程式服務）

應用程式閘道後端集區成員未繫結至可用性設定組。 應用程式閘道可以位於虛擬網路外部的執行個體進行通訊。 如此一來後, 端集區的成員可以是跨越叢集、 跨資料中心或 Azure 外部，只要沒有 IP 連線能力。

如果您使用內部 Ip 做為後端集區成員時，您必須使用[虛擬網路對等互連](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)或是[VPN 閘道](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)。 虛擬網路對等互連是在其他虛擬網路的支援和有利於負載平衡流量。

應用程式閘道也可以進行通訊與內部部署伺服器如果允許流量連經由 Azure ExpressRoute 或 VPN 通道。

您可以建立不同的後端集區，針對不同類型的要求。 例如，建立一個後端集區，對於一般的要求，並要求您的應用程式的微服務的另一個後端集區。

## <a name="health-probes"></a>健康狀態探查

根據預設，應用程式閘道會監視其後端集區中的所有資源的健康狀態，而且會自動移除 狀況不良的項目。 它則監視狀況不良的執行個體，並將它們新增回狀況良好的後端集區中，當它們變成可用，並回應健康情況探查。

除了使用預設的健全狀況探查監視，您也可以自訂健全狀況探查，以符合應用程式的需求。 自訂探查可讓您更細微地控制健全狀況監視。 使用自訂探查時，您可以設定探查間隔、 URL 和路徑，若要測試，以及可接受的後端集區執行個體標示為狀況不良之前的失敗的回應次數。 我們建議您設定自訂探查來監視每個後端集區的健全狀況。

如需詳細資訊，請參閱 <<c0> [ 監視您的應用程式閘道的健康情況](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview)。

## <a name="next-steps"></a>後續步驟

建立應用程式閘道：

* [在 Azure 入口網站中](quick-create-portal.md)
* [使用 Azure PowerShell](quick-create-powershell.md)
* [使用 Azure CLI](quick-create-cli.md)
