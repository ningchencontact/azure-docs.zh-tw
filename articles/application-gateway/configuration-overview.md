---
title: Azure 應用程式閘道設定概觀
description: 這篇文章說明如何設定 Azure 應用程式閘道的元件
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 03/20/2019
ms.author: absha
ms.openlocfilehash: 40c5444a54f4e483a9dcacb958c18f66da45019a
ms.sourcegitcommit: 9f4eb5a3758f8a1a6a58c33c2806fa2986f702cb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2019
ms.locfileid: "58906118"
---
# <a name="application-gateway-configuration-overview"></a>應用程式閘道設定概觀

Azure 應用程式閘道是由您可以針對不同案例的各種方式設定的數個元件所組成。 這篇文章會示範如何設定每個元件。

![應用程式閘道元件流程圖](./media/configuration-overview/configuration-overview1.png)

此圖顯示具有三個接聽程式的應用程式。 前兩個是多站台接聽程式`http://acme.com/*`和`http://fabrikam.com/*`分別。 同時接聽連接埠 80。 第三個是基本接聽程式具有端對端安全通訊端層 (SSL) 終止。


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>必要條件

### <a name="azure-virtual-network-and-dedicated-subnet"></a>Azure 虛擬網路和專用子網路

應用程式閘道是專用的部署，在您的虛擬網路中。 在您的虛擬網路內的專用子網路是必要應用程式閘道。 您可以將多個執行個體的指定應用程式閘道部署在子網路。 您也可以部署其他的應用程式閘道子網路中。 但您無法部署應用程式閘道子網路中的任何其他資源。

> [!NOTE]
> 您不能混合 Standard_v2 和標準的 Azure 應用程式閘道位於相同子網路。

#### <a name="size-of-the-subnet"></a>子網路的大小

如果私人前端 IP 設定，應用程式閘道便會耗用 1 個私人 IP 位址，每個執行個體，加上另一個私人 IP 位址。

Azure 也會保留供內部使用的每個子網路中的 5 個 IP 位址： 第 4 和最後一個 IP 位址。 例如，請考慮 15 應用程式閘道執行個體，不含私人前端 ip。 您需要至少 20 個 IP 位址，此子網路：為內部使用和應用程式閘道執行個體的 15 5。 因此，您需要/27 子網路大小或更大。

請考慮具有 27 的應用程式閘道執行個體和 IP 位址的私人前端 IP 子網路。 在此情況下，您需要 33 的 IP 位址：應用程式閘道執行個體，1 代表私用的後端，供內部使用 5 27。 因此，您需要 / 26 大小或更大的子網路。

我們建議您使用至少/28 子網路大小。 此大小可讓您 11 個可用的 IP 位址。 如果您的應用程式的負載需要超過 10 個 IP 位址，請考慮/27 或/26 子網路大小。

#### <a name="network-security-groups-on-the-application-gateway-subnet"></a>應用程式閘道子網路的網路安全性群組

網路安全性群組 (Nsg) 應用程式閘道上支援。 不過，有幾項限制：

- 您必須包括 v2 sku 的連接埠 65503-65534 v1 應用程式閘道的 sku 和 65200-65535 之間的連接埠上的連入流量的例外狀況。 Azure 基礎結構通訊需要此連接埠範圍。 這些連接埠會受到保護 （鎖定） 受到 Azure 憑證。 外部實體，包括這些閘道的客戶無法起始對這些端點，而不需要適當的憑證，就地變更。

- 無法封鎖輸出網際網路連線。 預設輸出規則，NSG 中的允許網際網路連線。 建議您：

  - 請勿移除預設輸出規則。
  - 請勿建立其他輸出規則，拒絕輸出網際網路連線。

- 流量**AzureLoadBalancer**必須允許標記。

##### <a name="whitelist-application-gateway-access-to-a-few-source-ips"></a>將一些來源 Ip 允許清單應用程式閘道存取

此案例中，使用 Nsg 中的應用程式閘道子網路上的內容。 依照此優先順序在子網路上將下列限制：

1. 允許連入流量的來源 IP/IP 範圍。
2. 允許連入要求從所有來源的連接埠 65503-65534[後端健康情況通訊](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)。 Azure 基礎結構通訊需要此連接埠範圍。 這些連接埠會受到保護 （鎖定） 受到 Azure 憑證。 沒有適當位置中的憑證，外部實體無法起始對這些端點的變更。
3. 允許連入的 Azure Load Balancer 會探查 (*AzureLoadBalancer*標記) 和輸入虛擬網路流量 (*VirtualNetwork*標記) 上[網路安全性群組](https://docs.microsoft.com/azure/virtual-network/security-overview)。
4. 使用全部拒絕規則，以封鎖其他所有的連入流量。
5. 允許所有目的地對網際網路的輸出流量。

#### <a name="user-defined-routes-supported-on-the-application-gateway-subnet"></a>使用者定義的路由應用程式閘道子網路支援

使用者定義路由 (Udr) v1 SKU，則支援應用程式閘道子網路，只要它們不會改變端對端要求/回應通訊。 比方說，您可以設定在應用程式閘道子網路 UDR 以指向防火牆設備的封包檢查。 但是，您必須先確定的封包都可以聯繫其目的地之後檢查。 若要這樣做可能會導致不正確的健全狀況探查或流量路由的行為。 這包括學習到的路由或預設 0.0.0.0/0 路由傳送虛擬網路中的 Azure ExpressRoute 或 VPN 閘道。

V2 sku，不支援應用程式閘道子網路的 Udr。 如需詳細資訊，請參閱 <<c0> [ 自動調整和應用程式閘道的區域備援](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant#known-issues-and-limitations)。

> [!NOTE]
> 使用應用程式閘道子網路的 Udr 會導致中的健全狀況狀態[後端健康情況檢視](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics#back-end-health)顯示為 「 不明 」。 它也會讓產生的應用程式閘道記錄檔和度量資訊失敗。 我們建議您不要 Udr 使用應用程式閘道子網路，您可以檢視後端健康狀態、 記錄和計量。

## <a name="front-end-ip"></a>前端 IP

您可以設定應用程式閘道有公用 IP 位址、 私人 IP 位址，或兩者。 當您在裝載用戶端必須透過網際網路對向虛擬 IP (VIP) 在網際網路上存取後端時需要公用 IP。 

公用 IP 並不需要不會向網際網路公開的內部端點。 這稱為*內部的 load balancer* (ILB) 端點。 應用程式閘道 ILB 可用於不會被公開到網際網路的內部特定業務應用程式。 它也可用於服務和安全性界限內的多層式應用程式中的層，不會被公開到網際網路，但需要的循環配置資源負載散發、 工作階段綁定或 SSL 終止。

支援 1 個公用 IP 位址或 1 個私人 IP 位址。 當您建立應用程式閘道時，您可以選擇前端 IP。

- 針對公用 IP，您可以建立新的公用 IP 位址，或使用現有的公用 IP 的應用程式閘道相同的位置。 如果您建立新的公用 IP，您選取的 IP 位址類型 （靜態或動態） 稍後無法變更。 如需詳細資訊，請參閱 <<c0> [ 靜態與動態公用 IP 位址](https://docs.microsoft.com/en-us/azure/application-gateway/application-gateway-components#static-vs-dynamic-public-ip-address)。

- 針對私人 IP，您可以指定從應用程式閘道建立所在的子網路的私人 IP 位址。 如果您未指定，會自動會從子網路選取任意 IP 位址。 如需詳細資訊，請參閱 <<c0> [ 建立的應用程式閘道搭配內部負載平衡器](https://docs.microsoft.com/azure/application-gateway/application-gateway-ilb-arm)。

前端的 IP 位址與相關聯*接聽程式*，這會檢查內送的前端 IP 的要求。

## <a name="listeners"></a>接聽程式

接聽程式是一種邏輯實體，會檢查內送連接要求，使用連接埠、 通訊協定、 主機和 IP 位址。 當您設定接聽程式時，您必須輸入值，這些符合閘道上的連入要求中的對應值。

當您使用 Azure 入口網站建立應用程式閘道時，您也會建立預設的接聽程式接聽程式選擇的通訊協定和連接埠。 您可以選擇是否要啟用接聽程式上的 HTTP2 支援。 建立應用程式閘道之後，您可以編輯該預設接聽程式的設定 (*appGatewayHttpListener*/*appGatewayHttpsListener*) 或建立新的接聽程式。

### <a name="listener-type"></a>接聽項型別

當您建立新的接聽程式時，您選擇[*基本*並*多站台*](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#types-of-listeners)。

- 如果您裝載的應用程式閘道的單一站台，請選擇 [基本]。 了解[如何建立基本接聽程式的應用程式閘道](https://docs.microsoft.com/azure/application-gateway/quick-create-portal)。

- 如果您要設定一個以上的 web 應用程式或多個相同的父系網域的子網域相同的應用程式閘道執行個體上，選擇多站台接聽程式。 多站台接聽程式，您也必須輸入主機名稱。 這是因為應用程式閘道會依賴 HTTP 1.1 主機標頭來裝載多個網站上的相同公用 IP 位址和連接埠。

#### <a name="order-of-processing-listeners"></a>處理接聽程式的順序

V1 的 sku，接聽程式會處理它們所列出的順序。 如果基本接聽程式符合傳入的要求，接聽程式會先處理該要求。 因此，設定多站台接聽程式之前先確定流量會路由至正確的後端的基本接聽程式。

V2 sku，多站台接聽程式會處理之前基本接聽程式。

### <a name="front-end-ip"></a>前端 IP

選擇想要與此接聽程式產生關聯的前端 IP 位址。 接聽程式會接聽這個 ip 的連入要求。

### <a name="front-end-port"></a>前端連接埠

選擇前端連接埠。 選取現有的連接埠，或建立新的帳戶。 選擇的任何值[允許的連接埠範圍](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#ports)。 您可以使用已知的通訊埠，例如 80 和 443，不僅適合任何允許自訂連接埠。 公眾對應的接聽程式 」 或 「 私用對向的接聽程式，可以使用的連接埠。

### <a name="protocol"></a>通訊協定

選擇 HTTP 或 HTTPS:

- 如果您選擇 HTTP 時，用戶端與應用程式閘道之間的流量未加密。

- 如果您想選擇 HTTPS [SSL 終止](https://docs.microsoft.com/azure/application-gateway/overview#secure-sockets-layer-ssl-terminationl)或是[端對端 SSL 加密](https://docs.microsoft.com/azure/application-gateway/ssl-overview)。 用戶端與應用程式閘道之間的流量會加密。 並終止應用程式閘道的 SSL 連線。 如果您想要端對端 SSL 加密，您必須選擇 HTTPS，並設定**後端 HTTP**設定。 這可確保它會從應用程式閘道的後端傳送時，重新加密流量。

若要設定 SSL 終止和端對端 SSL 加密，您必須將憑證加入接聽程式，以啟用應用程式閘道，以衍生對稱金鑰。 這取決於 SSL 通訊協定規格。 對稱金鑰用來加密和解密會傳送至閘道的流量。 閘道憑證必須是個人資訊交換 (PFX) 格式。 此格式可讓您匯出私密金鑰閘道用來加密和解密流量。

#### <a name="supported-certificates"></a>支援的憑證

請參閱[支援 SSL 終止憑證](https://docs.microsoft.com/azure/application-gateway/ssl-overview#certificates-supported-for-ssl-termination)。

### <a name="additional-protocol-support"></a>其他通訊協定支援

#### <a name="http2-support"></a>HTTP2 支援

連線到應用程式閘道接聽程式的用戶端可存取 HTTP/2 通訊協定支援。 後端伺服器集區的通訊是透過 HTTP/1.1。 預設已停用 HTTP/2 支援。 下列 Azure PowerShell 程式碼片段示範如何啟用此功能：

```azurepowershell
$gw = Get-AzApplicationGateway -Name test -ResourceGroupName hm

$gw.EnableHttp2 = $true

Set-AzApplicationGateway -ApplicationGateway $gw
```

#### <a name="websocket-support"></a>WebSocket 支援

預設會啟用 WebSocket 支援。 沒有任何使用者可設定的設定，啟用或停用它。 可对 HTTP 和 HTTPS 侦听器使用 WebSocket。

### <a name="custom-error-pages"></a>自訂錯誤頁面

您可以定義在全域層級或接聽程式層級自訂的錯誤。 但從 Azure 入口網站中建立全域層級自訂錯誤頁面目前不支援。 您可以在接聽程式層級設定 403 的 web 應用程式防火牆錯誤的自訂錯誤頁面 」 或 「 502 維護 頁面。 您也必須指定可公開存取的 blob URL 指定的錯誤狀態碼。 如需詳細資訊，請參閱[建立應用程式閘道的自訂錯誤頁面](https://docs.microsoft.com/azure/application-gateway/custom-error)。

![應用程式閘道錯誤碼](https://docs.microsoft.com/azure/application-gateway/media/custom-error/ag-error-codes.png)

若要設定一個通用的自訂錯誤頁面，請參閱[Azure PowerShell 設定](https://docs.microsoft.com/azure/application-gateway/custom-error#azure-powershell-configuration)。

### <a name="ssl-policy"></a>SSL 原則

您可以集中管理 SSL 憑證，並降低額外負荷的後端伺服器陣列的加密解密。 集中式的 SSL 處理也可讓您指定適合您的安全性需求的中央 SSL 原則。 您可以選擇*預設*，*預先定義*，或*自訂*SSL 原則。

您設定 SSL 原則來控制 SSL 通訊協定版本。 您可以將應用程式閘道設定為拒絕 TLS1.0、 TLS1.1 和 tls 1.2。 根據預設，SSL 2.0 和 3.0 會停用，而不是可設定。 如需詳細資訊，請參閱 <<c0> [ 應用程式閘道 SSL 原則概觀](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-policy-overview)。

建立接聽程式之後，您會將其與要求路由規則。 該規則會判斷如何接聽程式所接收的要求會路由傳送至後端。

## <a name="request-routing-rules"></a>要求路由規則

當您使用 Azure 入口網站建立應用程式閘道時，您會建立預設規則 (*rule1*)。 此規則會繫結的預設接聽程式 (*appGatewayHttpListener*) 與預設後端集區 (*appGatewayBackendPool*) 和預設的後端 HTTP 設定 (*appGatewayBackendHttpSettings*)。 建立閘道之後，您可以編輯預設規則的設定，或建立新的規則。

### <a name="rule-type"></a>規則型別

當您建立規則時，您選擇[*基本*並*路徑型*](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#request-routing-rule)。

- 如果您想要轉寄的相關聯的接聽程式上的所有要求，請選擇基本 (例如*部落格<i></i>.contoso.com/\*)* 至單一後端集區。
- 選擇路徑為基礎如果您想要路由傳送到特定的後端集區的要求從特定的 URL 路徑。 路径模式仅应用到 URL 的路径，而不应用到该 URL 的查询参数。

#### <a name="order-of-processing-rules"></a>處理規則的順序

V1 的 sku，模式比對連入要求的處理，則會列出路徑的路徑型規則的 URL 路徑對應中的順序。 如果要求符合路徑對應中的兩個或多個路徑中的模式，所列路徑中第一次會比對。 會將要求轉送至後端與該路徑相關聯。

V2 sku，確切的相符項目會是較高的優先順序高於在 URL 路徑對應中的路徑順序。 如果要求符合兩個或多個路徑中的模式，此要求被轉送至後端完全符合要求的路徑與相關聯。 如果連入要求中的路徑不完全符合對應中的任何路徑，模式比對要求的處理路徑型規則路徑對應順序 清單中。

### <a name="associated-listener"></a>相關聯的接聽程式

關聯規則的接聽程式，讓*要求路由規則*相關聯的接聽程式進行評估以判斷後端集區，以將要求路由傳送。

### <a name="associated-back-end-pool"></a>相關聯的後端集區

包含服務的接聽程式接收的要求的後端目標的後端集區建立關聯的規則。

 - 基本規則，允許只能有一個後端集區。 相關聯的接聽程式上的所有要求都會都轉送到該後端集區。

 - 針對路徑為基礎的規則，將每個 URL 路徑中的多個對應的後端集區。 符合所輸入的 URL 路徑的要求都會轉送到對應的後端集區中。 此外，新增的預設後端集區。 不符合規則中的任何 URL 路徑的要求都會轉送到該集區中。

### <a name="associated-back-end-http-setting"></a>相關聯的後端 HTTP 設定

加入每個規則的後端 HTTP 設定。 要求會路由傳送從應用程式閘道的後端目標，使用的連接埠號碼、 通訊協定，以及指定此設定中的其他資訊。

基本規則，允許只能有一個後端 HTTP 設定。 相關聯的接聽程式上的所有要求都會都轉送到對應的後端目標，使用此 HTTP 設定。

加入每個規則的後端 HTTP 設定。 要求會路由傳送從應用程式閘道的後端目標，使用的連接埠號碼、 通訊協定，以及可在此設定中指定的其他資訊。

基本規則，允許只能有一個後端 HTTP 設定。 相關聯的接聽程式上的所有要求都會都轉送到對應的後端目標，使用此 HTTP 設定。

針對路徑為基礎的規則，將每個 URL 路徑中的多個對應的後端 HTTP 設定。 比對 URL 路徑，此設定中的要求都會轉送到對應的後端目標中，使用對應至每個 URL 路徑的 HTTP 設定。 此外，新增的預設 HTTP 設定。 不符合此規則中的任何 URL 路徑的要求都會轉送到預設的後端集區中，使用預設 HTTP 設定。

### <a name="redirection-setting"></a>重新導向設定

如果重新導向設定的基本規則，相關聯的接聽程式上的所有要求重新都導向目標。 這是*全域*重新導向。 如果重新導向路徑型規則設定，只有在特定網站區域中的要求會被重新導向。 範例是由表示的購物車區域 */cart/\**。 這是*路徑型*重新導向。

如需有關重新導向的詳細資訊，請參閱 <<c0> [ 應用程式閘道重新導向概觀](https://docs.microsoft.com/azure/application-gateway/redirect-overview)。

#### <a name="redirection-type"></a>重新導向類型

選擇所需的重新導向的型別：*Permanent(301)*， *Temporary(307)*， *Found(302)*，或*請參閱 other(303)*。

#### <a name="redirection-target"></a>重新導向目標

選擇另一個接聽程式或外部網站作為重新導向目標。

##### <a name="listener"></a>接聽程式

您可以選擇 接聽程式做為重新導向到另一個閘道上從一個接聽程式的流量重新導向目標。 當您想要啟用 HTTP 至 HTTPS 重新導向時需要此設定。 它會將從來源接聽程式會檢查目的地接聽程式會檢查傳入 HTTPS 要求的連入 HTTP 要求的流量重新導向。 您也可以選擇要求轉送至重新導向目標中包含的查詢字串和來自原始要求的路徑。

![應用程式閘道的元件 對話方塊](./media/configuration-overview/configure-redirection.png)

如需 HTTP 至 HTTPS 重新導向的詳細資訊，請參閱：
- [使用 Azure 入口網站的 HTTP-HTTP 重新導向](https://docs.microsoft.com/azure/application-gateway/redirect-http-to-https-portal)
- [使用 PowerShell 的 HTTP-HTTP 重新導向](https://docs.microsoft.com/azure/application-gateway/redirect-http-to-https-powershell)
- [使用 Azure CLI 的 HTTP 至 HTTP 重新導向](https://docs.microsoft.com/azure/application-gateway/redirect-http-to-https-cli)

##### <a name="external-site"></a>外部網站

當您想要重新導向至外部網站的此規則相關聯的接聽程式上的流量，請選擇 外部站台。 您可以選擇要轉送到重新導向目標的要求中包含來自原始要求的查詢字串。 您無法轉送至外部站台的原始要求中的路徑。

如需有關重新導向的詳細資訊，請參閱：
- [將流量重新導向至外部網站使用 PowerShell](https://docs.microsoft.com/azure/application-gateway/redirect-external-site-powershell)
- [將流量重新導向至外部網站使用 CLI](https://docs.microsoft.com/azure/application-gateway/redirect-external-site-cli)

#### <a name="rewrite-the-http-header-setting"></a>請重寫的 HTTP 標頭設定

這項設定會新增、 移除或更新 HTTP 要求和回應標頭時要求和回應封包將用戶端與後端集區之間移動。 您可以只設定這項功能，透過 PowerShell。 Azure 入口網站和 CLI 支援尚無法使用。 如需詳細資訊，請參閱

 - [重新撰寫 HTTP 標頭概觀](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers)
 - [設定 HTTP 標頭重寫](https://docs.microsoft.com/azure/application-gateway/add-http-header-rewrite-rule-powershell#specify-your-http-header-rewrite-rule-configuration)

## <a name="http-settings"></a>HTTP 設定

應用程式閘道會在此使用您指定的設定，將流量路由至後端伺服器。 建立 HTTP 設定之後，您必須將其關聯一或多個要求路由規則。

### <a name="cookie-based-affinity"></a>以 Cookie 為基礎的同質性

需要在同一台服务器上保留用户会话时，此功能非常有用。 閘道管理的 cookie 可讓應用程式閘道直接後續來自使用者工作階段到相同的伺服器進行處理。 當工作階段狀態儲存在本機伺服器上的使用者工作階段時，這很重要。 如果應用程式無法處理 cookie 型同質性，您無法使用這項功能。 若要使用它，請確定用戶端支援 cookie。

### <a name="connection-draining"></a>清空連線

連線清空，可協助您依正常程序在計畫性的服務更新期間移除後端集區成員。 您可以在規則建立期間後, 端集區的所有成員套用這項設定。 它可確保取消註冊的所有執行個體的後端集區不會收到任何新的要求。 同時，現有的要求才能設定的時間限制內完成。 連線清空適用於後端會明確地從後端集區移除 API 呼叫的執行個體。 它也適用於報告為後端執行個體*不良*健康狀態探查。

### <a name="protocol"></a>通訊協定

應用程式閘道支援 HTTP 和 HTTPS，要求路由傳送到後端伺服器。 如果您選擇 HTTP 後, 端伺服器的流量未加密。 如果未加密的通訊無法接受，請選擇 [HTTPS]。

這項設定使用 HTTPS 接聽程式支援結合[端對端 SSL](https://docs.microsoft.com/azure/application-gateway/ssl-overview)。 這可讓您安全地傳輸至後端所加密的敏感性資料。 在 已啟用的端對端 SSL 的後端集區中每個後端伺服器必須設定以允許安全通訊的憑證。

### <a name="port"></a>Port

此設定會指定其中的後端伺服器接聽流量從應用程式閘道的連接埠。 您可以設定介於 1 到 65535 之間的連接埠。

### <a name="request-timeout"></a>要求逾時

此設定是應用程式閘道會從後端集區接收回應，它會傳回 「 連線逾時 」 錯誤訊息之前等候的秒數。

### <a name="override-back-end-path"></a>覆寫後端路徑

此設定可讓您設定會將要求轉送至後端時所要使用的選擇性自訂的轉送路徑。 內送路徑符合自訂路徑中的任何一部分**覆寫後端路徑**欄位複製到轉送的路徑。 下表顯示這項功能的運作方式：

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

### <a name="use-for-app-service"></a>使用 app service

這是 UI 快顯選取兩個必要的設定，適用於 Azure App Service 後端。 它可讓**挑選從後端位址的主機名稱**，並建立新的自訂探查。 (如需詳細資訊，請參閱 <<c0> [ 挑選主機名稱，從後端位址](#pick)設定一節。)建立新的探查，探查標頭會挑出來自後端成員的位址。

### <a name="use-custom-probe"></a>使用自訂探查

這項設定將產生關聯[自訂探查](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#custom-health-probe)HTTP 設定。 您可以將只有一個自訂探查關聯的 HTTP 設定。 如果您沒有明確地使自訂探查[預設探查](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#default-health-probe-settings)用來監視後端的健康情況。 我們建議您建立更充分地掌控您的後端健康情況監視的自訂探查。

> [!NOTE]
> 自訂探查不監視後端集區的健全狀況，除非明確相關聯的接聽程式對應的 HTTP 設定。

### <a id="pick"/></a>選擇從後端位址的主機名稱

這項功能以動態方式設定*主機*後端集區的主機名稱的要求中的標頭。 它會使用 IP 位址或 FQDN。

這項功能可協助時後, 端的網域名稱不同於應用程式閘道的 DNS 名稱後, 端會依賴特定主機標頭或伺服器名稱指示 (SNI) 擴充功能，來解析為正確的端點。

範例案例是多租用戶的服務作為後端。 App service 是多租用戶服務的單一 IP 位址會使用共用的空間。 因此，應用程式服務只可以透過自訂網域設定中所設定的主機名稱存取。

根據預設，自訂網域名稱是*example.azurewebsites。<i></i>net*。 若要存取您的 app service 使用的應用程式閘道，透過不明確註冊的 app service 中的主機名稱或應用程式閘道的 FQDN，您會覆寫應用程式服務的主機名稱的原始要求中的主機名稱。 若要這樣做，請啟用**挑選從後端位址的主機名稱**設定。

為其現有的自訂 DNS 名稱會對應至 app service 的自訂網域，您不需要啟用此設定。

> [!NOTE]
> 此設定不需要 App Service Environment for PowerApps，這是專用的部署。

### <a name="host-name-override"></a>主機名稱覆寫

此功能取代了*主機*傳入要求您指定的主機名稱的應用程式閘道中的標頭。

例如，如果*www.contoso<i></i>.com*中指定**主機名稱**設定時，原始要求*https:/<i></i>/appgw.eastus.cloudapp.net/path1*變更為*https:/<i></i>/www.contoso.com/path1*時將要求轉送至後端伺服器。

## <a name="back-end-pool"></a>後端集區

您可以指向四種類型的後端成員中的後端集區： 特定的虛擬機器、 虛擬機器擴展集、 IP 位址/FQDN 或將 app service。 每個後端集區可以指向相同類型的多個成員。 不支援指向相同後端集區中的不同類型的成員。

建立後端集區之後，您必須將其關聯一或多個要求路由規則。 您也必須設定您的應用程式閘道上的每個後端集區的健康情況探查。 當要求路由規則條件符合時，應用程式閘道將流量轉送至對應的後端集區中狀況良好伺服器 （做為健康狀態探查所決定）。

## <a name="health-probes"></a>健康狀態探查

應用程式閘道預設監視其後端中的所有資源的健全狀況。 但我們強烈建議您建立每個後端 HTTP 設定，以取得更充分地掌控健全狀況監視的自訂探查。 若要了解如何設定自訂探查，請參閱[自訂健全狀況探查設定](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#custom-health-probe-settings)。

> [!NOTE]
> 建立自訂的健康情況探查之後，您需要將其關聯到後端 HTTP 設定。 自訂探查不會監視後端集區的健全狀況，除非明確相關聯的接聽程式對應的 HTTP 設定。

## <a name="next-steps"></a>後續步驟

既然您了解應用程式閘道元件，您可以：

- [在 Azure 入口網站中建立的應用程式閘道](quick-create-portal.md)
- [使用 PowerShell 建立應用程式閘道](quick-create-powershell.md)
- [使用 Azure CLI 建立應用程式閘道](quick-create-cli.md)
