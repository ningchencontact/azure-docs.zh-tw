---
title: 設定和驗證 VNet 或 VPN 連線
description: 設定及驗證各種 Azure VPN 和 VNet 部署的逐步指引
services: virtual-network
documentationcenter: na
author: v-miegge
manager: dcscontentpm
editor: ''
ms.assetid: 0433a4f4-b5a0-476d-b398-1506c57eafa2
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/28/2019
ms.author: kaushika
ms.openlocfilehash: fc4b649ce8d082d8d854c4c19b617c088ff3141c
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/11/2019
ms.locfileid: "70901889"
---
# <a name="configure-and-validate-vnet-or-vpn-connections"></a>設定和驗證 VNet 或 VPN 連線

本引導式逐步解說提供逐步指引，以在像是傳輸路由、VNet 對 VNet、BGP、多網站、點對站等案例中設定和驗證各種 Azure VPN 和 VNet 部署。

Azure VPN 閘道可讓您彈性地在 Azure 中安排幾乎任何類型的連線虛擬網路（VNet）拓撲：您可以在區域之間、VNet 類型（Azure Resource Manager 與之間連接 Vnet傳統），在 Azure 內或內部部署的混合式環境中，在不同的訂用帳戶中，依此類推。 

## <a name="scenario-1-vnet-to-vnet-vpn-connection"></a>案例 1：VNet 對 VNet VPN 連線

透過 VPN 將虛擬網路連線到另一個虛擬網路（VNet 對 VNet），類似于將 VNet 連線到內部部署網站位置。 這兩種連線類型都使用 VPN 閘道，來提供使用**IPsec/IKE**的安全通道。 虛擬網路可位於相同或不同的區域，以及來自相同或不同的訂用帳戶。

![包](./media/virtual-network-configure-vnet-connections/4034386_en_2.png)
 
圖 1-使用 IPsec 的 VNet 對 VNet

如果您的 Vnet 位於相同的區域中，您可能會考慮使用 VNet 對等互連來連接它們，這不會使用 VPN 閘道、增加輸送量並減少延遲，請選取**設定並驗證 vnet 對等互連**以設定 vnet 對等互連建立.

如果您的 Vnet 是使用 Azure Resource manager 部署模型所建立，請選取 [**設定和驗證 Resource Manager vnet 至 Resource Manager vnet**連線]，以設定 VPN 連線。

如果其中一個 Vnet 是使用 Azure 傳統部署模型所建立，另一個則是由 Resource Manager 所建立，選取 [**設定並驗證傳統 VNet 至 Resource Manager VNet**連線] 來設定 VPN 連線。

### <a name="configuration-1-configure-vnet-peering-for-two-vnets-in-the-same-region"></a>設定1：在相同區域中設定兩個 Vnet 的 VNet 對等互連

開始執行 Azure VNet 對等互連之前，請確定您符合下列先決條件，以設定 VNet 對等互連：

* 對等互連的虛擬網路必須存在於同一個 Azure 區域中。
* 對等互連的虛擬網路必須有不重迭的 IP 位址空間。
* 虛擬網路對等互連是介於兩個虛擬網路之間。 對等互連之間沒有任何衍生的可轉移關聯性。 例如，如果 VNetA 與 VNetB 對等互連，而 VNetB 與 VNetC 對等互連，則 VNetA 「不會」對等互連至 VNetC。

當您符合需求時，您可以遵循[建立虛擬網路對等互連教學](https://docs.microsoft.com/azure/virtual-network/virtual-network-create-peering)課程來建立及設定 VNet 對等互連。

若要檢查 VNet 對等互連設定，請使用下列方法：

1. 使用具有必要[角色和許可權](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering#roles-permissions)的帳戶登入[Azure 入口網站](https://portal.azure.com/)。
2. 在 Azure 入口網站頂端包含「搜尋資源」文字的方塊中，輸入「虛擬網路」。 當搜尋結果中出現**虛擬網路**時，按一下該項目。
3. 在出現的 [虛擬網路] 刀鋒視窗中，按一下您想要為其建立對等互連的虛擬網路。
4. 在針對您選取的虛擬網路出現的窗格中，按一下 [**設定**] 區段中的 [**對等互連**]。
5. 按一下您要檢查設定的對等互連。

![包](./media/virtual-network-configure-vnet-connections/4034496_en_1.png)
 
使用 Azure Powershell，執行命令[get-azurermvirtualnetworkpeering](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermvirtualnetworkpeering?view=azurermps-4.1.0)來取得虛擬網路對等互連，例如：

```
PS C:\Users\User1> Get-AzureRmVirtualNetworkPeering -VirtualNetworkName Vnet10-01 -ResourceGroupName dev-vnets
Name                             : LinkToVNET10-02
Id                               : /subscriptions/GUID/resourceGroups/dev-vnets/providers/Microsoft.Network/virtualNetworks/VNET10-01/virtualNetworkPeerings/LinkToVNET10-0
2
Etag                             : W/"GUID"
ResourceGroupName                : dev-vnets
VirtualNetworkName               : vnet10-01
ProvisioningState                : Succeeded
RemoteVirtualNetwork             : {
                                  "Id": "/subscriptions/GUID/resourceGroups/DEV-VNET
                                   s/providers/Microsoft.Network/virtualNetworks/VNET10-02"
                                   }
AllowVirtualNetworkAccess        : True
AllowForwardedTraffic            : False
AllowGatewayTransit              : False
UseRemoteGateways                : False
RemoteGateways                   : null
RemoteVirtualNetworkAddressSpace : null
```

### <a name="connection-type-1-connect-a-resource-manager-vnet-to-anther-resource-manager-vnet-azure-resource-manager-to-azure-resource-manager"></a>連線類型1：將 Resource Manager VNet 連線至另一個 Resource Manager VNet （Azure Resource Manager 到 Azure Resource Manager）

您可以直接設定從一個 Resource Manager VNet 到另一個 Resource Manager VNet 的連線，或設定與 IPsec 的連線。

### <a name="configuration-2-configure-vpn-connection-between-resource-manager-vnets"></a>設定2：設定 Resource Manager Vnet 之間的 VPN 連線

若要在不含 IPsec 的 Resource Manager Vnet 之間設定連線，請參閱[使用 Azure 入口網站設定 vnet 對 VNET VPN 閘道聯](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal)機。

若要設定兩個 Resource Manager Vnet 之間的 IPsec 連線，請遵循在每個 VNet[的 Azure 入口網站中建立站對站](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal)連線中的步驟1-5。

> [!Note]
> 這些步驟只適用於相同的訂用帳戶中的 VNet。 如果您的 VNet 位於不同的訂用帳戶中，則必須使用 PowerShell 來進行連線。 請參閱 [PowerShell](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-vnet-vnet-rm-ps) 文件。

### <a name="validate-vpn-connection-between-resource-manager-vnets"></a>驗證 Resource Manager Vnet 之間的 VPN 連線

![包](./media/virtual-network-configure-vnet-connections/4034493_en_2.png)

圖 4-Azure Resource Manager VNet 的傳統 VNet 連線

若要檢查您的 VPN 連線是否設定正確，請遵循下列指示：

> [!Note]
> 虛擬網路元件之後的數位（例如，下列步驟中的「Vnet 1」）會對應到 [圖 4] 中的數位。

1. 檢查已連線的 Vnet 中是否有重迭的位址空間。

   > [!Note]
   > Vnet 1 和 Vnet 6 中不能有重迭的位址空間。 

2. 確認在**Connection 物件**4 中正確定義了 Azure Resource Manager Vnet 1 位址範圍。
3. 確認在**Connection 物件**3 中正確定義了 Azure Resource Manager Vnet 6 位址範圍。
4. 確認預先共用金鑰符合兩個連線物件3和4。
5. 確認**連線物件**4 中的 Azure Resource Manager Vnet 閘道 2 VIP 已正確定義。
6. 確認在**Connection 物件**3 中正確定義了 Azure Resource Manager Vnet 閘道 5 VIP。

### <a name="connection-type-2-connect-a-classic-vnet-to-a-resource-manager-vnet"></a>連線類型2：將傳統 VNet 連接到資源管理員 VNet

您可以在不同訂用帳戶和不同區域中的 VNet 之間建立連線。 只要您已將閘道類型設定為路由型，您也可以將已有連線的 Vnet 連線到內部部署網路。

若要設定傳統 VNet 與 Resource Manager VNet 之間的連線，請參閱[使用 Azure 入口網站從不同的部署模型連接虛擬網路](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-connect-different-deployment-models-portal)，以取得詳細資訊。

![包](./media/virtual-network-configure-vnet-connections/4034389_en_2.png)

圖 5-Azure Resource Manager VNet 的傳統 VNet 連線

若要在將傳統 VNet 連線至 Azure Resource Manager VNet 時檢查設定，請遵循下列指示：

> [!Note]
> 在下列步驟中，虛擬網路元件（例如「Vnet 1」）之後的數位會對應到 [圖 5] 中的數位。

1. 檢查已連線的 Vnet 中是否有重迭的位址空間。

   > [!Note]
   > Vnet 1 和 Vnet 6 中不能有重迭的位址空間

2. 確認在傳統區域網路定義3中，已正確定義 Azure Resource Manager VNet 6 位址範圍。
3. 確認在 Azure Resource Manager**連線物件**4 中，已正確定義傳統 VNet 1 位址範圍。
4. 確認在 Azure Resource Manager**連線物件**4 中，已正確定義傳統 VNet 閘道 2 VIP。
5. 確認在傳統**區域網路定義**3 中，已正確定義 Azure Resource Manager VNet 閘道 5 VIP。
6. 確認兩個已連線的 Vnet 上的預先共用金鑰是否相符：
   1. 傳統 Vnet-區域網路定義3
   2. Azure Resource Manager Vnet-連線物件4

## <a name="scenario-2-point-to-site-vpn-connection"></a>案例 2：點對站 VPN 連線

點對站 (P2S) 設定可讓您建立從個別的用戶端電腦到虛擬網路的安全連線。 當您想要從遠端位置 (例如從住家或會議) 連線至 VNet 時，或只有幾個需要連線至虛擬網路的用戶端時，點對站連線是很實用的解決方案。 使用原生 Windows VPN 用戶端從用戶端電腦起始 P2S VPN 連線。 連線用戶端會使用憑證進行驗證。

![包](./media/virtual-network-configure-vnet-connections/4034387_en_3.png)

圖 2-點對站連線

點對站連線不需要 VPN 裝置。 P2S 會建立透過 SSTP (安全通訊端通道通訊協定) 的 VPN 連線。 您可以使用不同的部署工具和部署模型，將點對站連線連接到 VNet：

* [使用 Azure 入口網站設定 VNet 的點對站連線](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal)
* [使用 Azure 入口網站（傳統）設定 VNet 的點對站連線](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-classic-azure-portal)
* [使用 PowerShell 設定 VNet 的點對站連線](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps)

### <a name="validate-your-point-to-site-connections"></a>驗證您的點對站連線

疑難排解文章[：Azure 點對站連線問題](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems)會逐步解說點對站連接的常見問題。

## <a name="scenario-3-multi-site-vpn-connection"></a>案例 3：多網站 VPN 連線

您可以將站對站（S2S）連線新增至已有 S2S 連線、點對站連線或 VNet 對 VNet 連線的 VNet，這種連接通常稱為**多網站**設定。 

![包](./media/virtual-network-configure-vnet-connections/4034497_en_2.png)

圖 3-多網站連線

Azure 目前適用于兩種部署模型：Resource Manager 和傳統。 這兩個模型彼此不完全相容。 若要使用不同的模型設定**多網站**連線，請參閱下列文章：

* [將站對站連線新增至具有現有 VPN 閘道連線的 VNet](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal)
* [將站對站連線新增至具有現有 VPN 閘道連線的 VNet （傳統）](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-multi-site)

> [!Note]
> 這些步驟不適用於 ExpressRoute 和站對站並存連線設定。 如需共存連線的詳細資訊，請參閱[ExpressRoute/S2S](https://docs.microsoft.com/azure/expressroute/expressroute-howto-coexist-resource-manager)並存連線。

## <a name="scenario-4-configure-transit-routing"></a>案例 4：設定傳輸路由

可轉移路由是一種特定的路由案例，您可以在其中連線到「菊輪鍊」拓撲中的多個網路。 此路由可讓「連鎖」任一端的 Vnet 資源，透過中的 Vnet 彼此通訊。 如果沒有可轉移的路由，透過中樞對等互連的網路或裝置就無法彼此連線。

### <a name="configuration-1-configure-transit-routing-in-a-point-to-site-connection"></a>設定1：設定點對站連線中的傳輸路由

在此案例中，您會設定 VNetA 與 VNetB 之間的站對站 VPN 連線，同時也請設定點對站 VPN，讓用戶端連線到 VNetA 的閘道。 然後，您想要啟用「點對站」用戶端的傳輸路由，以連線到 VNetB，而這會通過 VNetA。 在 VNetA 與 VNetB 之間的站對站 VPN 上啟用 BGP 時，支援此案例。 如需詳細資訊，請參閱[關於點對站 VPN 路由](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-point-to-site-routing)。

### <a name="configuration-2-configure-transit-routing-in-an-expressroute-connection"></a>設定2：在 ExpressRoute 連線中設定傳輸路由

Microsoft Azure ExpressRoute 可讓您透過連線提供者所提供的專用私人連線，將內部部署網路擴充至 Microsoft 雲端。 透過 ExpressRoute，您可以建立 Microsoft 雲端服務的連線，例如 Microsoft Azure、Office 365 和 Dynamics 365。  如需詳細資訊，請參閱[ExpressRoute 總覽](https://docs.microsoft.com/azure/expressroute/expressroute-introduction)。

![包](./media/virtual-network-configure-vnet-connections/4034395_en_1.png)

圖 6-ExpressRoute 「私用對等互連」與 Azure Vnet 的連線

> [!Note]
> 我們建議，如果 VNetA 和 VNetB 位於相同的地緣政治區域，而您將[這兩個 vnet 連結到 ExpressRoute 線路](https://docs.microsoft.com/azure/expressroute/expressroute-howto-linkvnet-arm)，而不是設定可轉移路由。 如果您的 Vnet 位於不同的地緣政治區域，您也可以直接將其連結至您的線路（如果您有[ExpressRoute Premium](https://docs.microsoft.com/azure/expressroute/expressroute-faqs#expressroute-premium)）。 

如果您有 ExpressRoute 和站對站共存，則不支援傳輸路由。 如需詳細資訊，請參閱[設定 ExpressRoute 和站對站並存連線以取得詳細資訊](https://docs.microsoft.com/azure/expressroute/expressroute-howto-coexist-resource-manager)。

如果您已啟用 ExpressRoute 將您的區域網路連線到 Azure 虛擬網路，您可以在您想要具有可轉移路由的 Vnet 之間啟用 VNet 對等互連。 若要允許您的區域網路連線到遠端 VNet，您必須設定[虛擬網路對等互連](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#gateways-and-on-premises-connectivity)。 

> [!Note]
> VNet 對等互連僅適用于相同區域中的 Vnet。

若要檢查您是否已設定 VNet 對等互連的傳輸路由，請遵循下列指示：

1. 使用具有必要[角色和許可權](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering#roles-permissions)的帳戶登入[Azure 入口網站](https://portal.azure.com/)。
2. [建立 VNet a 和 B 之間的對等互連](https://docs.microsoft.com/azure/virtual-network/virtual-network-create-peering)，如先前圖表中所示（圖6）。 
3. 在針對您選取的虛擬網路出現的窗格中，按一下 [**設定**] 區段中的 [**對等互連**]。
4. 按一下您要查看的對等互連和設定 **，以驗證**您已在連接到 expressroute 線路的 VNetA 上啟用 [**允許閘道傳輸**]，並在未連線到 expressroute 的遠端 VNetB 上**使用遠端閘道**專線.

### <a name="configuration-3-configure-transit-routing-in-a-vnet-peering-connection"></a>設定3：在 VNet 對等互連連線中設定傳輸路由

當虛擬網路已對等互連時，您也可以將對等互連虛擬網路中的閘道設定為內部部署網路的傳輸點。 若要在 VNet 對等互連中設定傳輸路由，請檢查[虛擬網路對虛擬網路](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-vnet-vnet-rm-ps?toc=/azure/virtual-network/toc.json)連線。

> [!Note]
> 透過不同部署模型建立的虛擬網路之間的對等互連關聯性不支援閘道傳輸。 對等互連關聯性中的兩個虛擬網路都必須透過 Resource Manager 建立，閘道傳輸才能運作。

若要檢查您是否已設定 VNet 對等互連的傳輸路由，請遵循下列指示：

1. 使用具有必要[角色和許可權](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering#roles-permissions)的帳戶登入[Azure 入口網站](https://portal.azure.com/)。
2. 在包含 Azure 入口網站頂端的 搜尋資源 方塊中，輸入*虛擬網路*。 當搜尋結果中出現**虛擬網路**時，按一下該項目。
3. 在出現的 [**虛擬網路**] 分頁中，按一下您要檢查對等互連設定的虛擬網路。
4. 在針對您選取的虛擬網路出現的窗格中，按一下 [**設定**] 區段中的 [**對等互連**]。
5. 按一下您要查看的對等互連，並驗證您已啟用 [**允許閘道傳輸**] 和 [在**設定下** **使用遠端閘道**]。

![包](./media/virtual-network-configure-vnet-connections/4035414_en_1.png)

### <a name="configuration-4-configure-transit-routing-in-a-vnet-to-vnet-connection"></a>設定4：在 VNet 對 VNet 連線中設定傳輸路由

若要設定 Vnet 之間的傳輸路由，您必須使用 Resource Manager 部署模型和 PowerShell，在所有中繼 VNet 對 VNet 連線上啟用 BGP。 如需指示，請參閱[如何使用 PowerShell 在 AZURE VPN 閘道上設定 BGP](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-bgp-resource-manager-ps)。

使用傳統部署模型可以透過 Azure VPN 閘道傳輸流量，但依賴網路設定檔中靜態定義的位址空間。 使用傳統部署模型的 Azure 虛擬網路和 VPN 閘道尚不支援 BGP。 若沒有 BGP，手動定義傳輸位址空間會造成錯誤，因此不建議您這麼做。

> [!Note]
> 傳統的 VNet 對 VNet 連線是使用 Azure 入口網站（傳統），或在傳統入口網站中使用網路設定檔來設定。 您無法透過 Azure Resource Manager 部署模型或 Azure 入口網站來建立或修改傳統虛擬網路。 如需傳統 Vnet 傳輸路由的詳細資訊，請參閱[使用 VPN （V1）在 AZURE ARM 中的中樞 & 輪輻、菊輪鍊和全網格 VNET 拓撲](https://blogs.msdn.microsoft.com/igorpag/2015/10/01/hubspoke-daisy-chain-and-full-mesh-vnet-topologies-in-azure-arm-using-vpn-v1/)。

### <a name="configuration-5-configure-transit-routing-in-a-site-to-site-connection"></a>設定5：設定站對站連線中的傳輸路由

若要設定內部部署網路與具有站對站連線的 VNet 之間的傳輸路由，您必須使用 Resource Manager 部署模型和 PowerShell，在所有中繼站對站連線上啟用 BGP，請參閱[如何設定](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-bgp-resource-manager-ps)如需指示，請使用 PowerShell 來取得 AZURE VPN 閘道上的 BGP。

使用傳統部署模型可以透過 Azure VPN 閘道傳輸流量，但依賴網路設定檔中靜態定義的位址空間。 使用傳統部署模型的 Azure 虛擬網路和 VPN 閘道尚不支援 BGP。 若沒有 BGP，手動定義傳輸位址空間會造成錯誤，因此不建議您這麼做。

> [!Note]
> 傳統的站對站連線是使用 Azure 入口網站（傳統），或在傳統入口網站中使用網路設定檔來設定。 您無法透過 Azure Resource Manager 部署模型或 Azure 入口網站來建立或修改傳統虛擬網路。 如需傳統 Vnet 傳輸路由的詳細資訊，請參閱[使用 VPN （V1）在 AZURE ARM 中的中樞 & 輪輻、菊輪鍊和全網格 VNET 拓撲](https://blogs.msdn.microsoft.com/igorpag/2015/10/01/hubspoke-daisy-chain-and-full-mesh-vnet-topologies-in-azure-arm-using-vpn-v1/)。

## <a name="scenario-5-configure-bgp-for-a-vpn-gateway"></a>案例 5：設定適用於 VPN 閘道的 BGP

BGP 是網際網路中使用的標準路由通訊協定，可交換兩個或多個網路之間的路由和可連線性資訊。 在 Azure 虛擬網路的內容中使用 BGP 時，BGP 會啟用 Azure VPN 閘道和您的內部部署 VPN 裝置（稱為 BGP 對等互連或鄰近專案）。 它們會交換「路由」，以通知這兩個閘道的可用性和可連線性，讓這些前置詞通過所涉及的閘道或路由器。 BGP 也可以傳播從一個 BGP 對等互連到所有其他 BGP 對等所識別的 BGP 閘道，來啟用多個網路之間的傳輸路由。 如需詳細資訊，請參閱[BGP 與 AZURE VPN 閘道的總覽](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-bgp-overview)。

### <a name="configure-bgp-for-a-vpn-connection"></a>設定 VPN 連線的 BGP

若要設定使用 BGP 的 VPN 連線，請參閱[如何使用 PowerShell 在 AZURE VPN 閘道上設定 BGP](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-bgp-resource-manager-ps)。

> [!Note]
> 1. 藉由為其建立 AS 號碼，在虛擬網路閘道上啟用 BGP。 基本閘道不支援 BGP。 若要檢查閘道的 SKU，請移至 Azure 入口網站中 [VPN 閘道] 分頁的 [總覽] 區段。 如果您的 SKU 是**基本**的，您必須將 sku （[調整閘道大小](https://docs.microsoft.com/powershell/module/azurerm.network/resize-azurermvirtualnetworkgateway?view=azurermps-4.1.0&viewFallbackFrom=azurermps-4.0.0)）變更為**VpnGw1** sku。 檢查 SKU 會導致20-30 分鐘的停機時間。 一旦閘道具有正確的 SKU，就可以透過[Reset-azurermvirtualnetworkgateway](https://docs.microsoft.com/powershell/module/azurerm.network/set-azurermvirtualnetworkgateway?view=azurermps-3.8.0) PowerShell 命令來新增 as-let。 在您設定 AS 號碼之後，將會自動提供閘道的 BGP 對等 IP。
> 2. LocalNetworkGateway 必須以 AS 號碼和 BGP 對等位址**手動**提供。 您可以使用[set-azurermlocalnetworkgateway](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermlocalnetworkgateway?view=azurermps-4.1.0)或[設定 set-azurermlocalnetworkgateway](https://docs.microsoft.com/powershell/module/azurerm.network/set-azurermlocalnetworkgateway?view=azurermps-4.1.0) PowerShell 命令-let 來設定**ASN**和 **-BgpPeeringAddress**值。 有些 AS 數位會保留給 azure，您無法如[關於 BGP 與 azure VPN 閘道](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-bgp-overview#bgp-faq)中所述使用它們。
> 3. 最後，連線物件必須啟用 BGP。 您可以透過[get-azurermvirtualnetworkgatewayconnection](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermvirtualnetworkgatewayconnection?view=azurermps-4.1.0)或[set-get-azurermvirtualnetworkgatewayconnection](https://docs.microsoft.com/powershell/module/azurerm.network/set-azurermvirtualnetworkgatewayconnection?view=azurermps-4.1.0)，將 **-EnableBGP**值設定為`$True` 。

### <a name="validate-the-bgp-configuration"></a>驗證 BGP 設定

若要檢查 bgp 是否已正確設定，您可以執行 Cmdlet `get-AzureRmVirtualNetworkGateway`和`get-AzureRmLocalNetworkGateway`，然後您會在 BgpSettingsText 元件中看到 bgp 相關的輸出。 例如: BgpSettingsText:

```
{

"Asn": AsnNumber,

"BgpPeeringAddress": "IP address",

"PeerWeight": 0

}
```

## <a name="scenario-6-highly-available-active-active-vpn-connection"></a>案例 6：高可用性主動-主動 VPN 連線

主動-主動和作用中-待命閘道之間的重要差異：

* 您必須建立兩個具有兩個公用 IP 位址的閘道 IP 設定
* 您必須設定*EnableActiveActiveFeature*旗標
* 閘道 SKU 必須是 VpnGw1、VpnGw2、VpnGw3

若要達到跨單位和 VNet 對 VNet 連線能力的高可用性，您應該部署多個 VPN 閘道，並在您的網路和 Azure 之間建立多個平行連線。 如需連線能力選項和拓撲的總覽，請參閱[高可用性跨單位和 vnet 對 vnet 連線能力](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-highlyavailable)。

若要建立主動-主動跨單位和 VNet 對 VNet 連線，請遵循[使用 AZURE Vpn 閘道設定 active-active S2S VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-activeactive-rm-powershell)連線中的指示，以主動/主動模式設定 Azure vpn 閘道。

> [!Note]  
> 1. 當您將位址新增到局域網路閘道以啟用 BGP 時，主動對主動模式*只會新增 bgp 對等的/32 位址*。 如果您新增更多位址，則會將它們視為靜態路由，並優先于 BGP 路由。
> 2. 您必須針對連接到 Azure 的內部部署網路使用不同的 BGP Asn。 （如果是相同的，您必須變更 VNet ASN，前提是您的內部部署 VPN 裝置已經使用 ASN 來與其他 BGP 鄰近專案對等）。

## <a name="scenario-7-change-an-azure-vpn-gateway-type-after-deployment"></a>案例7：在部署後變更 Azure VPN 閘道類型

您無法將 Azure VNet 閘道類型從以原則為基礎的變更為路由式或其他方式。 在 won'tbe 保留 IP 位址和預先共用金鑰（PSK）之後，您必須刪除閘道。 接著，您可以建立所需類型的新閘道。 若要刪除並建立閘道，請遵循下列步驟：

1. 刪除與原始閘道相關聯的任何連接。
2. 使用 Azure 入口網站、PowerShell 或傳統 PowerShell 來刪除閘道： 
   * [使用 Azure 入口網站刪除虛擬網路閘道](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-delete-vnet-gateway-portal)
   * [使用 PowerShell 刪除虛擬網路閘道](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-delete-vnet-gateway-powershell)
   * [使用 PowerShell 刪除虛擬網路閘道（傳統）](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-delete-vnet-gateway-classic-powershell)
3. 依照[建立 vpn 閘道](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal#a-namevnetgatewaya4-create-the-vpn-gateway)中的步驟來建立所需類型的新閘道，並完成 VPN 設定。

> [!Note]
> 此程式大約需要60分鐘的時間。

## <a name="next-steps"></a>後續步驟

* [為 Azure VM 之間的連線問題疑難排解](https://docs.microsoft.com/azure/virtual-network/virtual-network-troubleshoot-connectivity-problem-between-vms)

