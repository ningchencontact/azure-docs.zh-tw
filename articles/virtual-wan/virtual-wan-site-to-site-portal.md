---
title: 使用 Azure 虛擬 WAN 與 Azure 建立站對站連線 |Microsoft Docs
description: 在本教學課程，您可了解如何使用 Azure 虛擬 WAN 來與 Azure 建立站對站的 VPN 連線。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 07/13/2018
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to connect my local site to my VNets using Virtual WAN and I don't want to go through a Virtual WAN partner.
ms.openlocfilehash: ea36a3d4a2471cee6a18d70275aaf2e83ffc6f39
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/19/2018
ms.locfileid: "39159646"
---
# <a name="tutorial-create-a-site-to-site-connection-using-azure-virtual-wan-preview"></a>教學課程：使用 Azure 虛擬 WAN 來建立站對站連線 (預覽)

本教學課程會示範如何使用虛擬 WAN，透過 IPsec/IKE (IKEv2) VPN 連線來與 Azure 中的資源連線。 此類型的連線需要位於內部部署的 VPN 裝置，且您已對該裝置指派對外開放的公用 IP 位址。 如需有關虛擬 WAN 的詳細資訊，請參閱[虛擬 WAN 概觀](virtual-wan-about.md)

> [!NOTE]
> 如果您有許多網站，通常會使用[虛擬 WAN 夥伴](https://aka.ms/virtualwan)來建立此組態。 不過，如果您慣用網路並且知道如何設定自己的 VPN 裝置，可以自行建立此組態。
>

![虛擬 WAN 的圖表](./media/virtual-wan-about/virtualwan.png)

在本教學課程中，您了解如何：

> [!div class="checklist"]
> * 建立 WAN
> * 建立網站
> * 建立中樞
> * 將中樞連線至網站
> * 將 VNet 連線至中樞
> * 下載並套用 VPN 裝置組態
> * 檢視您的虛擬 WAN
> * 檢視資源健康情況
> * 監視連線

> [!IMPORTANT]
> Azure 虛擬 WAN 目前為受控的公開預覽版本。 若要使用虛擬 WAN，您必須[註冊預覽版](#enroll)。
>
> 此公開預覽版是在沒有服務等級協定的情況下提供，不得用於生產工作負載。 可能不支援特定功能、可能已經限制功能，或者可能無法在所有 Azure 位置提供使用。 如需詳細資訊，請參閱 [Microsoft Azure 預覽專用的補充使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="before-you-begin"></a>開始之前

在開始設定之前，請確認您已符合下列條件：

* 確定您有相容的路由式 VPN 裝置能使用 IKEv2，以及有能夠進行設定的人員。 如果您使用虛擬 WAN 夥伴，此組態設定會自動建立，不需要擔心不知道如何手動設定裝置。
* 確認您的 VPN 裝置有對外開放的公用 IPv4 位址。 此 IP 位址不能位於 NAT 後方。
* 如果您已經有想要與之連線的虛擬網路，請確認沒有任何內部部署網路的子網路與想要連線的虛擬網路重疊。 您的虛擬網路不需要閘道子網路，而且不能有任何虛擬網路閘道。 如果您還沒有虛擬網路，可以使用本文中的步驟建立一個。
* 取得中樞區域的 IP 位址範圍。 中樞是虛擬網路，而您為中樞區域指定的位址範圍不能與任何連線的現有虛擬網路重疊。 也不能與連線至內部部署的位址範圍重疊。 如果您不熟悉位於內部部署網路組態的 IP 位址範圍，您需要與能夠提供那些詳細資料的人協調。
* 如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

## <a name="enroll"></a>1.在預覽版本中註冊

設定虛擬 WAN 之前，您必須先在預覽版中註冊訂用帳戶。 否則，您將無法在入口網站中使用虛擬 WAN。 若要註冊，您可以傳送電子郵件與訂用帳戶識別碼至 **azurevirtualwan@microsoft.com**。 當訂用帳戶註冊完成之後，您會收到電子郵件。

## <a name="vnet"></a>2.建立虛擬網路

如果您還沒有 VNet，可以使用 PowerShell 快速建立一個。 您也可以使用 Azure 入口網站建立虛擬網路。

* 請務必確認您所建立的 VNet 位址空間不會與任何其他想要連線的 Vnet 位址範圍重疊，或是不會與內部部署網路的位址空間重疊。 
* 如果您已經有 VNet，請確認它符合所需的準則，而且沒有虛擬網路閘道。

按一下本文中的 [試試看] 來開啟 PowerShell 主控台，即可輕鬆建立您的 VNet。 調整值，然後複製命令並貼到主控台視窗。

### <a name="create-a-resource-group"></a>建立資源群組

調整 PowerShell 命令，然後建立資源群組。

```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName WANTestRG -Location WestUS
```

### <a name="create-a-vnet"></a>建立 VNet

調整 PowerShell 命令，以建立適用於您環境的 VNet。

```azurepowershell-interactive
$fesub1 = New-AzureRmVirtualNetworkSubnetConfig -Name FrontEnd -AddressPrefix "10.1.0.0/24"
$vnet   = New-AzureRmVirtualNetwork `
            -Name WANVNet1 `
            -ResourceGroupName WANTestRG `
            -Location WestUS `
            -AddressPrefix "10.1.0.0/16" `
            -Subnet $fesub1
```

## <a name="wan"></a>3.建立虛擬 WAN

1. 透過瀏覽器瀏覽至 [Azure 入口網站](https://portal.azure.com) ，並使用您的 Azure 帳戶登入。
2. 在此階段中，您可以藉由瀏覽至 [所有服務] 並搜尋虛擬 WAN 來找到虛擬 WAN。 或是，在 Azure 入口網站頂端的搜尋方塊中搜尋虛擬 WAN。 按一下 [虛擬 WAN] 來開啟頁面。
3. 按一下 [建立] 來開啟 [建立 WAN] 頁面。 如果該頁面無法使用，表示您尚未獲准取得此預覽版本。

  ![建立 WAN](./media/virtual-wan-site-to-site-portal/createwan.png)
4. 在 [建立 WAN] 頁面上，填寫下列欄位。

  * **名稱** - 選取您想要用來稱呼 WAN 的名稱。
  * **訂用帳戶** - 選取您要使用的訂用帳戶。
  * **資源群組** - 建立新的或使用現有的資源群組。
  * **資源位置** - 從下拉式清單中選擇資源位置。 WAN 是全域資源，並不會在特定區域存留。 不過，您必須選取一個區域以方便管理，以及放置所建立的 WAN 資源。
5. 按一下 [建立] 來建立 WAN。

## <a name="site"></a>4.建立網站

建立需要的網站數目，這會與您的實體位置相對應。 例如，如果您在紐約 (NY)、倫敦和洛杉磯 (LA) 各有分公司，就會建立三個不同網站。 這些網站包含您內部部署的 VPN 裝置端點。 在此階段中，您可以只為網站指定一個私人位址空間。

1. 瀏覽至 [所有資源]。
2. 按一下您建立的虛擬 WAN。
3. 按一下頁面頂端的 [+ 建立網站] 以開啟 [建立網站] 頁面。

  ![新網站](media/virtual-wan-site-to-site-portal/createsite.png)
4. 在 [建立網站] 頁面上，填寫下列欄位：

  *  **名稱** - 這是您要用來參考內部部署網站的名稱。
  *  **公用 IP 位址** - 這是您內部部署網路中 VPN 裝置的公用 IP 位址。
  *  **私人位址空間** - 這是位於內部部署網站上的 IP 位址空間。 流向此位址空間的流量會路由至您本機網站。
  *  **訂用帳戶** - 請確認訂用帳戶。
  *  **資源群組** - 要使用的資源群組。
5. 按一下 [進階顯示] 即可檢視其他設定。 您可以**啟用 BGP** (選擇性欄位，可在 Azure 中為此網站建立的所有連線上啟用此功能)。 您也可以輸入 [裝置資訊] (選擇性欄位)。 這可協助 Azure 小組深入了解您的環境，以便在未來增加額外最佳化的可能性，或協助您進行疑難排解。

  ![BGP](media/virtual-wan-site-to-site-portal/sitebgp.png)
6. 按一下 [確認] 以建立網站。
7. 為您想要建立的每個網站重複這些步驟。

## <a name="hub"></a>5.建立中樞並與網站連線

1. 在您的虛擬 WAN 頁面上，按一下 [網站]。
2. 在 [未關聯的網站] 下，您會看到尚未連線至中樞的網站清單。
3. 選取您想要與之產生關聯的網站。
4. 從下拉式清單中，選取要與您中樞相關聯的區域。 要與您中樞產生關聯的區域中，應具有您想要連線的 VNet。
5. 按一下 [確認]。 如果此區域中沒有您的中樞，虛擬中樞 VNet 將會自動建立。 在此情況下，[建立區域中樞] 頁面會隨即出現。
6. 在 [建立區域中樞] 頁面上，輸入您的中樞 VNet 位址範圍。 這是將包含您中樞服務的 VNet。 您在此處輸入的範圍必須是私人 IP 位址範圍，而且不能與任何內部部署位址空間或 VNet 位址空間重疊。 後續的 VPN 端點會建立在中樞的 VNet 中。 (自動的中樞與閘道建立僅可在入口網站中進行)。
7. 按一下頁面底部的 [新增] 。

## <a name="vnet"></a>6.將 VNet 連線至中樞

在此步驟中，您會在中樞和 VNet 之間建立等互連的連線。 為您想要連線的每個 VNet 重複這些步驟。

1. 在您的虛擬 WAN 頁面上，按一下 [虛擬網路連線]。
2. 在 [虛擬網路連線] 頁面上，按一下 [+ 新增連線]。
3. 在 [新增連線] 頁面上，填寫下列欄位︰

    * **名稱** - 為您的連線命名。
    * **中樞** - 選取要與此連線產生關聯的中樞。
    * **訂用帳戶** - 請確認訂用帳戶。
    * **虛擬網路** - 選取要與此中樞連線的虛擬網路。 虛擬網路不能有現有的虛擬網路閘道。

## <a name="device"></a>7.下載 VPN 組態

若要設定您的內部部署 VPN 裝置，請使用 VPN 裝置組態。

1. 在您的虛擬 WAN 頁面上，按一下 [概觀]。
2. 在 [概觀] 頁面頂端，按一下 [下載 VPN 組態]。 Azure 會在資源群組 'microsoft-network-[location]' 中建立儲存體帳戶，其中 location 是 WAN 的位置。 將組態套用至您的 VPN 裝置之後，就可以刪除此儲存體帳戶。
3. 檔案建立完成之後，您可以按一下連結來下載。
4. 將組態套用至您的 VPN 裝置。

### <a name="understanding-the-vpn-device-configuration-file"></a>了解 VPN 裝置組態檔

裝置組態檔包含設定內部部署 VPN 裝置時要使用的設定。 當您檢視此檔案時，請注意下列資訊：

* **vpnSiteConfiguration -** 本區段表示連線至虛擬 WAN 時設定為網站的裝置詳細資料。 其中包含分支裝置的名稱和公用 IP 位址。
* **vpnSiteConnections -** 本區段提供下列相關資訊：

    * 虛擬中樞 VNet 的**位址空間**<br>範例：
 
        ```
        "AddressSpace":"10.1.0.0/24"
        ```
    * 連線至中樞的 Vnet **位址空間**<br>範例：

         ```
        "ConnectedSubnets":["10.2.0.0/16","10.30.0.0/16"]
         ```
    * 虛擬中樞 vpngateway 的 **IP 位址**。 由於 vpngateway 的每個連線都由主動-主動組態中的 2 個通道組成，因此您會看到此檔案中列出這兩個 IP 位址。 在此範例中，您會看到每個網站的 "Instance0" 和 "Instance1"。<br>範例：

        ``` 
        "Instance0":"104.45.18.186"
        "Instance1":"104.45.13.195"
        ```
    * **Vpngateway 連線組態詳細資料**，例如 BGP、預先共用金鑰等等。PSK 是自動為您產生的預先共用金鑰。 您隨時都可以在自訂 PSK 的 [概觀] 頁面中編輯連線。
  
### <a name="example-device-configuration-file"></a>範例裝置組態檔

  ```
  { 
      "configurationVersion":{ 
         "LastUpdatedTime":"2018-07-03T18:29:49.8405161Z",
         "Version":"r403583d-9c82-4cb8-8570-1cbbcd9983b5"
      },
      "vpnSiteConfiguration":{ 
         "Name":"testsite1",
         "IPAddress":"73.239.3.208"
      },
      "vpnSiteConnections":[ 
         { 
            "hubConfiguration":{ 
               "AddressSpace":"10.1.0.0/24",
               "Region":"West Europe",
               "ConnectedSubnets":[ 
                  "10.2.0.0/16",
                  "10.30.0.0/16"
               ]
            },
            "gatewayConfiguration":{ 
               "IpAddresses":{ 
                  "Instance0":"104.45.18.186",
                  "Instance1":"104.45.13.195"
               }
            },
            "connectionConfiguration":{ 
               "IsBgpEnabled":false,
               "PSK":"bkOWe5dPPqkx0DfFE3tyuP7y3oYqAEbI",
               "IPsecParameters":{ 
                  "SADataSizeInKilobytes":102400000,
                  "SALifeTimeInSeconds":3600
               }
            }
         }
      ]
   },
   { 
      "configurationVersion":{ 
         "LastUpdatedTime":"2018-07-03T18:29:49.8405161Z",
         "Version":"1f33f891-e1ab-42b8-8d8c-c024d337bcac"
      },
      "vpnSiteConfiguration":{ 
         "Name":" testsite2",
         "IPAddress":"66.193.205.122"
      },
      "vpnSiteConnections":[ 
         { 
            "hubConfiguration":{ 
               "AddressSpace":"10.1.0.0/24",
               "Region":"West Europe"
            },
            "gatewayConfiguration":{ 
               "IpAddresses":{ 
                  "Instance0":"104.45.18.187",
                  "Instance1":"104.45.13.195"
               }
            },
            "connectionConfiguration":{ 
               "IsBgpEnabled":false,
               "PSK":"XzODPyAYQqFs4ai9WzrJour0qLzeg7Qg",
               "IPsecParameters":{ 
                  "SADataSizeInKilobytes":102400000,
                  "SALifeTimeInSeconds":3600
               }
            }
         }
      ]
   },
   { 
      "configurationVersion":{ 
         "LastUpdatedTime":"2018-07-03T18:29:49.8405161Z",
         "Version":"cd1e4a23-96bd-43a9-93b5-b51c2a945c7"
      },
      "vpnSiteConfiguration":{ 
         "Name":" testsite3",
         "IPAddress":"182.71.123.228"
      },
      "vpnSiteConnections":[ 
         { 
            "hubConfiguration":{ 
               "AddressSpace":"10.1.0.0/24",
               "Region":"West Europe"
            },
            "gatewayConfiguration":{ 
               "IpAddresses":{ 
                  "Instance0":"104.45.18.187",
                  "Instance1":"104.45.13.195"
               }
            },
            "connectionConfiguration":{ 
               "IsBgpEnabled":false,
               "PSK":"YLkSdSYd4wjjEThR3aIxaXaqNdxUwSo9",
               "IPsecParameters":{ 
                  "SADataSizeInKilobytes":102400000,
                  "SALifeTimeInSeconds":3600
               }
            }
         }
      ]
   }
  ```

### <a name="configuring-your-vpn-device"></a>設定 VPN 裝置

>[!NOTE]
> 如果您正在使用虛擬 WAN 合作夥伴解決方案，VPN 裝置設定會自動發生；其中裝置控制器會從 Azure 取得組態檔，然後套用到要設定為與 Azure 連線的裝置。 這表示您無須知道如何手動設定 VPN 裝置。
>

如果需要設定裝置的指示，您可以使用 [VPN 裝置組態指令碼頁面](~/articles/vpn-gateway/vpn-gateway-about-vpn-devices.md#configscripts)中的指示，並留意下列注意事項：

* VPN 裝置頁面上的指示不是針對虛擬 WAN 所撰寫，但您也可以從組態檔使用虛擬 WAN 的值，手動設定 VPN 裝置。 
* 適用於 VPN 閘道的可下載裝置組態指令碼不適用於虛擬 WAN，因為組態不同。
* 虛擬 WAN 只能使用 IKEv2，不能使用 IKEv1。
* 虛擬 WAN 只能使用路由式 VPN 裝置和裝置指示。

## <a name="viewwan"></a>8.檢視您的虛擬 WAN

1. 瀏覽至虛擬 WAN。
2. 在 [概觀] 頁面中，地圖上的每個點都代表著中樞。 暫留在任一點上，即可檢視中樞健康情況摘要。
3. 在 [中樞與連線] 區段中，您可以檢視中樞狀態、網站、區域、VPN 連線狀態和輸入與輸出位元組。

## <a name="viewhealth"></a>9.檢視資源健康情況

1. 瀏覽至您的 WAN。
2. 在 WAN 頁面上的 [支援 + 疑難排解] 區段中，按一下 [健康情況] 並檢視您的資源。

## <a name="connectmon"></a>10.監視連線

建立連線以監視 Azure VM 和遠端站台之間的通訊。 如需有關如何設定連線監視的資訊，請參閱[監視網路通訊](~/articles/network-watcher/connection-monitor.md)。 來源欄位是 Azure 中的 VM IP，目的地 IP 是位址是網站 IP。

## <a name="cleanup"></a>11.清除資源

您可以使用 [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) 來移除不再需要的資源群組，以及其所包含的所有資源。 將 "myResourceGroup" 取代為您的資源群組名稱，然後執行下列 PowerShell 命令：

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="feedback"></a>預覽意見反應

歡迎提供意見反應。 若要回報任何問題，或針對虛擬 WAN 提供意見反應 (正面或負面皆可)，請寄送電子郵件至 <azurevirtualwan@microsoft.com>。 請在主旨列中以 "[]" 包含您的公司名稱。 如果您要回報問題，請也包含您的訂用帳戶 ID。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何：

> [!div class="checklist"]
> * 建立 WAN
> * 建立網站
> * 建立中樞
> * 將中樞連線至網站
> * 將 VNet 連線至中樞
> * 下載並套用 VPN 裝置組態
> * 檢視您的虛擬 WAN
> * 檢視資源健康情況
> * 監視連線

若要深入了解虛擬 WAN，請參閱[虛擬 WAN 概觀](virtual-wan-about.md)頁面。
