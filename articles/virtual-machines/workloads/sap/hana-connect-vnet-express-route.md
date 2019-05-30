---
title: 設定從虛擬網路到 SAP HANA on Azure （大型執行個體） 的連線能力 |Microsoft Docs
description: 設定從虛擬網路以使用 SAP HANA on Azure （大型執行個體） 的連線。
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
ms.date: 05/25/2019
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 9cd290f9e5e7819f3dffa2dd1efea9cd0028fc2c
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/27/2019
ms.locfileid: "66239458"
---
# <a name="connect-a-virtual-network-to-hana-large-instances"></a>將虛擬網路連線至 HANA 大型執行個體

建立 Azure 虛擬網路之後，您可以將該網路連線到 Azure 大型執行個體上的 SAP HANA。 在虛擬網路上建立 Azure ExpressRoute 閘道。 此閘道可讓您將虛擬網路連結至 ExpressRoute 線路連接到 「 HANA 大型執行個體 」 戳記上客戶租用戶。

> [!NOTE] 
> 此步驟可能需要 30 分鐘的時間才能完成。 會在指定的 Azure 訂用帳戶中建立新閘道，然後連線到指定的 Azure 虛擬網路。

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

如果閘道已經存在，請檢查其是否為 ExpressRoute 閘道。 如果不是 ExpressRoute 閘道，刪除閘道，並重新建立成 ExpressRoute 閘道。 如果已建立 ExpressRoute 閘道，請參閱本文的下一節＜連結虛擬網路＞。 

- 使用 [Azure 入口網站](https://portal.azure.com/) 或 PowerShell 來建立連線到您虛擬網路的 ExpressRoute VPN 閘道。
  - 如果您使用 Azure 入口網站，請新增新的 [虛擬網路閘道]  ，然後選取 [ExpressRoute]  作為閘道類型。
  - 如果您是使用 PowerShell，請先下載並使用最新版的 [Azure PowerShell SDK](https://azure.microsoft.com/downloads/)。 
 
下列命令會建立 ExpressRoute 閘道。 文字前面加上 _$_ 應以您的特定資訊進行更新的使用者定義變數。

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

Azure 虛擬網路現在具有 ExpressRoute 閘道。 使用 Microsoft 所提供的授權資訊連接到 SAP HANA 大型執行個體 」 ExpressRoute 線路的 ExpressRoute 閘道。 您可以使用 Azure 入口網站或 PowerShell 來連線。 PowerShell 的指示如下所示。 

針對每個連線使用不同的 AuthGUID，執行下列命令，為每個 ExpressRoute 閘道。 下列指令碼顯示的前兩個項目來自 Microsoft 所提供的資訊。 此外，AuthGUID 對每個虛擬網路及其閘道來說都是特定的。 如果您想要新增另一個 Azure 虛擬網路，您需要的 Microsoft 從連線至 Azure 的 HANA 大型執行個體 ExpressRoute 線路取得另外一個 AuthID。 

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
-PeerId $PeerID -ConnectionType ExpressRoute -AuthorizationKey $AuthGUID -ExpressRouteGatewayBypass
```

> [!NOTE]
> 最後一個參數，在命令中新增-AzVirtualNetworkGatewayConnection **ExpressRouteGatewayBypass**新的參數，可讓 ExpressRoute 快速路徑。 這種功能可以減少您的 Azure Vm 與 HANA 大型執行個體單位之間的網路延遲。 在 2019 年加入的功能。 如需詳細資訊，請檢查發行項[SAP HANA （大型執行個體） 網路架構](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture)。 請確定您執行 PowerShell cmdlet 的最新版本之前執行命令。

若要將閘道連線到與訂用帳戶關聯的多個 ExpressRoute 線路，可能需要執行此步驟多次。 例如，您可能要將相同的虛擬網路閘道連線到 ExpressRoute 線路，而此線路會將虛擬網路連線到內部部署網路。

## <a name="applying-expressroute-fast-path-to-existing-hana-large-instance-expressroute-circuits"></a>套用到現有的 HANA 大型執行個體 」 ExpressRoute 線路的 ExpressRoute 快速路徑
到目前為止的文件說明如何連接與 HANA 大型執行個體部署至其中一個 Azure 虛擬網路的 Azure ExpressRoute 閘道建立的新 ExpressRoute 線路。 但許多客戶已經有其 ExpressRoute 線路設定已經且有自己的虛擬網路連接到 HANA 大型執行個體。 當新 ExpressRoute 快速路徑會減少網路延遲，建議您套用使用這項功能的變更。 連接新的 ExpreesRoute 線路，並將變更現有的 ExpressRoute 線路的命令都相同。 如此一來，您需要執行這一系列的 PowerShell 命令，以變更要使用現有的線路 

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
-PeerId $PeerID -ConnectionType ExpressRoute -AuthorizationKey $AuthGUID -ExpressRouteGatewayBypass
```

請務必您新增的最後一個參數，如上方顯示啟用 ExpressRoute 快速路徑功能


## <a name="expressroute-global-reach"></a>ExpressRoute 遍及全球的觸角
當您想要為一或兩個的兩個案例中啟用 觸及全球範圍：

 - HANA 系統複寫，而不需要任何其他 proxy 或防火牆
 - 在兩個不同區域中執行系統複製或系統重新整理的 HANA 大型執行個體單位之間的複製備份

您需要考量的事項：

- 您必須提供/29 位址空間範圍位址空間。 位址範圍可能不會重疊的任何其他位址空間範圍，您使用目前連接到 Azure 的 HANA 大型執行個體，並可能不會重疊任何 IP 位址範圍與您用在 Azure 或內部部署的其他位置。
- 沒有可用於您內部部署路由公佈至 HANA 大型執行個體的 Asn （自發系統編號） 的限制。 在內部必須通告使用私人 Asn 65000 – 65020 範圍中的任何路由或 65515。 
- 連接到 HANA 大型執行個體的內部部署直接存取的案例中，您需要計算的費用為將您連接至 Azure 的線路。 如需價格，請檢查的價格[全域觸達的附加元件](https://azure.microsoft.com/pricing/details/expressroute/)。

若要取得一或兩個套用至您的部署案例，請開啟支援訊息與 Azure 上，如中所述[HANA 大型執行個體中開啟支援要求](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-li-portal#open-a-support-request-for-hana-large-instances)

所需的資料和關鍵字，您需要使用 microsoft 能夠路由及執行您的要求，看起來像：

- 服務：SAP HANA 大型執行個體
- 問題類型：組態和設定
- 問題的子類型：並未列出我的問題
- 主體 '修改我的網路-加入觸及全球範圍'
- 詳細資料：'加入全域連線到 HANA 大型執行個體至 HANA 大型執行個體租用戶或' 新增至內部部署至 HANA 大型執行個體租用戶的全域連線。
- HANA 大型執行個體至 HANA 大型執行個體租用戶案例的其他詳細資料：您必須定義**兩個 Azure 區域**來連接兩個租用戶位於何處**AND**您必須提交  **/29 IP 位址範圍**
- 在內部部署至 HANA 大型執行個體租用戶案例的其他詳細資料：您必須定義**Azure 區域**部署 HANA 大型執行個體租用戶想要直接連接。 此外，您需要提供**Auth GUID**並**線路對等識別碼**您收到建立您在內部部署與 Azure 之間的 ExpressRoute 線路時。 此外，您必須命名為您**ASN**。 最後一個交付項目是 **/29 IP 位址範圍**的 ExpressRoute 觸及全球範圍。

> [!NOTE]
> 如果您想要有處理兩種情況下，您必須提供兩個不同/29 IP 位址範圍沒有重疊與任何其他 IP 位址目前為止使用的範圍。 




## <a name="next-steps"></a>後續步驟

- [HLI 的其他網路需求](hana-additional-network-requirements.md)
