---
title: 路由傳送網路流量 - PowerShell | Microsoft Docs
description: 了解如何使用 PowerShell 以路由表路由傳送網路流量。
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/05/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: f91b143c75a82aa6760796770b3ae4d0e4ec53dd
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/08/2018
---
# <a name="route-network-traffic-with-a-route-table-using-powershell"></a>使用 PowerShell 以路由表路由傳送網路流量

根據預設，Azure 會自動路由傳送虛擬網路內所有子網路之間的流量。 您可以建立您自己的路由，以覆寫 Azure 的預設路由。 舉例來說，如果您想要通過防火牆路由傳送子網路之間的流量，則建立自訂路由的能力很有幫助。 在本文中，您將了解如何：

> [!div class="checklist"]
> * 建立路由表
> * 建立路由
> * 讓路由表與虛擬網路子網路產生關聯
> * 測試路由
> * 針對路由進行疑難排解

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

如果您選擇在本機安裝和使用 PowerShell，本文會要求使用 Azure PowerShell 模組版本 5.4.1 或更新版本。 執行 `Get-Module -ListAvailable AzureRM` 來了解安裝的版本。 如果您需要升級，請參閱[安裝 Azure PowerShell 模組](/powershell/azure/install-azurerm-ps)。 如果您在本機執行 PowerShell，則也需要執行 `Login-AzureRmAccount` 以建立與 Azure 的連線。 

## <a name="create-a-route-table"></a>建立路由表

根據預設，Azure 會路由傳送虛擬網路內所有子網路之間的流量。 若要深入了解 Azure 的預設路由，請參閱[系統路由](virtual-networks-udr-overview.md)。 若要覆寫 Azure 的預設路由，您可建立內含路由的路由表，並且將路由表與虛擬網路子網路產生關聯。

建立路由表之前，請先使用 [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) 來建立資源群組。 下列範例會針對在本文中建立的所有資源，建立名為 myResourceGroup 的資源群組。 

```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

使用 [New-AzureRmRouteTable](/powershell/module/azurerm.network/new-azurermroutetable) 建立路由表。 下列範例會建立名為 myRouteTablePublic 的路由表。

```azurepowershell-interactive
$routeTablePublic = New-AzureRmRouteTable `
  -Name 'myRouteTablePublic' `
  -ResourceGroupName myResourceGroup `
  -location EastUS
```

## <a name="create-a-route"></a>建立路由

路由表包含零個或多個路由。 藉由使用 [Get-AzureRmRouteTable](/powershell/module/azurerm.network/get-azurermroutetable) 擷取路由表物件來建立路由，使用 [Add-AzureRmRouteConfig](/powershell/module/azurerm.network/add-azurermrouteconfig) 建立路由，然後使用 [Set-AzureRmRouteTable](/powershell/module/azurerm.network/set-azurermroutetable) 將路由組態寫入路由表。 

```azurepowershell-interactive
Get-AzureRmRouteTable `
  -ResourceGroupName "myResourceGroup" `
  -Name "myRouteTablePublic" `
  | Add-AzureRmRouteConfig `
  -Name "ToPrivateSubnet" `
  -AddressPrefix 10.0.1.0/24 `
  -NextHopType "VirtualAppliance" `
  -NextHopIpAddress 10.0.2.4 `
 | Set-AzureRmRouteTable
```

路由會透過具有 IP 位址 10.0.2.4 的網路虛擬設備將所有流量導向至 10.0.1.0/24 位址首碼。 在稍後步驟中會建立具有指定位址首碼的網路虛擬設備和子網路。 路由會覆寫 Azure 的預設路由，該預設路由會直接路由傳送子網路之間的流量。 每個路由都會指定下一個躍點類型。 下一個躍點類型會指示 Azure 如何路由傳送流量。 在此範例中，下一個躍點類型是 VirtualAppliance。 若要深入了解 Azure 中所有可用的下一個躍點類型，以及使用它們的時機，請參閱[下一個躍點類型](virtual-networks-udr-overview.md#custom-routes)。

## <a name="associate-a-route-table-to-a-subnet"></a>建立路由表與子網路的關聯

您必須先建立虛擬網路和子網路，才能讓路由表與子網路產生關聯。 使用 [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) 建立虛擬網路。 下列範例會建立名為 myVirtualNetwork 的虛擬網路，位址首碼為 10.0.0.0/16。

```azurepowershell-interactive
$virtualNetwork = New-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

藉由使用 [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig) 建立三個子網路組態，建立三個子網路。 下列範例會針對「公用」、「私人」和「DMZ」子網路建立三個子網路組態：

```azurepowershell-interactive
$subnetConfigPublic = Add-AzureRmVirtualNetworkSubnetConfig `
  -Name Public `
  -AddressPrefix 10.0.0.0/24 `
  -VirtualNetwork $virtualNetwork

$subnetConfigPrivate = Add-AzureRmVirtualNetworkSubnetConfig `
  -Name Private `
  -AddressPrefix 10.0.1.0/24 `
  -VirtualNetwork $virtualNetwork

$subnetConfigDmz = Add-AzureRmVirtualNetworkSubnetConfig `
  -Name DMZ `
  -AddressPrefix 10.0.2.0/24 `
  -VirtualNetwork $virtualNetwork
```

位址首碼必須位於針對虛擬網路指定的位址前置詞內。 子網路位址首碼彼此不能重疊。

請使用 [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/Set-AzureRmVirtualNetwork) 將子網路組態寫入至虛擬網路，這樣會在虛擬網路中建立子網路：

```azurepowershell-interactive
$virtualNetwork | Set-AzureRmVirtualNetwork
```

您可以將路由表與零個或多個子網路建立關聯。 子網路可以有零個或一個與其相關聯的路由表。 來自子網路的輸出流量會根據 Azure 的預設路由，以及您新增至與子網路相關聯之路由表的自訂路由來進行路由傳送。 使用 [Set-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/set-azurermvirtualnetworksubnetconfig) 讓 myRouteTablePublic 路由表與「公用」子網路產生關聯，然後使用 [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/set-azurermvirtualnetwork) 將子網路組態寫入虛擬網路。

```azurepowershell-interactive
Set-AzureRmVirtualNetworkSubnetConfig `
  -VirtualNetwork $virtualNetwork `
  -Name 'Public' `
  -AddressPrefix 10.0.0.0/24 `
  -RouteTable $routeTablePublic | `
Set-AzureRmVirtualNetwork
```

在您部署用於生產環境的路由表之前，建議您徹底瞭解[在 Azure 中路由傳送](virtual-networks-udr-overview.md)和 [Azure 限制](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)。

## <a name="test-routing"></a>測試路由

若要測試路由，您要建立一個作為網路虛擬設備的虛擬機器，讓您在上一個步驟中建立的路由通過。 建立網路虛擬設備之後，您要將虛擬機器部署到「公用」和「私人」子網路中。 然後透過網路虛擬設備將「公用」子網路的流量路由傳送至「私人」子網路。

### <a name="create-a-network-virtual-appliance"></a>建立網路虛擬設備

執行網路應用程式的虛擬機器通常稱為網路虛擬設備。 網路虛擬設備通常會接收網路流量、執行某些動作，然後根據在網路應用程式中設定的邏輯轉送或卸除網路流量。 

#### <a name="create-a-network-interface"></a>建立網路介面

在上一個步驟中，您建立的路由將網路虛擬設備指定為下一個躍點類型。 執行網路應用程式的虛擬機器通常稱為網路虛擬設備。 在生產環境中，您部署的網路虛擬設備通常是預先設定的虛擬機器。 從 [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?search=network%20virtual%20appliance&page=1) 可取得數個網路虛擬設備。 在本文中會建立基本虛擬機器。

虛擬機器有一或多個與它連結的網路介面，這可讓虛擬機器與網路通訊。 若要讓網路介面能夠將傳送給它的網路流量轉送 (目的地不是它自己的 IP 位址)，則必須對網路介面啟用 IP 轉送。 在建立網路介面之前，您必須使用 [Get-AzureRmVirtualNetwork](/powershell/module/azurerm.network/get-azurermvirtualnetwork) 擷取虛擬網路識別碼，然後使用 [Get-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/get-azurermvirtualnetworksubnetconfig) 擷取子網路識別碼。 在已啟用 IP 轉送的 DMZ 子網路中，使用 [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface) 建立網路介面：

```azurepowershell-interactive
# Retrieve the virtual network object into a variable.
$virtualNetwork=Get-AzureRmVirtualNetwork `
  -Name myVirtualNetwork `
  -ResourceGroupName myResourceGroup

# Retrieve the subnet configuration into a variable.
$subnetConfigDmz = Get-AzureRmVirtualNetworkSubnetConfig `
  -Name DMZ `
  -VirtualNetwork $virtualNetwork

# Create the network interface.
$nic = New-AzureRmNetworkInterface `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name 'myVmNva' `
  -SubnetId $subnetConfigDmz.Id `
  -EnableIPForwarding
```

#### <a name="create-a-virtual-machine"></a>建立虛擬機器

若要建立虛擬機器並將現有網路介面與它連結，您必須先使用 [New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig) 建立虛擬機器組態。 組態包含在先前步驟中建立的網路介面。 出現提供使用者名稱和密碼的提示時，選取您想要用來登入虛擬機器的使用者名稱和密碼。 

```azurepowershell-interactive
# Create a credential object.
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

# Create a virtual machine configuration.
$vmConfig = New-AzureRmVMConfig `
  -VMName 'myVmNva' `
  -VMSize Standard_DS2 | `
  Set-AzureRmVMOperatingSystem -Windows `
    -ComputerName 'myVmNva' `
    -Credential $cred | `
  Set-AzureRmVMSourceImage `
    -PublisherName MicrosoftWindowsServer `
    -Offer WindowsServer `
    -Skus 2016-Datacenter `
    -Version latest | `
  Add-AzureRmVMNetworkInterface -Id $nic.Id
```

以 [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) 使用虛擬機器組態建立虛擬機器。 以下範例會建立名為 myVmNva 的虛擬機器。 

```azurepowershell-interactive
$vmNva = New-AzureRmVM `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -VM $vmConfig `
  -AsJob
```

`-AsJob` 選項會在背景建立虛擬機器，因此您可以繼續進行下一步。 出現提示時，輸入您想要用來登入虛擬機器的使用者名稱和密碼。 在生產環境中，您部署的網路虛擬設備通常是預先設定的虛擬機器。 從 [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?search=network%20virtual%20appliance&page=1) 可取得數個網路虛擬設備。

Azure 已將 10.0.2.4 指派為虛擬機器的私人 IP 位址，因為 10.0.2.4 是 myVirtualNetwork 的 DMZ 子網路中第一個可用的 IP 位址。

### <a name="create-virtual-machines"></a>建立虛擬機器

在虛擬網路中建立兩部虛擬機器，您就可以在後續步驟中驗證來自「公用」子網路的流量是否透過網路虛擬設備路由傳送至「私人」子網路。 

使用 [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) 在「公用」子網路中建立虛擬機器。 下列範例會在 myVirtualNetwork 虛擬網路的「公用」子網路中建立名為 myVmWeb 的虛擬機器。 

```azurepowershell-interactive
New-AzureRmVm `
  -ResourceGroupName "myResourceGroup" `
  -Location "East US" `
  -VirtualNetworkName "myVirtualNetwork" `
  -SubnetName "Public" `
  -ImageName "Win2016Datacenter" `
  -Name "myVmWeb" `
  -AsJob
```

Azure 已將 10.0.0.4 指派為虛擬機器的私人 IP 位址，因為 10.0.1.4 是 myVirtualNetwork 的「公用」子網路中第一個可用的 IP 位址。

在「私人」子網路中建立虛擬機器。

```azurepowershell-interactive
New-AzureRmVm `
  -ResourceGroupName "myResourceGroup" `
  -Location "East US" `
  -VirtualNetworkName "myVirtualNetwork" `
  -SubnetName "Private" `
  -ImageName "Win2016Datacenter" `
  -Name "myVmMgmt"
```

建立虛擬機器需要幾分鐘的時間。 Azure 已將 10.0.1.4 指派為虛擬機器的私人 IP 位址，因為 10.0.1.4 是 myVirtualNetwork 的「私人」子網路中第一個可用的 IP 位址。 

在建立虛擬機器且 Azure 將輸出傳回 PowerShell 之前，請勿繼續進行下一個步驟。

### <a name="route-traffic-through-a-network-virtual-appliance"></a>透過網路虛擬設備來路由傳送流量

使用 [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension) 來啟用通過 Windows 防火牆對 myVmWeb 和 myVmMgmt 虛擬機器的 ICMP 輸入，以便在後續步驟中使用 tracert 來測試虛擬機器之間的通訊：

```powershell-interactive
Set-AzureRmVMExtension `
  -ResourceGroupName myResourceGroup `
  -VMName myVmWeb `
  -ExtensionName AllowICMP `
  -Publisher Microsoft.Compute `
  -ExtensionType CustomScriptExtension `
  -TypeHandlerVersion 1.8 `
  -SettingString '{"commandToExecute": "netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow"}' `
  -Location EastUS

Set-AzureRmVMExtension `
  -ResourceGroupName myResourceGroup `
  -VMName myVmMgmt `
  -ExtensionName AllowICMP `
  -Publisher Microsoft.Compute `
  -ExtensionType CustomScriptExtension `
  -TypeHandlerVersion 1.8 `
  -SettingString '{"commandToExecute": "netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow"}' `
  -Location EastUS
```

先前的命令可能需要幾分鐘才能完成。 在命令完成且輸出傳回 PowerShell 之前，請勿繼續進行下一個步驟。 雖然本文使用 tracert 來測試路由，但不建議在生產環境部署中允許 ICMP 通過 Windows 防火牆。

您從網際網路連線至虛擬機器的公用 IP 位址。 請使用 [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) 來傳回虛擬機器的公用 IP 位址。 以下範例會傳回 myVmMgmt 虛擬機器的公用 IP 位址：

```azurepowershell-interactive
Get-AzureRmPublicIpAddress `
  -Name myVmMgmt `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

請從您的本機電腦使用下列命令，來建立與 myVmMgmt 虛擬機器的遠端桌面工作階段。 以上一個命令傳回的 IP 位址取代 `<publicIpAddress>`。

```
mstsc /v:<publicIpAddress>
```

將會建立一個「遠端桌面通訊協定」(.rdp) 檔案、下載至您的電腦並開啟。 輸入您在建立虛擬機器時指定的使用者名稱和密碼 (您可能需要選取 [更多選擇]，然後選取 [使用不同的帳戶]，以指定您在建立虛擬機器時輸入的認證)，然後選取 [確定]。 您可能會在登入過程中收到憑證警告。 按一下 [是] 或 [繼續] 以繼續進行連線。 

您已在[啟用 IP 轉送](#enable-ip-forwarding)中針對虛擬機器的網路介面啟用在 Azure 內 IP 轉送。 在虛擬機器內，作業系統或在虛擬機器內執行的應用程式也必須能夠轉送網路流量。 當您在生產環境中部署網路虛擬設備時，設備通常會在轉送流量之前篩選、記錄或執行一些其他函式。 但在本文中，作業系統只會轉送它接收的所有流量。 在 myVmNva 的作業系統內啟用 IP 轉送：

從 myVmMgmt 虛擬機器的命令提示字元，透過遠端桌面連線至 myVmNva 虛擬機器：

``` 
mstsc /v:myVmNva
```
    
若要在 myVmNva 虛擬機器的作業系統內啟用 IP 轉送，請在 myVmNva 虛擬機器的 PowerShell 中輸入下列命令：

```powershell
Set-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters -Name IpEnableRouter -Value 1
```
    
重新啟動 myVmNva 虛擬機器，這也會中斷與遠端桌面工作階段的連線，讓您留在您對 myVmMgmt 虛擬機器開啟的遠端桌面工作階段中。

在 myVmNva 虛擬機器重新啟動之後，使用下列命令，測試從 myVmMgmt 虛擬機器到 myVmWeb 虛擬機器的網路流量路由。

```
tracert myvmweb
```

回應如下列範例所示：

```
Tracing route to myvmweb.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.0.4]
over a maximum of 30 hops:
    
1     1 ms     1 ms     1 ms  10.0.0.4
  
Trace complete.
```

您可以看到流量從 myVmMgmt 虛擬機器直接路由傳送至 myVmWeb 虛擬機器。 Azure 的預設路由會直接路由傳送子網路之間的流量。

請使用下列命令，從 myVmMgmt 虛擬機器將桌面遠端連線至 myVmWeb 虛擬機器：

``` 
mstsc /v:myVmWeb
```

若要測試從 myVmWeb 虛擬機器到 myVmMgmt 虛擬機器的網路流量路由，請從命令提示字元輸入下列命令：

```
tracert myvmmgmt
```

回應如下列範例所示：

```
Tracing route to myvmmgmt.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.1.4]
over a maximum of 30 hops:
        
1    <1 ms     *        1 ms  10.0.2.4
2     1 ms     1 ms     1 ms  10.0.1.4
        
Trace complete.
```

您可以看到第一個躍點是 10.0.2.4，也就是網路虛擬設備的私人 IP 位址。 第二躍點是 10.0.1.4，也就是 myVmMgmt 虛擬機器的私人 IP 位址。 新增至 myRouteTablePublic 路由表且與「公用」子網路產生關聯的路由，會導致 Azure 透過 NVA 路由傳送流量，而不是直接路由傳送到「私人」子網路。

將 myVmWeb 和 myVmMgmt 虛擬機器的遠端桌面工作階段都關閉。

## <a name="troubleshoot-routing"></a>針對路由進行疑難排解

如同您在先前步驟中所學，Azure 會套用預設路由，您可以選擇性地使用自己的路由覆寫。 有時候，流量可能無法如您所預期進行路由。 如果您在 EastUS 區域還沒有網路監看員，請使用 [New-AzureRmNetworkWatcher](/powershell/module/azurerm.network/new-azurermnetworkwatcher) 在該區域啟用網路監看員：

```azurepowershell-interactive
# Enable network watcher for east region, if you don't already have a network watcher enabled for the region:
$nw = New-AzureRmNetworkWatcher `
 -Location eastus `
 -Name myNetworkWatcher_eastus `
 -ResourceGroupName myResourceGroup
```

使用 [Get-AzureRmNetworkWatcherNextHop](/powershell/module/azurerm.network/get-azurermnetworkwatchernexthop) 來判斷如何路由傳送兩部虛擬機器之間的流量。 例如，下列命令會測試從 myVmWeb (10.0.0.4) 虛擬機器路由傳送至 myVmMgmt (10.0.1.4) 虛擬機器的流量：

```azurepowershell-interactive
$vmWeb = Get-AzureRmVM `
  -Name myVmWeb `
  -ResourceGroupName myResourceGroup

Get-AzureRmNetworkWatcherNextHop `
  -DestinationIPAddress 10.0.1.4 `
  -NetworkWatcherName myNetworkWatcher_eastus `
  -ResourceGroupName myResourceGroup `
  -SourceIPAddress 10.0.0.4 `
  -TargetVirtualMachineId $vmWeb.Id
```
稍待片刻後，會傳回下列輸出：

```azurepowershell
NextHopIpAddress    NextHopType       RouteTableId
------------------  ---------------- ---------------------------------------------------------------------------------------------------------------------------
10.0.2.4            VirtualAppliance  /subscriptions/<Subscription-Id>/resourceGroups/myResourceGroup/providers/Microsoft.Network/routeTables/myRouteTablePublic
```

輸出會通知您從 myVmWeb 到 myVmMgmt 之流量的下一個躍點 IP 位址是 10.0.2.4 (myVmNva 虛擬機器)，下一個躍點類型是 VirtualAppliance，而造成路由的路由表是 myRouteTablePublic。

每個網路介面的有效路由是由 Azure 的預設路由和您定義的路由所組成。 使用 [Get-AzureRmEffectiveRouteTable](/powershell/module/azurerm.network/get-azurermeffectiveroutetable) 可查看虛擬機器中網路介面的所有有效路由。 例如，若要顯示 myVmWeb 虛擬機器中 myVmWeb 網路介面的有效路由，請輸入下列命令：

```azurepowershell-interactive
Get-AzureRmEffectiveRouteTable `
  -NetworkInterfaceName myVmWeb `
  -ResourceGroupName myResourceGroup `
  | Format-Table
```

所有預設路由與您在上一個步驟中新增的路由都會傳回。

## <a name="clean-up-resources"></a>清除資源

請使用 [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) 來移除不再需要的資源群組以及其所包含的所有資源。

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>後續步驟

在本文中，您已建立路由表並將其與子網路產生關聯。 您已建立網路虛擬設備，它會將來自公用子網路的流量路由傳送至私人子網路。 雖然您可以在虛擬網路內部署許多 Azure 資源，但是某些 Azure PaaS 服務的資源無法部署到虛擬網路。 您仍可將某些 Azure PaaS 服務的資源存取，限制為僅來自虛擬網路子網路的流量。 請繼續閱讀下一篇文章，以了解如何限制對 Azure PaaS 資源的網路存取。

> [!div class="nextstepaction"]
> [限制對 PaaS 資源的網路存取](virtual-network-service-endpoints-configure.md#azure-powershell)