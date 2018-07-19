---
title: 在 Azure 可用性區域中建立區域備援虛擬網路閘道 - 預覽 | Microsoft Docs
description: 在可用性區域中部署 VPN 閘道和 ExpressRoute 閘道 - 預覽。
services: vpn-gateway
documentationcenter: na
author: cherylmc
Customer intent: As someone with a basic network background, I want to understand how to create zone-redundant gateways.
ms.service: vpn-gateway
ms.topic: article
ms.date: 07/09/2018
ms.author: cherylmc
ms.openlocfilehash: fa349555a5effd41ca519cbd5a29005203d79543
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/10/2018
ms.locfileid: "37952550"
---
# <a name="create-a-zone-redundant-virtual-network-gateway-in-azure-availability-zones---preview"></a>在 Azure 可用性區域中建立區域備援虛擬網路閘道 - 預覽

您可以在 [Azure 可用性區域](../availability-zones/az-overview.md)中部署 VPN 和 ExpressRoute 閘道。 此方式可為虛擬網路閘道帶來復原力、延展性和更高的可用性。 在 Azure 可用性區域中部署閘道可從根本上和邏輯上分隔區域內的閘道，同時還能在發生區域層級的失敗時，保護您內部部署項目與 Azure 的網路連線。

比起一般虛擬網路閘道，分區和區域備援閘道在基本效能上已獲得改良。 此外，建立區域備援或分區虛擬網路閘道的速度比建立其他閘道快。 無須 45 分鐘，建立 ExpressRoute 閘道大約只需 15 分鐘，而建立 VPN 閘道大約只需 19 分鐘。

### <a name="zrgw"></a>區域備援閘道

若要在各個可用性區域上自動部署虛擬網路閘道，您可以使用區域備援虛擬網路閘道。 透過區域備援閘道，您可以利用 GA 上 99.99% 的 SLA 執行時間，來存取 Azure 上緊要且具延展性的服務。

<br>
<br>

![區域備援閘道圖示](./media/create-zone-redundant-vnet-gateway/zonered.png)

### <a name="zgw"></a>分區閘道

若要在特定區域中部署閘道，您可以使用分區閘道。 當您部署分區閘道時，閘道的兩個執行個體都會部署在相同的可用性區域中。

<br>
<br>

![分區閘道圖示](./media/create-zone-redundant-vnet-gateway/zonal.png)

## <a name="gwskus"></a>閘道 SKU

區域備援和分區閘道必須使用新的閘道 SKU。 一旦您[在預覽版中完成自行註冊](#enroll)，您會在所有 Azure AZ 區域看到新的虛擬網路閘道 SKU。 這些 SKU 與 ExpressRoute 和 VPN 閘道的對應 SKU 類似，不同之處在於這些 SKU 專用於區域備援和分區閘道。

新的閘道 SKU 為：

### <a name="vpn-gateway"></a>VPN 閘道

* VpnGw1AZ
* VpnGw2AZ
* VpnGw3AZ

### <a name="expressroute"></a>ExpressRoute

* ErGw1AZ
* ErGw2AZ
* ErGw3AZ

## <a name="pipskus"></a>公用 IP SKU

區域備援閘道和分區閘道皆會依賴 Azure 公用 IP 資源的「標準」SKU。 Azure 公用 IP 資源組態可決定您部署的閘道是區域備援或分區閘道。 如果您以「基本」SKU 建立公用 IP 資源，閘道就不會有任何區域備援，且閘道資源將會是區域性的。

### <a name="pipzrg"></a>區域備援閘道

當您使用**標準**公用 IP SKU 建立公開 IP 位址，但沒有指定區域時，行為會根據閘道是 VPN 閘道或 ExpressRoute 閘道而有所差異。 

* 如果是 VPN 閘道，會將兩個閘道執行個體部署在這三個區域的其中任兩個，以提供區域備援。 
* 如果是 ExpressRoute 閘道，由於可能會有兩個以上的執行個體，閘道可以橫跨所有三個區域。

### <a name="pipzg"></a>分區閘道

當您使用**標準**公用 IP SKU 建立公開 IP 位址，並指定區域 (1、2 或 3) 時，所有閘道執行個體都會部署在相同區域中。

### <a name="piprg"></a>區域閘道

當您使用**基本**公用 IP SKU 建立公開 IP 位址時，閘道會部署為區域閘道，而且閘道內不會建置任何區域備援。

## <a name="before-you-begin"></a>開始之前

您可以使用安裝在本機電腦的 PowerShell 或 Azure Cloud Shell。 如果您選擇在本機安裝和使用 PowerShell，則需要最新版的 PowerShell 模組，才能使用此功能。

[!INCLUDE [Cloud shell](../../includes/vpn-gateway-cloud-shell-powershell.md)]

### <a name="to-use-powershell-locally"></a>在本機上使用 PowerShell

如果您使用本機電腦上的 PowerShell，而不是使用 Cloud Shell，您必須安裝 PowerShell 模組 6.1.1 版或更新版本。 若要檢查已安裝的 PowerShell 版本，請使用下列命令：

```azurepowershell
Get-Module AzureRM -ListAvailable | Select-Object -Property Name,Version,Path
```

如果您需要升級，請參閱[安裝 Azure PowerShell 模組](/powershell/azure/install-azurerm-ps)。

[!INCLUDE [PowerShell login](../../includes/vpn-gateway-ps-login-include.md)]

## <a name="enroll"></a>1.在預覽版本中註冊

您必須先在預覽版本中自行註冊您的訂用帳戶，才能設定區域備援或分區閘道。 一旦佈建您的訂用帳戶後，您會開始在所有 Azure AZ 區域中看到新的閘道 SKU。 

請確定您已登入 Azure 帳戶，並使用您想要為此預覽版本列入白名單的訂用帳戶。 請使用下列範例進行註冊：

```azurepowershell-interactive
Register-AzureRmProviderFeature -FeatureName AllowVMSSVirtualNetworkGateway -ProviderNamespace Microsoft.Network
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
```

請使用下列命令來確認 'AllowVMSSVirtualNetworkGateway' 功能已向您的訂用帳戶註冊：

```azurepowershell-interactive
Get-AzureRmProviderFeature -ProviderNamespace Microsoft.Network
```

結果會看似以下範例：

![已佈建](./media/create-zone-redundant-vnet-gateway/verifypreview.png)

## <a name="variables"></a>2.宣告變數

範例步驟所使用的值如下所示。 此外，有部分範例會在步驟中使用已宣告的變數。 如果您在自己的環境中使用這些步驟，請務必以您自己的值取代這些值。 在指定位置時，請確認您指定的區域可受到支援。 如需詳細資訊，請參閱[常見問題集](#faq)。

```azurepowershell-interactive
$RG1         = "TestRG1"
$VNet1       = "VNet1"
$Location1   = "CentralUS"
$FESubnet1   = "FrontEnd"
$BESubnet1   = "Backend"
$GwSubnet1   = "GatewaySubnet"
$VNet1Prefix = "10.1.0.0/16"
$FEPrefix1   = "10.1.0.0/24"
$BEPrefix1   = "10.1.1.0/24"
$GwPrefix1   = "10.1.255.0/27"
$Gw1         = "VNet1GW"
$GwIP1       = "VNet1GWIP"
$GwIPConf1   = "gwipconf1"
```

## <a name="configure"></a>3.建立虛擬網路

建立資源群組。

```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName $RG1 -Location $Location1
```

建立虛擬網路。

```azurepowershell-interactive
$fesub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubnet1 -AddressPrefix $FEPrefix1
$besub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubnet1 -AddressPrefix $BEPrefix1
$vnet = New-AzureRmVirtualNetwork -Name $VNet1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNet1Prefix -Subnet $fesub1,$besub1
```

## <a name="gwsub"></a>4.新增閘道子網路

閘道子網路包含虛擬網路閘道服務所使用的保留 IP 位址。 請使用下列範例來新增和設定閘道子網路：

新增閘道子網路。

```azurepowershell-interactive
$getvnet = Get-AzureRmVirtualNetwork -ResourceGroupName $RG1 -Name VNet1
Add-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.1.255.0/27 -VirtualNetwork $getvnet
```

設定虛擬網路的閘道子網路設定。

```azurepowershell-interactive
$getvnet | Set-AzureRmVirtualNetwork
```
## <a name="publicip"></a>5.要求公用 IP 位址
 
在此步驟中，針對您要建立的閘道選擇適用的指示。 部署閘道的區域選擇取決於為公用 IP 位址指定的區域。

### <a name="ipzoneredundant"></a>針對區域備援閘道

使用**標準** PublicIpaddress SKU 要求公用 IP 位址，而且不指定任何區域。 在此情況下，建立的標準公用 IP 位址會是區域備援的公用 IP。   

```azurepowershell-interactive
$pip1 = New-AzureRmPublicIpAddress -ResourceGroup $RG1 -Location $Location1 -Name $GwIP1 -AllocationMethod Static -Sku Standard
```

### <a name="ipzonalgw"></a>針對分區閘道

使用**標準** PublicIpaddress SKU 要求公用 IP 位址。 指定區域 (1、2 或 3)。 所有閘道執行個體都會部署在此區域中。

```azurepowershell-interactive
$pip1 = New-AzureRmPublicIpAddress -ResourceGroup $RG1 -Location $Location1 -Name $GwIP1 -AllocationMethod Static -Sku Standard -Zone 1
```

### <a name="ipregionalgw"></a>針對區域閘道

使用**基本** PublicIpaddress SKU 要求公用 IP 位址。 在此情況下，閘道會部署為區域閘道，而且閘道內不會建置任何區域備援。 閘道執行個體會在任何區域中個別建立。

```azurepowershell-interactive
$pip1 = New-AzureRmPublicIpAddress -ResourceGroup $RG1 -Location $Location1 -Name $GwIP1 -AllocationMethod Dynamic -Sku Basic
```
## <a name="gwipconfig"></a>6.建立 IP 組態

```azurepowershell-interactive
$getvnet = Get-AzureRmVirtualNetwork -ResourceGroupName $RG1 -Name $VNet1
$subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name $GwSubnet1 -VirtualNetwork $getvnet
$gwipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GwIPConf1 -Subnet $subnet -PublicIpAddress $pip1
```

## <a name="gwconfig"></a>7.建立閘道

建立虛擬網路閘道。

### <a name="for-expressroute"></a>針對 ExpressRoute

```azurepowershell-interactive
New-AzureRmVirtualNetworkGateway -ResourceGroup $RG1 -Location $Location1 -Name $Gw1 -IpConfigurations $GwIPConf1 -GatewayType ExpressRoute
```

### <a name="for-vpn-gateway"></a>針對 VPN 閘道

```azurepowershell-interactive
New-AzureRmVirtualNetworkGateway -ResourceGroup $RG1 -Location $Location1 -Name $Gw1 -IpConfigurations $GwIPConf1 -GatewayType Vpn -VpnType RouteBased
```

## <a name="feedback"></a>如何提供意見反應

歡迎提供意見反應。 若要針對區域備援和分區 VPN 與 Express Route 閘道回報任何問題或提供意見反應 (正面或負面皆可)，請傳送電子郵件至 aznetworkgateways@microsoft.com。 請在主旨列中以 "[]" 包含您的公司名稱。 如果您要回報問題，請也包含您的訂用帳戶 ID。

## <a name="faq"></a>常見問題集

### <a name="how-do-i-sign-up-for-the-preview"></a>我該如何註冊預覽版？

您可以使用本文中的 PowerShell 命令[自行註冊](#enroll)。

### <a name="what-will-change-when-i-enroll"></a>註冊時會有何變更？

從您的觀點來看，您可以在預覽期間部署具有區域備援的閘道。 這表示閘道的所有執行個體將會部署到各個 Azure 可用性區域，而且每個可用性區域都是不同的容錯網域和更新網域。 面對區域失敗時，這可讓您的閘道具有更高的可靠性、可用性及復原能力。

### <a name="can-i-use-the-azure-portal"></a>我是否可使用 Azure 入口網站？

可以，您可以使用 Azure 入口網站進行預覽。 不過，您仍需使用 PowerShell 來註冊，否則您將無法在預覽期間使用入口網站。

### <a name="what-regions-are-available-for-the-preview"></a>哪些區域可使用預覽版本？

區域備援和分區閘道可用於生產/Azure 公用區域。

### <a name="will-i-be-billed-for-participating-in-this-preview"></a>加入此預覽版本需要付費嗎？

在預覽期間，您無須支付閘道的費用。 不過，您的部署不會連結任何 SLA。 我們非常想聽聽您的意見反應。

> [!NOTE]
> 針對 ExpressRoute 閘道，閘道不會列入計費，也不會對您收取閘道的費用。 不過，線路本身 (不是閘道) 會列入計費。

### <a name="what-regions-are-available-for-me-to-try-this-in"></a>我可以在哪些區域中嘗試此作業？

您可以在美國中部和法國中部區域 (正式推出可用性區域的 Azure 區域) 使用公開預覽版。 接下來，我們會在其他 Azure 公用區域中推出區域備援閘道。

### <a name="can-i-change-my-existing-virtual-network-gateways-to-zone-redundant-or-zonal-gateways"></a>我可以將現有的虛擬網路閘道變更為區域備援或分區閘道嗎？

目前不支援將現有的虛擬網路閘道遷移至區域備援或分區閘道。 不過，您可以刪除現有的閘道，然後重新建立區域備援或分區閘道。

### <a name="can-i-deploy-both-vpn-and-express-route-gateways-in-same-virtual-network"></a>可以在相同的虛擬網路中部署 VPN 和 Express Route 閘道嗎？

公開預覽期間支援 VPN 和 Express Route 閘道共存於相同的虛擬網路中。 不過，請注意下列需求和限制：

* 保留閘道子網路的 /27 IP 位址範圍。
* 區域備援/分區 Express Route 閘道只能與區域備援/分區 VPN 閘道共存。
* 先部署區域備援/分區 Express Route 閘道，再部署區域備援/分區 VPN 閘道。
* 區域備援/分區 Express Route 閘道最多可以連線至 4 個線路。

## <a name="next-steps"></a>後續步驟

歡迎提供意見反應。 若要針對區域備援和分區 VPN 與 Express Route 閘道回報任何問題或提供意見反應 (正面或負面皆可)，請傳送電子郵件至 aznetworkgateways@microsoft.com。 請在主旨列中以 "[]" 包含您的公司名稱。 如果您要回報問題，請也包含您的訂用帳戶 ID。
