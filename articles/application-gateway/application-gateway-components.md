---
title: Azure 应用程序网关组件
description: 本文提供有关应用程序网关中各个组件的信息
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 02/20/2019
ms.author: absha
ms.openlocfilehash: 44c8b331ebb258c39a003c91e0711e6dfb87cb12
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/18/2019
ms.locfileid: "58076287"
---
# <a name="application-gateway-components"></a>应用程序网关组件

 应用程序网关充当客户端的单一联络点。 它在 Azure VM、虚拟机规模集、应用服务或本地/外部服务器等多个后端池之间分配传入的应用程序流量。 为此，它会使用本文所述的多个组件。

![application-gateway-components](./media/application-gateway-components/application-gateway-components.png)

## <a name="frontend-ip-address"></a>前端 IP 位址

前端 IP 地址是与应用程序网关关联的 IP 地址。 可将应用程序网关配置为使用公共 IP 地址和/或专用 IP 地址。 仅支持一个公共 IP 地址或一个专用 IP 地址。 虚拟网络和公共 IP 地址必须位于应用程序网关所在的同一位置。

创建一个前端 IP 地址后，该地址将关联到某个侦听器。 

### <a name="static-vs-dynamic-public-ip-address"></a>静态与动态公共 IP 地址

v1 SKU 支持静态内部 IP 地址，但不支持静态公共 IP 地址。 如果將應用程式閘道停止後又啟動，VIP 可能會變更。 與應用程式閘道相關聯的 DNS 名稱在閘道的存留期內不會變更。 基於這個理由，建議使用 CNAME 別名，並將它指向應用程式閘道的 DNS 位址。

應用程式閘道 v2 SKU 支援靜態公用 IP 位址，以及靜態內部 IP 位址。 

## <a name="listeners"></a>接聽程式

在开始使用应用程序网关之前，必须添加一个或多个侦听器。 侦听器是一个逻辑实体，它会检查传入的连接请求，如果与请求关联的协议、端口、主机和 IP 地址匹配与侦听器配置关联的协议、端口、主机和 IP 地址，则接受请求。 可将多个侦听器附加到一个应用程序网关，这些侦听器可用于同一个协议。 侦听器检测到来自客户端的传入请求后，应用程序网关会使用你为收到传入请求的侦听器所定义的请求路由规则，将这些请求路由到后端池中的成员。

侦听器支持以下端口和协议：

### <a name="ports"></a>連接埠

侦听器在此端口上侦听客户端请求。 您可以設定連接埠範圍從 1 到 65502 V1 SKU 和 1 至 65199 V2 sku。

### <a name="protocols"></a>通訊協定

应用程序网关支持以下四种协议：HTTP、HTTPS、HTTP/2 和 WebSocket

在侦听器配置中显式指定 HTTP 或 HTTPS 协议。 应用程序网关原生[支持 WebSocket 和 HTTP/2 协议](https://docs.microsoft.com/azure/application-gateway/overview#websocket-and-http2-traffic)。 默认已启用 [WebSocket 支持](https://docs.microsoft.com/azure/application-gateway/application-gateway-websocket)。 使用者無法進行設定來選擇要啟用或停用 WebSocket 支援。 可对 HTTP 和 HTTPS 侦听器使用 WebSocket。 HTTP/2 通訊協定支援僅適用於連線到應用程式閘道接聽程式的用戶端。 與後端伺服器集區的通訊是透過 HTTP/1.1 進行的。 預設已停用 HTTP/2 支援。 可以选择启用该协议。

可以使用 HTTPS 侦听器进行 SSL 终止。 HTTPS 侦听器可将加密和解密工作卸载到应用程序网关，以避免加密和解密开销给 Web 服务器造成负担。 然后，应用程序可以专注于其业务逻辑。

### <a name="custom-error-pages"></a>自訂錯誤頁面

应用程序网关允许你创建自定义错误页而非显示默认错误页。 您可以使用自訂錯誤頁面來搭配您自己的商標和版面配置。 應用程式閘道可在要求無法送達後端時顯示自訂錯誤頁面。 有关详细信息，请参阅[应用程序网关的自定义错误页](https://docs.microsoft.com/azure/application-gateway/custom-error)。

### <a name="types-of-listeners"></a>侦听器类型

有两种类型的侦听器：

- **基本**：此类侦听器侦听单个域站点，该站点中的单个 DNS 映射到应用程序网关的 IP 地址。 在应用程序网关后面托管单个站点时，需要使用此侦听器配置。
- **多站点**：在同一个应用程序网关实例上配置多个 Web 应用程序时，需要使用此侦听器配置。 这样可以将最多 100 个网站添加到一个应用程序网关，为部署配置更有效的拓扑。 每個網站都可以導向到自己的後端集區。 例如︰有三个子域（abc.alpha.com、xyz.alpha.com 和 pqr.alpha.com）指向应用程序网关 IP 地址。 可以创建三个“多站点”类型的侦听器，并为每个侦听器配置相应的端口和协议设置。 有关详细信息，请参阅[多站点托管](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-app-overview)。

创建侦听器后，将它与某个请求路由规则相关联。该规则确定如何将侦听器上收到的请求路由到后端。

接聽程式會依其顯示順序處理。 因此，如果基本侦听器与传入请求匹配，它会先处理该请求。 多站台接聽程式應在基本接聽程式之前設定，以確保流量會路由傳送到正確的後端。

## <a name="request-routing-rule"></a>要求路由規則

这是最重要的应用程序网关组件，它确定如何路由与此规则关联的侦听器上的流量。 该规则绑定侦听器、后端服务器池和后端 HTTP 设置。 侦听器接受请求后，请求路由规则会确定是要将该请求转发到后端还是重定向到其他位置。 如果确定将请求转发到后端，则请求路由规则会定义要转发到的后端服务器池。 此外，请求路由规则还确定是否要重写请求中的标头。 一个侦听器只能附加到一个规则。

可以使用两种类型的请求路由规则：

- **基本：** 使用关联的 HTTP 设置将关联的侦听器（例如 blog.contoso.com/*）上的所有请求转发到关联的后端池。
- **基于路径：** 此规则类型可让你根据请求中的 URL，将关联的侦听器上的请求路由到特定的后端池。 如果请求中的 URL 路径与基于路径的规则中的路径模式相匹配，则使用该规则路由请求。 路径模式仅应用到 URL 的路径，而不应用到该 URL 的查询参数。 如果侦听器上的请求 URL 路径与任何基于路径的规则都不匹配，则将请求路由到默认的后端池和默认的 HTTP 设置。 有关详细信息，请参阅[基于 URL 的路由](https://docs.microsoft.com/azure/application-gateway/url-route-overview)。

### <a name="redirection-support"></a>重新支援

请求路由规则还允许重定向应用程序网关上的流量。 這是一般重新導向機制，因此您可以從使用規則定義的任何連接埠重新導向，或是重新導向至使用規則定義的任何連接埠。 可以选择另一个侦听器（有助于实现 HTTP 到 HTTPS 的自动重定向）或外部站点作为重定向目标，选择临时性或永久性重定向，或者选择将 URI 路径和查询字符串追加到重定向的 URL。 有关详细信息，请参阅[重定向应用程序网关上的流量](https://docs.microsoft.com/azure/application-gateway/redirect-overview)。

#### <a name="rewrite-http-headers"></a>重新撰寫 HTTP 標頭

使用要求的路由規則可以新增、 移除或更新 HTTP (S) 要求和回應標頭而要求和回應封包用戶端與後端集區之間移動，透過應用程式閘道。 標頭不只能設定為靜態值，但也為其他標頭和重要的伺服器變數。 這將協助您完成數個重要的使用案例，例如擷取 IP 位址的用戶端，移除機密資訊的後端中，加入額外的安全性措施，依此類推。如需詳細資訊，請參閱 <<c0> [ 重寫的 HTTP 標頭，您的應用程式閘道上](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers)。

## <a name="http-settings"></a>HTTP 設定

应用程序网关使用此组件中指定的端口号、协议和其他设置，将流量路由到后端服务器（在 HTTP 设置附加到的请求路由规则中指定）。 HTTP 设置中使用的端口和协议确定应用程序网关与后端服务器之间的流量是要加密（从而可实现端到端的 SSL）还是不加密。 此组件还用于：使用[基于 Cookie 的会话相关性](https://docs.microsoft.com/azure/application-gateway/overview#session-affinity)确定是否要在同一台服务器上保留用户会话、使用[连接清空](https://docs.microsoft.com/azure/application-gateway/overview#connection-draining)完成后端池成员的正常删除、关联自定义探测以监视后端运行状况、设置请求超时间隔、替代请求中的主机名和路径，以及一键式指定应用服务后端的后端设置。 

## <a name="backend-pool"></a>後端集區

后端池用于将请求路由到为请求提供服务的后端服务器。 后端池可以包含 NIC、虚拟机规模集、公共 IP 地址、内部 IP 地址、FQDN 和多租户后端（例如 Azure 应用服务）。 应用程序网关后端池成员不会绑定到可用性集。 应用程序网关可与其所在虚拟网络外部的实例通信，因此，后端池的成员可以跨群集、数据中心，或者位于 Azure 外部，前提是建立了 IP 连接。 如果您打算使用內部 IP 做為後端集區成員，則需要 [VNET 對等互連](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)或 [VPN 閘道](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)。 支持 VNet 对等互连，这有助于对其他虚拟网络中的流量进行负载均衡。 此外，应用程序网关可与通过 ExpressRoute 或 VPN 隧道连接的本地服务器通信，只要允许这种流量即可。

可为不同类型的请求创建不同的后端池。 例如，为常规请求创建一个后端池，为发往应用程序微服务的请求创建另一个后端池。

## <a name="health-probes"></a>健康狀態探查

默认情况下，应用程序网关会监视其后端池中所有资源的运行状况，并自动从池中删除任何被视为不正常的资源。 它会持续监视不正常的实例，一旦这些实例恢复可用状态并能响应运行状况探测，应用程序网关就会将它们添加回正常的后端池中。 除了使用預設的健全狀況探查監視，您也可以自訂健全狀況探查，以符合應用程式的需求。 自訂探查可讓您更細微地控制健全狀況監視。 使用自定义探测时，可以配置探测间隔、要测试的 URL 和路径，以及在将后端池实例标记为不正常之前可接受的失败响应次数。 强烈建议配置自定义探测来监视每个后端池的运行状况。 有关详细信息，请参阅[监视应用程序网关的运行状况](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview)。

## <a name="next-steps"></a>後續步驟

了解应用程序网关组件后，可以：
* [在 Azure 门户中创建应用程序网关](quick-create-portal.md)
* [使用 Azure PowerShell 创建应用程序网关](quick-create-powershell.md)
* [使用 Azure CLI 创建应用程序网关](quick-create-cli.md)
