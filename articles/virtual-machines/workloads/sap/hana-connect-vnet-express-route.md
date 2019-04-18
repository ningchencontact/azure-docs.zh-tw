---
title: 從虛擬網路到 SAP HANA on Azure (大型執行個體) 的連線設定 | Microsoft Docs
description: 從虛擬網路到 SAP HANA on Azure (大型執行個體) 的連線設定。
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: cb14d0784ecb87c85b02952880e9eb5744d205a2
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/18/2019
ms.locfileid: "58850657"
---
# <a name="connect-a-virtual-network-to-hana-large-instances"></a>將虛擬網路連線至 HANA 大型執行個體

建立 Azure 虛擬網路之後，您可以將該網路連線到 Azure 大型執行個體上的 SAP HANA。 在虛擬網路上建立 Azure ExpressRoute 閘道。 此閘道可讓您將虛擬網路連結至 ExpressRoute 線路，而此線路會連線到大型執行個體戳記上的客戶租用戶。

> [!NOTE] 
> 此步驟可能需要 30 分鐘的時間才能完成。 會在指定的 Azure 訂用帳戶中建立新閘道，然後連線到指定的 Azure 虛擬網路。

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

如果閘道已經存在，請檢查其是否為 ExpressRoute 閘道。 如果不是，請刪除閘道，並重新建立為 ExpressRoute 閘道。 如果已建立 ExpressRoute 閘道，請參閱本文的下一節＜連結虛擬網路＞。 

- 使用 [Azure 入口網站](https://portal.azure.com/) 或 PowerShell 來建立連線到您虛擬網路的 ExpressRoute VPN 閘道。
  - 如果您使用 Azure 入口網站，請新增新的 [虛擬網路閘道]，然後選取 [ExpressRoute] 作為閘道類型。
  - 如果您是使用 PowerShell，請先下載並使用最新版的 [Azure PowerShell SDK](https://azure.microsoft.com/downloads/)。 下列命令會建立 ExpressRoute 閘道。 前面有 _$_ 的文字是使用者定義的變數，應更新成您的特定資訊。

```powershell
# These Values should already exist, update to match your environment
$myAzureRegion = "eastus"
$myGroupName = "SAP-East-Coast"
$myVNetName = "VNet01"

# These values are used to create the gateway, update for how you wish the GW components to be named
$myGWName = "VNet01GW"
$myGWConfig = "VNet01GWConfig"
$myGWPIPName = "VNet01GWPIP"
$myGWSku = "HighPerformance" # Supported values for HANA large instances are: HighPerformance or UltraPerformance

# These Commands create the Public IP and ExpressRoute Gateway
$vnet = Get-AzVirtualNetwork -Name $myVNetName -ResourceGroupName $myGroupName
$subnet = Get-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
New-AzPublicIpAddress -Name $myGWPIPName -ResourceGroupName $myGroupName `
-Location $myAzureRegion -AllocationMethod Dynamic
$gwpip = Get-AzPublicIpAddress -Name $myGWPIPName -ResourceGroupName $myGroupName
$gwipconfig = New-AzVirtualNetworkGatewayIpConfig -Name $myGWConfig -SubnetId $subnet.Id `
-PublicIpAddressId $gwpip.Id

New-AzVirtualNetworkGateway -Name $myGWName -ResourceGroupName $myGroupName -Location $myAzureRegion `
-IpConfigurations $gwipconfig -GatewayType ExpressRoute `
-GatewaySku $myGWSku -VpnType PolicyBased -EnableBgp $true
```

在此範例中，使用了 HighPerformance 閘道 SKU。 您的選項為 HighPerformance 或 UltraPerformance，因為 SAP HANA on Azure (大型執行個體) 僅支援這些閘道 SKU。

> [!IMPORTANT]
> 對於類型 II 類別 SKU 的 HANA 大型執行個體，您必須使用 UltraPerformance 閘道 SKU。

## <a name="link-virtual-networks"></a>連結虛擬網路

Azure 虛擬網路現在具有 ExpressRoute 閘道。 使用 Microsoft 所提供的授權資訊，將 ExpressRoute 閘道連線到 SAP HANA on Azure (大型執行個體) ExpressRoute 線路。 您可以使用 Azure 入口網站或 PowerShell 來連線。 建議使用入口網站，不過如果您想要使用 PowerShell，以下提供 PowerShell 指示。 

針對每個連線使用不同的 AuthGUID，來為每個虛擬網路閘道執行下列命令。 下列指令碼顯示的前兩個項目來自 Microsoft 所提供的資訊。 此外，AuthGUID 對每個虛擬網路及其閘道來說都是特定的。 如果您想要新增另一個 Azure 虛擬網路，就必須為將 HANA 大型執行個體連線到 Azure 的 ExpressRoute 線路取得另外一個 AuthID。 

```powershell
# Populate with information provided by Microsoft Onboarding team
$PeerID = "/subscriptions/9cb43037-9195-4420-a798-f87681a0e380/resourceGroups/Customer-USE-Circuits/providers/Microsoft.Network/expressRouteCircuits/Customer-USE01"
$AuthGUID = "76d40466-c458-4d14-adcf-3d1b56d1cd61"

# Your ExpressRoute Gateway information
$myGroupName = "SAP-East-Coast"
$myGWName = "VNet01GW"
$myGWLocation = "East US"

# Define the name for your connection
$myConnectionName = "VNet01GWConnection"

# Create a new connection between the ER Circuit and your Gateway using the Authorization
$gw = Get-AzVirtualNetworkGateway -Name $myGWName -ResourceGroupName $myGroupName
    
New-AzVirtualNetworkGatewayConnection -Name $myConnectionName `
-ResourceGroupName $myGroupName -Location $myGWLocation -VirtualNetworkGateway1 $gw `
-PeerId $PeerID -ConnectionType ExpressRoute -AuthorizationKey $AuthGUID
```

若要將閘道連線到與訂用帳戶關聯的多個 ExpressRoute 線路，可能需要執行此步驟多次。 例如，您可能要將相同的虛擬網路閘道連線到 ExpressRoute 線路，而此線路會將虛擬網路連線到內部部署網路。

## <a name="next-steps"></a>後續步驟

- [HLI 的其他網路需求](hana-additional-network-requirements.md)
