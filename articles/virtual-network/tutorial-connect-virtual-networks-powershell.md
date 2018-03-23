---
title: 使用虛擬網路對等互連連線虛擬網路 - PowerShell | Microsoft Docs
description: 深入了解如何使用虛擬網路對等互連連線虛擬網路。
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: ''
ms.topic: ''
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/06/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: c7b3fa2b566ab02e7fb4a03055db83f1545895e8
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/08/2018
---
# <a name="connect-virtual-networks-with-virtual-network-peering-using-powershell"></a>使用 PowerShell 以虛擬網路對等互連連線虛擬網路

您可以使用虛擬網路對等互連，讓虛擬網路彼此連線。 一旦虛擬網路對等互連，兩個虛擬網路中的資源就可以彼此通訊，且通訊時會有相同的延遲和頻寬，彷彿這些資源是位於相同的虛擬網路中。 本文涵蓋建立和對等互連兩個虛擬網路。 您會了解如何：

> [!div class="checklist"]
> * 建立兩個虛擬網路
> * 建立虛擬網路之間的對等互連
> * 測試對等互連

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

如果您選擇在本機安裝和使用 PowerShell，本文會要求使用 Azure PowerShell 模組版本 3.6 或更新版本。 執行 ` Get-Module -ListAvailable AzureRM` 來了解安裝的版本。 如果您需要升級，請參閱[安裝 Azure PowerShell 模組](/powershell/azure/install-azurerm-ps)。 如果您在本機執行 PowerShell，則也需要執行 `Login-AzureRmAccount` 以建立與 Azure 的連線。 

## <a name="create-virtual-networks"></a>建立虛擬網路

建立虛擬網路之前，您必須為虛擬網路以及在本文中建立的所有其他資源，建立資源群組。 使用 [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup)建立資源群組。 下列範例會在 eastus 位置建立名為 myResourceGroup 的資源群組。

```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

使用 [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) 建立虛擬網路。 下列範例會建立名為 myVirtualNetwork1 的虛擬網路，其位址首碼為 10.0.0/16。

```azurepowershell-interactive
$virtualNetwork1 = New-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork1 `
  -AddressPrefix 10.0.0.0/16
```

請使用 [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig) 來建立子網路設定。 下列範例會建立具有 10.0.0.0/24 位址首碼的子網路組態：

```azurepowershell-interactive
$subnetConfig = Add-AzureRmVirtualNetworkSubnetConfig `
  -Name Subnet1 `
  -AddressPrefix 10.0.0.0/24 `
  -VirtualNetwork $virtualNetwork1
```

請使用 [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/Set-AzureRmVirtualNetwork) 將子網路設定寫入至虛擬網路，以建立子網路：

```azurepowershell-interactive
$virtualNetwork1 | Set-AzureRmVirtualNetwork
```

建立具有 10.1.0.0/16 位址首碼和一個子網路的虛擬網路：

```azurepowershell-interactive
# Create the virtual network.
$virtualNetwork2 = New-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork2 `
  -AddressPrefix 10.1.0.0/16

# Create the subnet configuration.
$subnetConfig = Add-AzureRmVirtualNetworkSubnetConfig `
  -Name Subnet1 `
  -AddressPrefix 10.1.0.0/24 `
  -VirtualNetwork $virtualNetwork2

# Write the subnet configuration to the virtual network.
$virtualNetwork2 | Set-AzureRmVirtualNetwork
```

myVirtualNetwork2 虛擬網路的位址前置詞不會與 myVirtualNetwork1 虛擬網路的位址前置詞重疊。 您無法對等互連具有重疊位址首碼的虛擬網路。

## <a name="peer-virtual-networks"></a>對等互連虛擬網路

使用 [Add-AzureRmVirtualNetworkPeering](/powershell/module/azurerm.network/add-azurermvirtualnetworkpeering) 建立對等互連。 下列範例會將 myVirtualNetwork1 對等互連至 myVirtualNetwork2。

```azurepowershell-interactive
Add-AzureRmVirtualNetworkPeering `
  -Name myVirtualNetwork1-myVirtualNetwork2 `
  -VirtualNetwork $virtualNetwork1 `
  -RemoteVirtualNetworkId $virtualNetwork2.Id
```

在執行上一個命令之後所傳回的輸出中，您會看到 **PeeringState** 是「已啟動」。 對等互連會持續處於「已啟動」狀態，直到您建立從 myVirtualNetwork2 到 myVirtualNetwork1 的對等互連為止。 建立從 myVirtualNetwork2 到 myVirtualNetwork1 的對等互連。 

```azurepowershell-interactive
Add-AzureRmVirtualNetworkPeering `
  -Name myVirtualNetwork2-myVirtualNetwork1 `
  -VirtualNetwork $virtualNetwork2 `
  -RemoteVirtualNetworkId $virtualNetwork1.Id
```

在執行上一個命令之後所傳回的輸出中，您會看到 **PeeringState** 是「已連線」。 Azure 也會將 myVirtualNetwork1-myVirtualNetwork2 對等互連的對等互連狀態變更為「已連線」。 使用 [Get-AzureRmVirtualNetworkPeering](/powershell/module/azurerm.network/get-azurermvirtualnetworkpeering) 確認 myVirtualNetwork1-myVirtualNetwork2 對等互連的對等互連狀態變更為「已連線」。

```azurepowershell-interactive
Get-AzureRmVirtualNetworkPeering `
  -ResourceGroupName myResourceGroup `
  -VirtualNetworkName myVirtualNetwork1 `
  | Select PeeringState
```

兩個虛擬網路之對等互連的 **PeeringState** 都是「已連線」之前，其中一個虛擬網路中的資源無法與另一個虛擬網路中的資源通訊。 

對等互連介於兩個虛擬網路之間，但不可轉移。 所以舉例來說，如果您也想要將 myVirtualNetwork2 對等互連至 myVirtualNetwork3，您必須在虛擬網路 myVirtualNetwork2 與 myVirtualNetwork3 之間建立額外的對等互連。 雖然 myVirtualNetwork1 已與 myVirtualNetwork2 對等互連，但是如果 myVirtualNetwork1 也與 myVirtualNetwork3 對等互連，則 myVirtualNetwork1 內的資源只能存取 myVirtualNetwork3 中的資源。 

在對等互連生產環境虛擬網路之前，建議您徹底熟悉[對等互連概觀](virtual-network-peering-overview.md)、[管理對等互連](virtual-network-manage-peering.md)以及[虛擬網路限制](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)。 雖然本文說明相同訂用帳戶和位置中兩個虛擬網路之間的對等互連，您也可以對等互連[不同區域](#register)和[不同 Azure 訂用帳戶](create-peering-different-subscriptions.md#powershell)中的虛擬網路。 您也可以使用對等互連來建立[中樞和輪輻網路設計](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#vnet-peering)。

## <a name="test-peering"></a>測試對等互連

若要測試不同虛擬網路中虛擬機器之間透過對等互連的網路通訊，請將虛擬機器部署到每個子網路，然後在虛擬機器之間進行通訊。 

### <a name="create-virtual-machines"></a>建立虛擬機器

在每個虛擬網路中建立虛擬機器，以便您可以在稍後步驟中驗證其間的通訊。

請使用 [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) 來建立虛擬機器。 下列範例會在 myVirtualNetwork1 虛擬網路中建立名為 myVm1 的虛擬機器。 `-AsJob` 選項會在背景建立虛擬機器，因此您可以繼續進行下一步。 出現提示時，輸入您想要用來登入虛擬機器的使用者名稱和密碼。

```azurepowershell-interactive
New-AzureRmVm `
  -ResourceGroupName "myResourceGroup" `
  -Location "East US" `
  -VirtualNetworkName "myVirtualNetwork1" `
  -SubnetName "Subnet1" `
  -ImageName "Win2016Datacenter" `
  -Name "myVm1" `
  -AsJob
```

Azure 會自動將 10.0.0.4 指派為虛擬機器的私人 IP 位址，因為 10.0.0.4 是 myVirtualNetwork1 的 Subnet1 中第一個可用的 IP 位址。 

在 myVirtualNetwork2 虛擬網路中建立虛擬機器。

```azurepowershell-interactive
New-AzureRmVm `
  -ResourceGroupName "myResourceGroup" `
  -Location "East US" `
  -VirtualNetworkName "myVirtualNetwork2" `
  -SubnetName "Subnet1" `
  -ImageName "Win2016Datacenter" `
  -Name "myVm2"
```

建立虛擬機器需要幾分鐘的時間。 雖然不在傳回的輸出中，但 Azure 會將 10.1.0.4 指派為虛擬機器的私人 IP 位址，因為 10.1.0.4 是 myVirtualNetwork2 的 Subnet1 中第一個可用的 IP 位址。 

在 Azure 建立虛擬機器並將輸出傳回至 PowerShell 之前，請勿繼續進行後續步驟。

### <a name="test-virtual-machine-communication"></a>測試虛擬機器通訊

您可以從網際網路連線至虛擬機器的公用 IP 位址。 請使用 [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) 來傳回虛擬機器的公用 IP 位址。 以下範例會傳回 *myVm1* 虛擬機器的公用 IP 位址：

```azurepowershell-interactive
Get-AzureRmPublicIpAddress `
  -Name myVm1 `
  -ResourceGroupName myResourceGroup | Select IpAddress
```

請從您的本機電腦使用下列命令，來建立與 *myVm1* 虛擬機器的遠端桌面工作階段。 以上一個命令傳回的 IP 位址取代 `<publicIpAddress>`。

```
mstsc /v:<publicIpAddress>
```

將會建立一個「遠端桌面通訊協定」(.rdp) 檔案、下載至您的電腦並開啟。 輸入使用者名稱和密碼 (您可能需要選取 [更多選擇]，然後選取 [使用不同的帳戶] 以指定您在建立虛擬機器時輸入的認證)，然後按一下 [確定]。 您可能會在登入過程中收到憑證警告。 按一下 [是] 或 [繼續] 以繼續進行連線。

從命令提示字元，通過 Windows 防火牆啟用 Ping，您就可以在稍後步驟中從 myVm2 Ping 這部虛擬機器。

```
netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow
```

雖然本文使用 Ping 進行測試，但不建議在生產環境部署中允許 ICMP 通過 Windows 防火牆。

若要連線至 myVm2 虛擬機器，請在 myVm1 虛擬機器上從命令提示字元輸入下列命令：

```
mstsc /v:10.1.0.4
```

由於您在 myVm1 上面啟用 Ping，您現在可以在 myVm2 虛擬機器上從命令提示字元依據 IP 位址來 Ping 它：

```
ping 10.0.0.4
```

您會收到四個回覆。 如果您依據虛擬機器的名稱 (myVm1) 而不是其 IP 位址來進行 ping，則 ping 會失敗，因為 myVm1 是未知的主機名稱。 Azure 的預設名稱解析可以在相同虛擬網路中的虛擬機器之間運作，但是無法在不同虛擬網路中的虛擬機器之間運作。 若要跨虛擬網路間解析名稱，您必須[部署自己的 DNS 伺服器](virtual-networks-name-resolution-for-vms-and-role-instances.md)或使用 [Azure DNS 私人網域](../dns/private-dns-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。

同時中斷與 myVm1 和 myVm2 的 RDP 工作階段。

## <a name="clean-up-resources"></a>清除資源

請使用 [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) 來移除不再需要的資源群組以及其所包含的所有資源。

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

**<a name="register"></a>註冊可獲得虛擬網路對等互連全球預覽版**

在一般情況下，可以為相同地區中的虛擬網路建立對等互連。 讓不同區域中的虛擬網路進行對等互連的功能目前為預覽版。 如需了解有哪些可用的區域，請參閱[虛擬網路更新](https://azure.microsoft.com/updates/?product=virtual-network)。 若要使各區域的虛擬網路對等互連，您必須完成下列步驟 (在您想要對等互連之虛擬網路適用的訂用帳戶內)，先註冊預覽版：

1. 輸入下列命令，對於在您想要對等互連之每個虛擬網路適用的訂用帳戶註冊預覽：

    ```powershell-interactive
    Register-AzureRmProviderFeature `
      -FeatureName AllowGlobalVnetPeering `
      -ProviderNamespace Microsoft.Network
    
    Register-AzureRmResourceProvider `
      -ProviderNamespace Microsoft.Network
    ```
2. 藉由輸入下列命令，確認您已註冊預覽版︰

    ```powershell-interactive    
    Get-AzureRmProviderFeature `
      -FeatureName AllowGlobalVnetPeering `
      -ProviderNamespace Microsoft.Network
    ```

    如果您在針對兩個訂用帳戶輸入上一個命令之後收到的 **RegistrationState** 輸出是「已註冊」之前，嘗試對等互連不同區域中的虛擬網路，對等互連會失敗。

## <a name="next-steps"></a>後續步驟

在本文中，您已了解如何使用虛擬網路對等互連來連線兩個網路。 您可以透過 VPN [將自己的電腦連線到虛擬網路](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)，並且與虛擬網路中或已對等互連虛擬網路中的資源進行互動。

繼續進行可重複使用指令碼的指令碼範例，以完成虛擬網路文章中涵蓋的許多工作。

> [!div class="nextstepaction"]
> [虛擬網路指令碼範例](../networking/powershell-samples.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
