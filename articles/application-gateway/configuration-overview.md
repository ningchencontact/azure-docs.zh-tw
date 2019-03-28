---
title: Azure 應用程式閘道設定概觀
description: 本文章提供有關如何設定 Azure 應用程式閘道中的各種元件的資訊
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 03/20/2019
ms.author: absha
ms.openlocfilehash: ca4f9bf00d70f327ff756558e25315762a9a77a8
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2019
ms.locfileid: "58519743"
---
# <a name="application-gateway-configuration-overview"></a>應用程式閘道設定概觀

應用程式閘道包含可在不同的方式，完成不同的案例設定的數個元件。 這篇文章會引導您要設定的每個元件的方式。

![application-gateway-components](./media/configuration-overview/configuration-overview1.png)

上述範例映像會說明應用程式的 3 個接聽程式的組態。 兩個多站台接聽程式的第一次`http://acme.com/*`和`http://fabrikam.com/*`分別。 兩者都連接埠 80 上接聽。 第三個接聽程式是基本接聽程式進行端對端 SSL 終止。 

## <a name="prerequisites"></a>必要條件

### <a name="azure-virtual-network-and-dedicated-subnet"></a>Azure 虛擬網路和專用子網路

應用程式閘道是專用的部署，在您的虛擬網路中。 在您的虛擬網路內的專用子網路是必要應用程式閘道。 您可以在這個子網路的多個指定的應用程式閘道部署執行個體。 您也可以部署其他的應用程式閘道子網路中，但您無法部署應用程式閘道子網路中的任何其他資源。  

> [!NOTE]   
> 不支援在相同子網路上混合使用 Standard_v2 和標準應用程式閘道。

#### <a name="size-of-the-subnet"></a>子網路的大小

應用程式閘道會針對每個執行個體取用一個私人 IP 位址，如果已設定私人前端 IP 組態，則會再取用另一個私人 IP 位址。 此外，Azure 會保留五個 IP 位址-前四個和最後一個 IP 位址-供內部使用的每個子網路中。 例如，如果應用程式閘道設定為 15 個執行個體與不含私人前端 IP，然後至少 20 個 IP 位址必須在子網路： 用於內部用途的五個 IP 位址和 15 的 IP 位址，應用程式閘道的 15 個執行個體。 因此，以此案例而言是/27 子網路大小或更新版本需要。 如果您有 27 的執行個體和 IP 位址的私人前端 IP 組態，則 33 的 IP 位址將會需要 27 27 的執行個體的應用程式閘道的 IP 位址就會有一個 IP 位址的私人前端 IP 和五個 IP 位址供內部使用中。 因此，在此情況下 / 26 需要子網路大小或更高。

建議使用至少/28 子網路大小。 這可讓您 11 可用的位址。 如果您的應用程式的負載需要超過 10 個執行個體，您應該考慮/27 或/26 子網路大小。

#### <a name="network-security-groups-supported-on-the-application-gateway-subnet"></a>支援的應用程式閘道子網路上的網路安全性群組

網路安全性群組 (Nsg) 支援的應用程式閘道子網路，但有下列限制： 

- 必須針對應用程式閘道 v1 SKU 之連接埠 65503-65534 和 v2 SKU 之連接埠 65200 - 65535 上的傳入流量，設定例外。 Azure 基礎結構通訊需要此連接埠範圍。 它們受到 Azure 憑證的保護 (鎖定)。 若沒有適當的憑證，外部實體 (包括這些閘道的客戶) 將無法對這些端點起始任何變更。

- 無法封鎖輸出網際網路連線。 NSG 中的默认出站规则已经允许 Internet 连接。 建议不要删除默认的出站规则，且不要创建其他拒绝出站 Internet 连接的出站规则。

- 必須允許來自 AzureLoadBalancer 標籤的流量。

##### <a name="whitelist-application-gateway-access-to-a-few-source-ips"></a>將一些來源 Ip 允許清單應用程式閘道存取

在應用程式閘道子網路上使用 NSG 即可完成此案例。 應依照所列的優先順序在子網路施加下列限制：

1. 允許來源 IP/IP 範圍的傳入流量。
2. 允許從所有來源至連接埠 65503-65534 的傳入要求以便進行[後端健康情況通訊](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)。 Azure 基礎結構通訊需要此連接埠範圍。 它們受到 Azure 憑證的保護 (鎖定)。 若沒有適當的憑證，外部實體 (包括這些閘道的客戶) 將無法對這些端點起始任何變更。
3. 允許 [NSG](https://docs.microsoft.com/azure/virtual-network/security-overview) 上傳入的 Azure 負載平衡器探查 (AzureLoadBalancer 標籤) 和輸入虛擬網路流量 (VirtualNetwork 標籤)。
4. 使用「全部拒絕」規則封鎖所有其他傳入流量。
5. 允許所有目的地對網際網路的輸出流量。

#### <a name="user-defined-routes-supported-on-the-application-gateway-subnet"></a>使用者定義的路由應用程式閘道子網路支援

V1 SKU，如果使用者定義路由 (Udr) 都支援應用程式閘道子網路，只要它們不會改變的端對端要求/回應通訊。 例如，您可以在應用程式閘道子網路中設定 UDR，使其指向防火牆設備以進行封包檢查，但您必須確定封包在經過檢查後可送達預定目的地。 若未這麼做，可能會導致不正確的健康情況探查或流量路由行為。 這包括學習到的路由，或是 ExpressRoute 或 VPN 閘道在虛擬網路中傳播的預設 0.0.0.0/0 路由。

如果 v2 SKU，應用程式閘道子網路的 Udr 不支援。 如需詳細資訊，請參閱[自動調整規模和區域備援應用程式閘道 (公開預覽)](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant#known-issues-and-limitations)。

> [!NOTE]
> 使用應用程式閘道子網路的 Udr 會導致中的健全狀況狀態[後端健康情況檢視](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics#back-end-health)顯示為**未知**和 failue 的新一代應用程式閘道記錄檔也會產生與計量。 它會建議您不要使用 Udr 應用程式閘道子網路上要能夠檢視後端健康狀態、 記錄和計量。

## <a name="frontend-ip"></a>前端 IP

可将应用程序网关配置为使用公共 IP 地址和/或专用 IP 地址。 當您裝載的後端，才能透過網際網路對向的 VIP 網際網路用戶端存取時需要公用 IP。 公用 IP 並不需要不公開到網際網路，也稱為內部負載平衡器 (ILB) 端點的內部端點。 使用 ILB 設定閘道適合不會對網際網路公開的內部企業營運應用程式。 對於位在不會對網際網路公開的安全性界限中的多層式應用程式內的服務/階層也十分實用，但仍需要循環配置資源負載散發、工作階段綁定或安全通訊端層 (SSL) 終止。

仅支持一个公共 IP 地址或一个专用 IP 地址。 您選擇同時建立應用程式閘道的前端 IP。 

- 公用 IP 時，您可以選擇建立新的公用 IP，或使用現有的公用 IP 的應用程式閘道相同的位置。 如果您建立新的公用 IP 位址，就無法稍後變更 IP 位址選取的類型 （static 或 dynamic）。 如需詳細資訊，請參閱[靜態與動態公用 IP](https://docs.microsoft.com/azure/application-gateway/application-gateway-components) 

- 發生的私人 IP，您可以選擇指定從在其中建立應用程式閘道的子網路的私人 IP 位址。 如果未明確指定，任意的 IP 位址將會自動選取從子網路。 如需詳細資訊，請參閱[建立應用程式閘道與內部負載平衡器 (ILB) 端點。](https://docs.microsoft.com/azure/application-gateway/application-gateway-ilb-arm)

前端 IP 與相關聯*接聽程式*哪些檢查在前端 IP 的連入要求。

## <a name="listeners"></a>接聽程式

接聽程式是一種邏輯實體用來檢查內送連接要求中，使用連接埠、 通訊協定、 主機和 IP 位址。 因此，當您設定接聽程式，您必須輸入這些值的連接埠，通訊協定、 主機和 IP 位址是相同閘道上的連入要求中的對應值。 當您建立使用 Azure 入口網站的應用程式閘道時，您也會建立預設的接聽程式接聽程式選擇的通訊協定和連接埠。 此外，您可以選擇是否要啟用接聽程式上的 HTTP2 支援。 應用程式閘道建立之後，您可以編輯此預設接聽程式的設定 (*appGatewayHttpListener*/*appGatewayHttpsListener*) 和/或建立新的接聽程式。

### <a name="listener-type"></a>接聽項型別

您可以選擇[基本 」 或 「 多站台接聽程式](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#types-of-listeners)時建立新的接聽程式。 

- 如果您要裝載應用程式閘道的單一站台，請選擇 基本接聽程式。 了解[如何建立基本接聽程式的應用程式閘道](https://docs.microsoft.com/azure/application-gateway/quick-create-portal)。

- 如果您要在相同的應用程式閘道執行個體上設定一個以上的 web 應用程式或多個相同的父系網域的子網域，然後選擇 多站台接聽程式。 多站台接聽程式，您將此外要輸入主機名稱。 這是因為應用程式閘道會依賴 HTTP 1.1 主機標頭來裝載多個網站上的相同公用 IP 位址和連接埠。

#### <a name="order-of-processing-listeners"></a>處理接聽程式的順序

V1 的 Sku，如果接聽程式會處理它們顯示的順序。 因此，如果基本接聽程式符合傳入的要求，就會先處理它。 因此，多站台接聽程式應該設定基本接聽程式，以確保流量會路由至正確的後端之前。

V2 Sku，如果基本接聽程式之前處理多站台接聽程式。

### <a name="frontend-ip"></a>前端 IP

選擇您想要與此接聽程式產生關聯的前端 IP。 接聽程式會接聽這個 ip 的連入要求。

### <a name="frontend-port"></a>前端連接埠

選擇前端連接埠。 您可以選擇從現有的連接埠，或建立新的。 您可以選擇的任何值[允許的連接埠範圍](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#ports)。 這可讓您不僅使用已知的通訊埠，例如 80 和 443，但是任何允許適用於您使用自訂連接埠。 一個連接埠用於公用面向的接聽程式 」 或 「 私用對向的接聽程式。

### <a name="protocol"></a>通訊協定

您必須選擇 HTTP 和 HTTPS 通訊協定。 

- 如果您選擇 HTTP 時，用戶端和應用程式閘道之間的流量都會加密。

- 如果您有興趣，請選擇 HTTPS [Secure Sockets Layer (SSL) 終止](https://docs.microsoft.com/azure/application-gateway/overview)或是[端對端 SSL 加密](https://docs.microsoft.com/azure/application-gateway/ssl-overview)。 如果您選擇 HTTPS 時，用戶端和應用程式閘道之間的流量將會加密，並將應用程式閘道終止 SSL 連線。  如果您想要端對端 SSL 加密，您另外必須設定時選擇 HTTPS 通訊協定*後端 HTTP 設定*。 這可確保從後端應用程式閘道時，重新加密流量。

  若要設定 Secure Sockets Layer (SSL) 終止和端對端 SSL 加密，可以加入至接聽程式，以便在啟用 衍生依據 SSL 通訊協定規格的對稱金鑰的應用程式閘道需要憑證。 對稱金鑰則用來加密和解密流量傳送至閘道中。 閘道憑證必須採用「個人資訊交換」(PFX) 格式。 此檔案格式可允許將私密金鑰匯出，而應用程式閘道需要這個匯出的金鑰來執行流量的加密和解密。 

#### <a name="supported-certificates"></a>支援的憑證

請參閱[支援 SSL 終止憑證](https://docs.microsoft.com/azure/application-gateway/ssl-overview#certificates-supported-for-ssl-termination)。

### <a name="additional-protocol-support"></a>其他通訊協定支援

#### <a name="http2-support"></a>HTTP2 支援

HTTP/2 通訊協定支援僅適用於連線到應用程式閘道接聽程式的用戶端。 與後端伺服器集區的通訊是透過 HTTP/1.1 進行的。 預設已停用 HTTP/2 支援。 下列 Azure PowerShell 程式碼片段範例示範如何啟用它：

```azurepowershell
$gw = Get-AzureRmApplicationGateway -Name test -ResourceGroupName hm

$gw.EnableHttp2 = $true

Set-AzureRmApplicationGateway -ApplicationGateway $gw
```

#### <a name="websocket-support"></a>Websocket 支援

默认已启用 WebSocket 支持。 使用者無法進行設定來選擇要啟用或停用 WebSocket 支援。 可对 HTTP 和 HTTPS 侦听器使用 WebSocket。 

### <a name="custom-error-page"></a>自訂錯誤頁面

自訂錯誤頁面可以定義在全域層級，以及接聽程式層級，不過，從 Azure 入口網站中建立全域層級的自訂錯誤頁面目前不支援。 您可以將 403 WAF 錯誤的自訂錯誤頁面 」 或 「 502 的 [維護] 頁面上設定接聽程式層級。 您也需要指定可公開存取的 blob URL 指定的錯誤狀態碼。 如需詳細資訊，請參閱 <<c0> [ 建立自訂錯誤頁面](https://docs.microsoft.com/azure/application-gateway/custom-error)。

![應用程式閘道錯誤碼](https://docs.microsoft.com/azure/application-gateway/media/custom-error/ag-error-codes.png)

若要設定一個通用的自訂錯誤頁面，請使用[設定 Azure PowerShell](https://docs.microsoft.com/azure/application-gateway/custom-error#azure-powershell-configuration) 

### <a name="ssl-policy"></a>SSL 原則

您可以集中管理 SSL 憑證，並降低後端伺服器陣列的加密和解密額外負荷。 這個集中式 SSL 處理也可讓您指定適合您組織安全性需求的中央 SSL 原則。  您可以選擇預設值，預先定義或自訂 SSL 原則。 

您可以設定 SSL 原則來控制 SSL 通訊協定版本。 您可以設定為拒絕 TLS1.0、 TLS1.1 和 TLS1.2 的應用程式閘道。 SSL 2.0 和 3.0 都已預設停用並，無法加以設定。 如需詳細資訊，請參閱 <<c0> [ 應用程式閘道 SSL 原則概觀](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-policy-overview)。

创建侦听器后，将它与某个请求路由规则相关联。该规则确定如何将侦听器上收到的请求路由到后端。

## <a name="request-routing-rule"></a>要求路由規則

在建立使用 Azure 入口網站應用程式閘道時，您會建立預設規則 (*rule1*)，它會繫結的預設接聽程式 (*appGatewayHttpListener*) 與預設後端集區 (*appGatewayBackendPool*) 和預設後端 HTTP 設定 (*appGatewayBackendHttpSettings*)。 應用程式閘道建立之後，您可以編輯此預設規則的設定和/或建立新的規則。

### <a name="rule-type"></a>規則型別

您可以選擇[基本] 或 [路徑型規則](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#request-routing-rule)時建立新的規則。 

- 如果您想要轉送的相關聯的接聽程式上的所有要求 (例如： blog.contoso.com/*) 單一的後端集區中，選擇 基本接聽程式。 
- 選擇路徑為基礎接聽程式如果您想要路由傳送要求以特定的後端集區的特定 URL 路徑。 路径模式仅应用到 URL 的路径，而不应用到该 URL 的查询参数。


#### <a name="order-of-processing-rules"></a>處理規則的順序

V1 Sku 時的連入要求的模式比對是路徑型規則的 URL 路徑對應中會列出路徑的順序處理。 基於這個理由，如果要求符合 URL 路徑對應中的兩個或多個路徑中的模式，然後為列出的路徑第一次將會比對，並要求會轉送至後端與該路徑相關聯。

發生 v2 Sku，完全符合會保存於 URL 路徑對應中會列出的路徑順序較高的優先順序。 針對該理由，如果要求符合兩個或多個路徑中的模式，則要求會轉送至後端相關聯，完全符合要求的路徑。 如果連入要求中的路徑完全符合 URL 路徑對應中的任何路徑，然後比對的連入要求的模式會處理這些路徑列示在 路徑型規則的 URL 路徑對應中的順序。

### <a name="associated-listener"></a>相關聯的接聽程式

您需要建立關聯之規則的接聽程式，讓*要求路由規則*聯*接聽程式*進行評估以判斷*後端集區*的要求會路由傳送。

### <a name="associated-backend-pool"></a>相關聯的後端集區

建立包含後端目標會提供接聽程式所接收的要求的後端集區的關聯。 發生基本的規則，因為相關聯的接聽程式上的所有要求都將都轉送到此後端集區，被允許只有一個後端集區。 路徑型規則，如果新增多個後端集區對應至每個 URL 路徑。 比對，在此處輸入的 URL 路徑的要求會轉送到對應的後端集區中。 此外，加入預設的後端集區，因為不符合任何輸入此規則中的 URL 路徑的要求都將轉送到它。

### <a name="associated-backend-http-setting"></a>相關聯的後端 HTTP 設定

加入每個規則的後端 HTTP 設定。 會將要求路由傳送從應用程式閘道使用的連接埠號碼、 通訊協定和其他設定此設定中指定的後端目標。 發生基本的規則，因為相關聯的接聽程式上的所有要求都將都轉送到對應的後端目標，使用此 HTTP 設定，被允許只有一個後端 HTTP 設定。 路徑型規則，如果新增多個對應至每個 URL 路徑的後端 HTTP 設定。 比對，在此處輸入的 URL 路徑的要求會轉送到對應的後端目標，使用對應至每個 URL 路徑的 HTTP 設定。 此外，加入預設 HTTP 設定，因為不符合任何輸入此規則中的 URL 路徑的要求都將轉送到預設的後端集區使用的預設 HTTP 設定。

### <a name="redirection-setting"></a>重新導向設定

如果重新導向設定的基本規則，相關聯的接聽程式上的所有要求將重新都導向至重新導向的目標，藉此讓全域重新導向。 如果重新導向設定路徑型規則，要求只在特定站台] 區域中，例如購物車 」 區域，以表示 *，會被重新導向至重新導向的目標，藉此讓 [路徑型重新導向。 

重新導向功能的相關資訊，請參閱[重新導向概觀](https://docs.microsoft.com/azure/application-gateway/redirect-overview)。

- #### <a name="redirection-type"></a>重新導向類型

  選擇所需的重新導向的型別：Permanent(301)、 Temporary(307)、 Found(302) 或，請參閱 other(303)。

- #### <a name="redirection-target"></a>重新導向目標

  您可以選擇另一個接聽程式或外部網站做為重新導向目標。 

  - ##### <a name="listener"></a>接聽程式

    選擇的接聽程式做為重新導向目標有助於從一個接聽程式到閘道上的另一個接聽程式將重新導向。 您想要啟用 HTTP 至 HTTPS 重新導向，亦即，從來源接聽程式檢查來檢查傳入的 HTTPS 要求的目標接聽程式的連入 HTTP 要求重新導向流量時，需要此設定。 您也可以選擇要包含在要求轉送到重新導向目標中的原始要求中的查詢字串和路徑。![application-gateway-components](./media/configuration-overview/configure-redirection.png)

    如需有關 HTTP 至 HTTPS 重新導向，請參閱[使用入口網站的 HTTP 至 HTTP 重新導向](https://docs.microsoft.com/azure/application-gateway/redirect-http-to-https-portal)，[使用 PowerShell 的 HTTP 至 HTTP 重新導向](https://docs.microsoft.com/azure/application-gateway/redirect-http-to-https-powershell)， [HTTP 至 HTTP 重新導向使用 CLI](https://docs.microsoft.com/azure/application-gateway/redirect-http-to-https-cli)

  - ##### <a name="external-site"></a>外部網站

    當您想要將接聽程式因此規則相關聯重新導向至外部網站上的流量重新導向，請選擇 外部站台。 若要包含在要求中轉送到重新導向目標的原始要求中，您可以選擇的查詢字串。 您無法轉送至外部站台的原始要求中的路徑。

    如需有關重新導向至外部網站的詳細資訊，請參閱[將流量重新導向至外部站台使用 PowerShell](https://docs.microsoft.com/azure/application-gateway/redirect-external-site-powershell)和 [https://docs.microsoft.com/azure/application-gateway/redirect-external-site-cli](https://docs.microsoft.com/azure/application-gateway/redirect-external-site-cli)

#### <a name="rewrite-http-header-setting"></a>重新撰寫 HTTP 標頭設定

這項功能可讓您新增、 移除或更新 HTTP 要求和回應標頭時要求和回應封包用戶端與後端集區之間移動。    您可以設定這項功能只能透過 PowerShell。 入口網站和 CLI 支援尚無法使用。 如需詳細資訊，請參閱 <<c0> [ 重寫的 HTTP 標頭](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers)概觀並[設定的 HTTP 標頭重寫](https://docs.microsoft.com/azure/application-gateway/add-http-header-rewrite-rule-powershell#specify-your-http-header-rewrite-rule-configuration)。

## <a name="http-settings"></a>HTTP 設定

應用程式閘道會將流量路由至後端伺服器使用此元件中指定的組態。 一旦您建立的 HTTP 設定，您必須將其關聯與一個或多個要求路由規則。

### <a name="cookie-based-affinity"></a>以 Cookie 為依據的親和性

需要在同一台服务器上保留用户会话时，此功能非常有用。 使用受閘道管理的 Cookie，應用程式閘道即可將來自使用者工作階段的後續流量導向至同一部伺服器進行處理。 當使用者工作階段的工作階段狀態儲存在伺服器本機時，這項功能很重要。 如果應用程式無法處理 cookie 型同質性，然後您將無法使用這項功能。 若要使用以 cookie 為基礎的工作階段親和性，您應該確定用戶端必須支援 cookie。 

### <a name="connection-draining"></a>清空連線

清空連線可協助您在已規劃的服務更新期間，毫無錯誤地移除後端集區成員。 在创建规则期间，可将此设置应用到后端池的所有成员。 啟用之後，應用程式閘道可確保取消註冊的所有執行個體的後端集區不會在讓現有的要求，若要設定的時間限制內完成時收到任何新的要求。 這適用於透過 API 呼叫從後端集區中確實移除的後端執行個體，以及根據健康情況探查的判斷，而回報為狀況不良的後端執行個體。

### <a name="protocol"></a>通訊協定

應用程式閘道支援 HTTP 和 HTTPS 通訊協定要求路由傳送到後端伺服器。 如果選擇 HTTP 通訊協定，然後流量流程後端伺服器未加密。 在這些情況下，其中未加密的通訊後, 端伺服器不是可接受的選項，您應該選擇 HTTPS 通訊協定。 此設定的接聽程式中選擇 HTTPS 通訊協定結合可讓您啟用[端對端 SSL](https://docs.microsoft.com/azure/application-gateway/ssl-overview)。 可讓您安全地傳輸至後端加密的敏感性資料。 已啟用端對端 SSL 之後端集區中的每個後端伺服器，都必須設有憑證以便能夠進行安全通訊。

### <a name="port"></a>Port

這是後端伺服器會接聽來自應用程式閘道的流量的連接埠。 您可以設定介於 1 到 65535 之間的連接埠。

### <a name="request-timeout"></a>要求逾時

應用程式閘道會等候接收來自後端集區的回應，傳回 「 連線逾時 」 的錯誤之前的秒數。

### <a name="override-backend-path"></a>覆寫後端路徑

此設定可讓您設定會將要求轉送至後端時所要使用的選擇性自訂的轉送路徑。 這會將複製的相符項目中指定的自訂路徑的連入路徑的任何一部分**覆寫後端路徑**欄位轉送的路徑。 請參閱下表來了解功能的運作方式。

- 當 HTTP 設定附加至基本的要求路由規則：

  | 原始要求  | 覆寫後端路徑 | 要求轉送至後端 |
  | ----------------- | --------------------- | ---------------------------- |
  | /home/            | /override/            | 覆寫/home / /              |
  | / home/secondhome / | /override/            | 覆寫/home/secondhome /   |

- 當 HTTP 設定附加至路徑為基礎的要求路由規則：

  | 原始要求           | 路徑規則       | 覆寫後端路徑 | 要求轉送至後端 |
  | -------------------------- | --------------- | --------------------- | ---------------------------- |
  | /pathrule/home /            | /pathrule*      | /override/            | 覆寫/home / /              |
  | /pathrule/home/secondhome/ | /pathrule*      | /override/            | 覆寫/home/secondhome /   |
  | /home/                     | /pathrule*      | /override/            | 覆寫/home / /              |
  | / home/secondhome /          | /pathrule*      | /override/            | 覆寫/home/secondhome /   |
  | /pathrule/home /            | pathrule/首頁 * | /override/            | /override/                   |
  | /pathrule/home/secondhome/ | pathrule/首頁 * | /override/            | 覆寫/secondhome / /        |

### <a name="use-for-app-service"></a>為應用程式服務使用

這是 UI 快顯會選取兩個必要的設定，應用程式服務後端-可讓挑選從後端位址的主機名稱，並建立新的自訂探查。 為什麼要完成先前的原因針對一節所述**挑選從後端位址的主機名稱**設定。 也從後端成員的地址挑選探查標頭，會建立新的探查。

### <a name="use-custom-probe"></a>使用自訂探查

這項設定用來建立關聯[自訂探查](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#custom-health-probe)此 HTTP 設定。 您可以將只有一個自訂探查關聯的 HTTP 設定。 如果您不要明確建立關聯的自訂探查，然後[預設探查](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#default-health-probe-settings)將用來監視後端的健康情況。 建議您建立更細微地控制您的後端健康情況監視的自訂探查。

> [!NOTE]   
> 自訂探查將不會開始監視後端集區的健全狀況，除非明確相關聯的接聽程式對應的 HTTP 設定。

### <a name="pick-host-name-from-backend-address"></a>從後端位址挑選主機名稱

這項功能以動態方式設定*主機*中使用的 IP 位址或完整的網域名稱 (FQDN) 的後端集區的主機名稱的要求標頭。 這是很有幫助後, 端的網域名稱不同於應用程式閘道的 DNS 名稱後, 端會依賴特定主機標頭或 SNI 擴充功能來為多租用戶服務時解析為正確的端點，例如後端。 由於應用程式服務是多租用戶服務的單一 IP 位址搭配使用共用的空間，就可以存取應用程式服務只能搭配自訂網域設定中所設定的主機名稱。 根據預設，自訂網域名稱是*example.azurewebsites.net*。 因此，如果您想要存取您的 App service 使用應用程式閘道與其中一個未明確在應用程式服務中註冊的主機名稱或應用程式閘道的 FQDN，您必須藉由覆寫應用程式服務的主機名稱、 原始要求中的主機名稱啟用**挑選從後端位址的主機名稱**設定。

如果您擁有自訂網域，而且已將現有的自訂 DNS 名稱對應至 App service，則您不需要啟用此設定。

> [!NOTE]   
> 因為 ASE 是專用的部署，則不需要的 App Service Environment (ASE) 中執行這項設定。 

### <a name="host-name-override"></a>主機名稱覆寫

此功能取代了*主機*傳入的要求，您在此處指定的主機名稱的應用程式閘道中的標頭。 比方說，如果 www\.contoso.com 指定為**主機名稱**設定，原始要求 https://appgw.eastus.cloudapp.net/path1就會變更為 https://www.contoso.com/path1要求轉寄到後端伺服器的時機。 

## <a name="backend-pool"></a>後端集區

後端集區可以指向後端成員的四種： 特定的虛擬機器、 虛擬機器擴展集、 IP 位址 /FQDN 或 app service。 每個後端集區可以指向相同類型的多個成員。 不支援指向相同後端集區中的不同類型的成員。 

建立後端集區之後，您需要將它包含一或多個要求路由規則產生關聯。 您也需要設定您的應用程式閘道上的每個後端集區的健康情況探查。 當符合路由規則條件的要求時，應用程式閘道將流量轉送至對應的後端集區中狀況良好伺服器 （做為健康狀態探查所決定）。

## <a name="health-probes"></a>健康狀態探查

即使應用程式閘道會監視其後端中的所有資源健全狀況，根據預設，強烈建議您建立每個後端 HTTP 設定，以便有更細微地控制健全狀況監視的自訂探查。 若要了解如何設定自訂的健康狀態探查，請參閱[自訂健全狀況探查設定](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#custom-health-probe-settings)。

> [!NOTE]   
> 一旦您建立自訂的健康狀態探查，您需要將其關聯到後端 HTTP 設定。 自訂探查將不會開始監視後端集區的健全狀況，除非明確相關聯的接聽程式對應的 HTTP 設定。

## <a name="next-steps"></a>後續步驟

了解应用程序网关组件后，可以：

- [在 Azure 门户中创建应用程序网关](quick-create-portal.md)
- [建立應用程式閘道上使用 PowerShell](quick-create-powershell.md)
- [建立使用 Azure CLI 的應用程式閘道](quick-create-cli.md)
