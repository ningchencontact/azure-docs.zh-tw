---
title: 透過 Azure 虛擬 WAN 中的 ExpressRoute 私人對等互連建立站對站 VPN 連線 |Microsoft Docs
description: 在本教學課程中，您將瞭解如何使用 Azure 虛擬 WAN，透過 ExpressRoute 私用對等互連來建立站對站 VPN 連線。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: article
ms.date: 10/11/2019
ms.author: cherylmc
Customer intent: I want to connect my on-premises networks to my VNets using S2S VPN connection over my ExpressRoute private peering using Azure Virtual WAN.
ms.openlocfilehash: 6272d6fe6f8c35c06a8121e10be2dd5a2e5512a8
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2019
ms.locfileid: "73515027"
---
# <a name="create-a-site-to-site-vpn-connection-over-expressroute-private-peering-using-azure-virtual-wan"></a>使用 Azure 虛擬 WAN，透過 ExpressRoute 私人對等互連建立站對站 VPN 連線

本文說明如何使用虛擬 WAN，透過 ExpressRoute 線路的私人對等互連，建立從內部部署網路到 Azure 的 IPsec/IKE VPN 連線。 這可以透過 ExpressRoute 提供內部部署網路與 Azure 虛擬網路之間的加密傳輸，而不需要透過公用網際網路或使用公用 IP 位址。

## <a name="topology-and-routing"></a>拓撲和路由

下圖顯示透過 ExpressRoute 私用對等互連連線的 VPN 範例：

![透過 ExpressRoute 的 VPN](./media/vpn-over-expressroute/vwan-vpn-over-er.png)

此圖顯示透過 ExpressRoute 私人對等互連連線到 Azure 中樞 VPN 閘道的內部部署網路內的網路。 連線性的建立非常簡單：

1. 使用 ExpressRoute 線路和私用對等互連建立 ExpressRoute 連線能力
2. 依照本檔中的說明建立 VPN 連線能力

此設定的重要層面是透過 ExpressRoute 和 VPN 路徑，在內部部署網路與 Azure 之間路由傳送。

### <a name="traffic-from-on-premises-networks-to-azure"></a>從內部部署網路到 Azure 的流量

對於從內部部署網路到 Azure 的流量，Azure 前置詞（包括虛擬中樞和連線到中樞的所有輪輻虛擬網路）都會透過 ExpressRoute 私人對等互連 BGP 和 VPN BGP 來公告。 這會產生兩個從內部部署網路到 Azure 的網路路由（路徑）;一個透過 IPsec 保護的路徑，而一個直接透過 ExpressRoute**而不**是 ipsec 保護。 為確保加密會套用至通訊，您必須確定在圖表中，已連接 VPN 的網路，透過內部部署 VPN 閘道的 Azure 路由優於直接 ExpressRoute 路徑。

### <a name="traffic-from-azure-to-on-premises-networks"></a>從 Azure 到內部部署網路的流量

相同的需求適用于從 Azure 到內部部署網路的流量。 若要確保在直接 ExpressRoute 路徑（不含 IPsec）上偏好使用 IPsec 路徑，您有兩個選項：

- 針對 VPN 連線的網路，在 VPN BGP 會話上公告更具體的首碼。 您可以透過 ExpressRoute 私用對等互連來公告包含「VPN 連線網路」的較大範圍，然後在 VPN BGP 會話中更具體的範圍。 例如，透過 ExpressRoute 公告 10.0.0.0/16 和透過 VPN 的 10.0.1.0/24。

- 公告 VPN 和 ExpressRoute 的斷續首碼。 如果 VPN 連線的網路範圍與其他 ExpressRoute 連線網路不相交，您可以分別在 VPN 和 ExpressRoute BGP 會話中通告首碼。 例如，透過 ExpressRoute 公告 10.0.0.0/24 和透過 VPN 的 10.0.1.0/24。

在這兩個範例中，Azure 會透過 VPN 連線將流量傳送至 10.0.1.0/24，而不是直接透過 ExpressRoute 而不是 VPN 保護。

> [!WARNING]
> 如果您在 ExpressRoute 和 VPN 連線上公告**相同**的首碼，Azure 將**直接使用 expressroute 路徑，而不需要 VPN 保護**。
>

## <a name="before-you-begin"></a>開始之前

[!INCLUDE [Before you begin](../../includes/virtual-wan-tutorial-vwan-before-include.md)]

## <a name="openvwan"></a>1. 使用閘道建立虛擬 WAN 和中樞

繼續進行之前，必須先備妥下列 Azure 資源和對應的內部部署設定：

1. Azure 虛擬 WAN
2. 具有[ExpressRoute 網](virtual-wan-expressroute-portal.md)關和[VPN 閘道](virtual-wan-site-to-site-portal.md)的虛擬 WAN 中樞

如需建立 Azure 虛擬 WAN 和具有 ExpressRoute 關聯的中樞，請參閱[使用 Azure 虛擬 Wan 建立 ExpressRoute 關聯](virtual-wan-expressroute-portal.md)，然後[使用 Azure 虛擬 wan 建立站對站](virtual-wan-site-to-site-portal.md)連線，以進行建立 VPN 的步驟虛擬 WAN 中的閘道。

## <a name="site"></a>2. 建立內部部署網路的網站

網站資源與虛擬 WAN 的非 ExpressRoute VPN 網站相同。 要注意的重點是，內部部署 VPN 裝置的 IP 位址現在可以是私人 IP 位址，或是內部部署網路中的公用 IP 位址，可透過在步驟1中建立的 ExpressRoute 私用對等互連來存取。

> [!NOTE]
> 內部部署 VPN 裝置 IP 位址必須是透過 Azure ExpressRoute 私用對等互連向虛擬 WAN 中樞通告的位址首碼的一部分。
>

1. 流覽至瀏覽器上的 Azure 入口網站。 按一下您所建立的 WAN。 在 [WAN] 頁面的 [連線**能力**] 底下，按一下 [ **vpn 網站**] 以開啟 [vpn 網站] 頁面。

2. 在 [VPN 網站] 頁面上，按一下 [+ 建立網站]。

3. 在 [建立網站] 頁面上，填寫下列欄位：

   * **訂用帳戶** - 請確認訂用帳戶。
   * **資源群組**-選取或建立您想要使用的資源群組。
   * **區域**-VPN 網站資源的 Azure 區域。
   * **名稱** - 您要用來參考內部部署網站的名稱。
   * **裝置廠商**-內部部署 VPN 裝置的廠商。
   * **邊界閘道協定**-如果您的內部部署網路使用 BGP，請選取 [啟用]。
   * **私人位址空間** - 這是位於內部部署網站上的 IP 位址空間。 目的地為此位址空間的流量會透過 VPN 閘道路由傳送至內部部署網路。
   * **中樞**-選取一或多個中樞來連接此 VPN 網站。 選取的中樞必須已經建立 VPN 閘道。

4. 按 [**下一步]：** VPN 連結設定 > 的連結：

   * [**連結名稱**]-您想要用來參考此連接的名稱。
   * [**提供者名稱**]-此網站的網際網路服務提供者名稱。 如果是 ExpressRoute 內部部署網路，則為 ExpressRoute 服務提供者的名稱。
   * **速度**-網際網路服務連結或 ExpressRoute 線路的速度。
   * **IP 位址**-位於內部部署網站上的 VPN 裝置的公用 IP 位址。 或者，如果是 ExpressRoute 內部部署，則是透過 ExpressRoute 的 VPN 裝置的私人 IP 位址。

   如果已啟用 BGP，它會套用到在 Azure 中為此網站建立的所有連線。 在虛擬 WAN 上設定 BGP 就等同於在 Azure VPN 閘道上設定 BGP。 您的內部部署 BGP 對等互連位址*不得*與您的 vpn 到裝置的 IP 位址或 vpn 網站的 VNet 位址空間相同。 在 VPN 裝置上，請針對 BGP 對等互連 IP 使用不同的 IP 位址。 它可以是指派給裝置上的回送介面的位址。 但「不能」是 APIPA (169.254.x.x) 位址。 在代表位置的對應本機網路閘道中指定這個位址。 如需 BGP 必要條件，請參閱[關於 BGP 與 Azure VPN 閘道](../vpn-gateway/vpn-gateway-bgp-overview.md)。

5. 按 **[下一步]： [流覽 + 建立 >]** 以檢查設定值，並建立 VPN 網站。 如果您選取 [**中樞**] 來連線，則會在內部部署網路與中樞 VPN 閘道之間建立連線。

## <a name="hub"></a>3. 將 VPN 連線設定更新為使用 ExpressRoute

建立 VPN 網站並聯機到中樞之後，請使用下列步驟來設定連線以使用 ExpressRoute 私用對等互連：

1. 返回 [虛擬 WAN 資源] 頁面，然後按一下 [中樞] 資源。 或從 VPN 網站流覽至已連線的中樞。

2. 在 [連線**能力**] 底下，按一下 **[VPN （站對站）** ]

3. 按一下 [...]在 VPN 網站上透過 ExpressRoute，然後選取 [**編輯此中樞的 VPN**連線]

4. 在 [**使用 Azure 私人 IP 位址**] 上選取 [是]。 設定會將中樞 VPN 閘道設為在此連線的閘道上，使用中樞位址範圍內的私人 IP 位址，而不是公用 IP 位址。 這可確保來自內部部署網路的流量會流經 ExpressRoute 私人對等互連路徑，而不是針對此 VPN 連線使用公用網際網路。 下列螢幕擷取畫面顯示 [設定] 視窗。

   ![VPN 連線設定](./media/vpn-over-expressroute/vpn-link-configuration.png)
   
5. 按一下 [儲存]。

儲存之後，中樞 VPN 閘道會使用 VPN 閘道上的私人 IP 位址，透過 ExpressRoute 建立與內部部署 VPN 裝置的 IPsec/IKE 連線。

## <a name="associate"></a>4. 取得中樞 VPN 閘道私人 IP 位址

下載 VPN 裝置設定，以取得中樞 VPN 閘道的私人 IP 位址。 這些是設定內部部署 VPN 裝置的必要項。

1. **在您**中樞的頁面上，按一下 [連線] 底下的 [ **VPN （站對站）** ]

2. 在 [總覽] 頁面頂端，按一下 [**下載 VPN**設定]。Azure 會在資源群組「microsoft-網路-[位置] ' 中建立儲存體帳戶，其中 location 是 WAN 的位置。 將組態套用至您的 VPN 裝置之後，就可以刪除此儲存體帳戶。

3. 檔案建立完成之後，您可以按一下連結來下載。

4. 將組態套用至您的 VPN 裝置。

### <a name="understanding-the-vpn-device-configuration-file"></a>了解 VPN 裝置組態檔

裝置組態檔包含設定內部部署 VPN 裝置時要使用的設定。 當您檢視此檔案時，請注意下列資訊：

* **vpnSiteConfiguration -** 此區段表示網站連線至虛擬 WAN 時設定的裝置詳細資料。 其中包含分支裝置的名稱和公用 IP 位址。
* **vpnSiteConnections -** 此區段提供與下列設定有關的資訊：

    * 虛擬中樞 VNet 的**位址空間**<br/>範例：
           ```
           "AddressSpace":"10.51.230.0/24"
           ```
    * 連線至中樞的 Vnet **位址空間**<br>範例：
           ```
           "ConnectedSubnets":["10.51.231.0/24"]
            ```
    * 虛擬中樞 Vpngateway 的 **IP 位址**。 由於 Vpngateway 的每個連線都由「主動對主動」設定中的 2 個通道組成，因此您會看到此檔案中列出這兩個 IP 位址。 在此範例中，您會看到每個網站的 "Instance0" 和 "Instance1"，而且它們是私人 IP 位址，而不是公用 IP 位址。<br>範例：
           ``` 
           "Instance0":"10.51.230.4"
           "Instance1":"10.51.230.5"
           ```
    * **Vpngateway 連線設定詳細資料**，例如 BGP、預先共用金鑰等等。PSK 是自動為您產生的預先共用金鑰。 您隨時都可以在自訂 PSK 的 [概觀] 頁面中編輯連線。
  
### <a name="example-device-configuration-file"></a>範例裝置組態檔

```
[{
      "configurationVersion":{
        "LastUpdatedTime":"2019-10-11T05:57:35.1803187Z",
        "Version":"5b096293-edc3-42f1-8f73-68c14a7c4db3"
      },
      "vpnSiteConfiguration":{
        "Name":"VPN-over-ER-site",
        "IPAddress":"172.24.127.211",
        "LinkName":"VPN-over-ER"
      },
      "vpnSiteConnections":[{
        "hubConfiguration":{
          "AddressSpace":"10.51.230.0/24",
          "Region":"West US 2",
          "ConnectedSubnets":["10.51.231.0/24"]
        },
        "gatewayConfiguration":{
          "IpAddresses":{
            "Instance0":"10.51.230.4",
            "Instance1":"10.51.230.5"
          }
        },
        "connectionConfiguration":{
          "IsBgpEnabled":false,
          "PSK":"abc123",
          "IPsecParameters":{"SADataSizeInKilobytes":102400000,"SALifeTimeInSeconds":3600}
        }
      }]
    },
    {
      "configurationVersion":{
        "LastUpdatedTime":"2019-10-11T05:57:35.1803187Z",
        "Version":"fbdb34ea-45f8-425b-9bc2-4751c2c4fee0"
      },
      "vpnSiteConfiguration":{
        "Name":"VPN-over-INet-site",
        "IPAddress":"13.75.195.234",
        "LinkName":"VPN-over-INet"
      },
      "vpnSiteConnections":[{
        "hubConfiguration":{
          "AddressSpace":"10.51.230.0/24",
          "Region":"West US 2",
          "ConnectedSubnets":["10.51.231.0/24"]
        },
        "gatewayConfiguration":{
          "IpAddresses":{
            "Instance0":"51.143.63.104",
            "Instance1":"52.137.90.89"
          }
        },
        "connectionConfiguration":{
          "IsBgpEnabled":false,
          "PSK":"abc123",
          "IPsecParameters":{"SADataSizeInKilobytes":102400000,"SALifeTimeInSeconds":3600}
        }
      }]
}]
```

### <a name="configuring-your-vpn-device"></a>設定 VPN 裝置

如果需要設定裝置的指示，您可以使用 [VPN 裝置組態指令碼頁面](~/articles/vpn-gateway/vpn-gateway-about-vpn-devices.md#configscripts)中的指示，並留意下列注意事項：

* VPN 裝置頁面上的指示不是針對虛擬 WAN 所撰寫，但您也可以從組態檔使用虛擬 WAN 的值，手動設定 VPN 裝置。 
* 適用於 VPN 閘道的可下載裝置組態指令碼不適用於虛擬 WAN，因為組態不同。
* 新的虛擬 WAN 可以支援 IKEv1 和 IKEv2。
* 虛擬 WAN 只能使用路由式 VPN 裝置和裝置指示。

## <a name="viewwan"></a>5. 查看您的虛擬 WAN

1. 瀏覽至虛擬 WAN。

2. 在 [概觀] 頁面中，地圖上的每個點都代表著中樞。 暫留在任一點上，即可檢視中樞健康情況摘要。

3. 在 [中樞與連線] 區段中，您可以檢視中樞狀態、網站、區域、VPN 連線狀態和輸入與輸出位元組。

## <a name="viewhealth"></a>6. 查看您的資源健康狀態

1. 瀏覽至您的 WAN。

2. 在 WAN 頁面上的 [支援 + 疑難排解] 區段中，按一下 [健康情況] 並檢視您的資源。

## <a name="connectmon"></a>7. 監視連接

建立連線以監視 Azure VM 和遠端站台之間的通訊。 如需有關如何設定連線監視的資訊，請參閱[監視網路通訊](~/articles/network-watcher/connection-monitor.md)。 來源欄位是 Azure 中的 VM IP，目的地 IP 是位址是網站 IP。

## <a name="cleanup"></a>8. 清除資源

您可以使用 [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) 來移除不再需要的資源群組，以及其所包含的所有資源。 將 "myResourceGroup" 取代為您的資源群組名稱，然後執行下列 PowerShell 命令：

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>後續步驟

本文可協助您使用虛擬 WAN，透過 ExpressRoute 私人對等互連建立 VPN 連線。 若要深入瞭解虛擬 WAN 和其他相關功能，請參閱[虛擬 Wan 總覽](virtual-wan-about.md)頁面。
