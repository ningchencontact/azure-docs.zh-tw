---
title: 部署 IPv6 雙重堆疊應用程式在 Azure 虛擬網路-PowerShell
titlesuffix: Azure Virtual Network
description: 這篇文章示範如何部署 IPv6 雙重堆疊應用程式使用 Azure Powershell 的 Azure 虛擬網路中。
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/22/2019
ms.author: kumud
ms.openlocfilehash: 5ef051f42f3d092cc1d88008eaa8af981684ac6c
ms.sourcegitcommit: 1aefdf876c95bf6c07b12eb8c5fab98e92948000
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/06/2019
ms.locfileid: "66730046"
---
# <a name="deploy-an-ipv6-dual-stack-application-in-azure---powershell-preview"></a>部署在 Azure PowerShell （預覽） 中的 IPv6 雙重堆疊應用程式

本文說明如何部署雙重堆疊 （IPv4 + IPv6） 中的應用程式包含雙重堆疊的虛擬網路和子網路，使用雙 （IPv4 + IPv6） 前端組態，具有有雙重 IP 組態的 Nic 的 Vm 負載平衡器的 Azure 網路安全性群組和公用 Ip。

> [!Important]
> Azure 虛擬網路的 IPv6 支援目前處於公開預覽狀態。 此預覽版是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽專用的補充使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果您選擇要安裝在本機使用 PowerShell，本文需要 Azure PowerShell 模組版本 6.9.0 或更新版本。 執行 `Get-Module -ListAvailable Az` 來了解安裝的版本。 如果您需要升級，請參閱[安裝 Azure PowerShell 模組](/powershell/azure/install-Az-ps)。 如果您在本機執行 PowerShell，則也需要執行 `Connect-AzAccount` 以建立與 Azure 的連線。

## <a name="prerequisites"></a>必要條件
部署在 Azure 中的雙重堆疊應用程式之前，您必須設定這項預覽功能使用下列 Azure PowerShell 的訂用帳戶：

註冊，如下所示：
```azurepowershell
Register-AzProviderFeature -FeatureName AllowIPv6VirtualNetwork -ProviderNamespace Microsoft.Network
```
需要 30 分鐘才能完成功能註冊。 您可以執行下列 Azure PowerShell 命令來檢查您的註冊狀態：檢查註冊，如下所示：
```azurepowershell
Get-AzProviderFeature -FeatureName AllowIPv6VirtualNetwork -ProviderNamespace Microsoft.Network
```
註冊完成之後，請執行下列命令：

```azurepowershell
Register-AzResourceProvider -ProviderNamespace Microsoft.Network
```

## <a name="create-a-resource-group"></a>建立資源群組

您可以建立雙重堆疊虛擬網路之前，您必須建立的資源群組[新增 AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)。 下列範例會建立名為的資源群組*myRGDualStack*中*東部我們*位置：

```azurepowershell-interactive
   $rg = New-AzResourceGroup `
  -ResourceGroupName "dsRG1"  `
  -Location "east us"
```

## <a name="create-ipv4-and-ipv6-public-ip-addresses"></a>建立 IPv4 和 IPv6 公用 IP 位址
若要從網際網路存取您的虛擬機器，您會需要 IPv4 和 IPv6 公用 IP 位址的負載平衡器。 建立具有公用 IP 位址[新增 AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress)。 下列範例會建立 IPv4 和 IPv6 公用 IP 位址名為*dsPublicIP_v4*並*dsPublicIP_v6*中*dsRG1*資源群組：

```azurepowershell-interactive
$PublicIP_v4 = New-AzPublicIpAddress `
  -Name "dsPublicIP_v4" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -AllocationMethod Dynamic `
  -IpAddressVersion IPv4
  
$PublicIP_v6 = New-AzPublicIpAddress `
  -Name "dsPublicIP_v6" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -AllocationMethod Dynamic `
  -IpAddressVersion IPv6
```
若要存取您的虛擬機器使用 RDP 連線，建立虛擬機器，使用 IPV4 公用 IP 位址[新增 AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress)。

```azurepowershell-interactive
  $RdpPublicIP_1 = New-AzPublicIpAddress `
  -Name "RdpPublicIP_1" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -AllocationMethod Dynamic `
  -IpAddressVersion IPv4
  
  $RdpPublicIP_2 = New-AzPublicIpAddress `
   -Name "RdpPublicIP_2" `
   -ResourceGroupName $rg.ResourceGroupName `
   -Location $rg.Location  `
   -AllocationMethod Dynamic `
   -IpAddressVersion IPv4
```

## <a name="create-basic-load-balancer"></a>建立基本負載平衡器

在本節中，您會設定 （IPv4 和 IPv6） 的雙重的前端 IP 與負載平衡器的後端位址集區，然後再建立基本負載平衡器。

### <a name="create-front-end-ip"></a>建立前端 IP

使用 [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig) 建立前端 IP。 下列範例會建立 IPv4 和 IPv6 前端 IP 組態，名為*dsLbFrontEnd_v4*並*dsLbFrontEnd_v6*:

```azurepowershell-interactive
$frontendIPv4 = New-AzLoadBalancerFrontendIpConfig `
  -Name "dsLbFrontEnd_v4" `
  -PublicIpAddress $PublicIP_v4

$frontendIPv6 = New-AzLoadBalancerFrontendIpConfig `
  -Name "dsLbFrontEnd_v6" `
  -PublicIpAddress $PublicIP_v6

```

### <a name="configure-back-end-address-pool"></a>設定後端位址集區

使用 [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig) 建立後端位址集區。 在其餘步驟中，VM 會連結至此後端集區。 下列範例會建立名為的後端位址集區*dsLbBackEndPool_v4*並*dsLbBackEndPool_v6*來包含使用 IPV4 和 IPv6 NIC 組態的 Vm:

```azurepowershell-interactive
$backendPoolv4 = New-AzLoadBalancerBackendAddressPoolConfig `
-Name "dsLbBackEndPool_v4"

$backendPoolv6 = New-AzLoadBalancerBackendAddressPoolConfig `
-Name "dsLbBackEndPool_v6"
```

### <a name="create-a-load-balancer-rule"></a>建立負載平衡器規則

負載平衡器規則用來定義如何將流量分散至 VM。 您可定義連入流量的前端 IP 組態及後端 IP 集區來接收流量，以及所需的來源和目的地連接埠。 要確定只有狀況良好 Vm 可接收流量，您可以選擇性地定義健康狀態探查。 基本 load balancer 會使用 IPv4 探查，來評估的 Vm 上的 IPv4 和 IPv6 端點的健全狀況。 標準 load balancer 支援明確 IPv6 健康情況探查。

使用 [Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/add-azloadbalancerruleconfig) 建立負載平衡器規則。 下列範例會建立名為負載平衡器規則*dsLBrule_v4*並*dsLBrule_v6*並在平衡流量*TCP*連接埠*80*至IPv4 和 IPv6 前端 IP 組態：

```azurepowershell-interactive
$lbrule_v4 = New-AzLoadBalancerRuleConfig `
  -Name "dsLBrule_v4" `
  -FrontendIpConfiguration $frontendIPv4 `
  -BackendAddressPool $backendPoolv4 `
  -Protocol Tcp `
  -FrontendPort 80 `
  -BackendPort 80

$lbrule_v6 = New-AzLoadBalancerRuleConfig `
  -Name "dsLBrule_v6" `
  -FrontendIpConfiguration $frontendIPv6 `
  -BackendAddressPool $backendPoolv6 `
  -Protocol Tcp `
  -FrontendPort 80 `
  -BackendPort 80
```

### <a name="create-load-balancer"></a>建立負載平衡器

使用 [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer) 建立基本負載平衡器。 下列範例會建立公用基本負載平衡器*myLoadBalancer*使用 IPv4 和 IPv6 前端 IP 組態、 後端集區和您在先前步驟中建立的負載平衡規則：

```azurepowershell-interactive
$lb = New-AzLoadBalancer `
-ResourceGroupName $rg.ResourceGroupName `
-Location $rg.Location  `
-Name "MyLoadBalancer" `
-Sku "Basic" `
-FrontendIpConfiguration $frontendIPv4,$frontendIPv6 `
-BackendAddressPool $backendPoolv4,$backendPoolv6 `
-LoadBalancingRule $lbrule_v4,$lbrule_v6

```

## <a name="create-network-resources"></a>建立網路資源
在您部署一些 Vm，並可以測試您的平衡器之前，您必須建立支援網路資源的可用性設定組、 網路安全性群組、 虛擬網路與虛擬 Nic。 
### <a name="create-an-availability-set"></a>建立可用性設定組
若要改善您應用程式的高可用性，請將 VM 放在可用性設定組中。

使用 [New-AzAvailabilitySet](/powershell/module/az.compute/new-azavailabilityset) 建立可用性設定組。 下列範例會建立名為 myAvailabilitySet  的可用性設定組：

```azurepowershell-interactive
$avset = New-AzAvailabilitySet `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -Name "dsAVset" `
  -PlatformFaultDomainCount 2 `
  -PlatformUpdateDomainCount 2 `
  -Sku aligned
```

### <a name="create-network-security-group"></a>建立網路安全性群組

建立網路安全性群組的規則，將會管理您的 VNET 中的輸入和輸出通訊。

#### <a name="create-a-network-security-group-rule-for-port-3389"></a>建立連接埠 3389 的網路安全性群組規則

使用 [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) 建立網路安全性群組規則，以允許透過連接埠 3389 的 RDP 連線。

```azurepowershell-interactive
$rule1 = New-AzNetworkSecurityRuleConfig `
-Name 'myNetworkSecurityGroupRuleRDP' `
-Description 'Allow RDP' `
-Access Allow `
-Protocol Tcp `
-Direction Inbound `
-Priority 100 `
-SourceAddressPrefix * `
-SourcePortRange * `
-DestinationAddressPrefix * `
-DestinationPortRange 3389
```
#### <a name="create-a-network-security-group-rule-for-port-80"></a>建立連接埠 80 的網路安全性群組規則

建立網路安全性群組規則以允許網際網路連線，透過的通訊埠 80[新增 AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig)。

```azurepowershell-interactive
$rule2 = New-AzNetworkSecurityRuleConfig `
  -Name 'myNetworkSecurityGroupRuleHTTP' `
  -Description 'Allow HTTP' `
  -Access Allow `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 200 `
  -SourceAddressPrefix * `
  -SourcePortRange 80 `
  -DestinationAddressPrefix * `
  -DestinationPortRange 80
```
#### <a name="create-a-network-security-group"></a>建立網路安全性群組

使用 [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) 建立網路安全性群組。

```azurepowershell-interactive
$nsg = New-AzNetworkSecurityGroup `
-ResourceGroupName $rg.ResourceGroupName `
-Location $rg.Location  `
-Name "dsNSG1"  `
-SecurityRules $rule1,$rule2
```
### <a name="create-a-virtual-network"></a>建立虛擬網路

使用 [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) 建立虛擬網路。 下列範例會建立名為 myVnet  的虛擬網路和 mySubnet  ：

```azurepowershell-interactive
# Create dual stack subnet
$subnet = New-AzVirtualNetworkSubnetConfig `
-Name "dsSubnet" `
-AddressPrefix "10.0.0.0/24","ace:cab:deca:deed::/64"

# Create the virtual network
$vnet = New-AzVirtualNetwork `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -Name "dsVnet" `
  -AddressPrefix "10.0.0.0/16","ace:cab:deca::/48"  `
  -Subnet $subnet
```

### <a name="create-nics"></a>建立 NIC

建立與虛擬 Nic[新增 AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface)。 下列範例會建立兩個虛擬 Nic 這兩個具有 IPv4 和 IPv6 組態。 (您在下列步驟中針對應用程式建立的每部 VM 都有一個虛擬 NIC)。

```azurepowershell-interactive
  $Ip4Config=New-AzNetworkInterfaceIpConfig `
    -Name dsIp4Config `
    -Subnet $vnet.subnets[0] `
    -PrivateIpAddressVersion IPv4 `
    -LoadBalancerBackendAddressPool $backendPoolv4 `
    -PublicIpAddress  $RdpPublicIP_1
    
  $Ip6Config=New-AzNetworkInterfaceIpConfig `
    -Name dsIp6Config `
    -Subnet $vnet.subnets[0] `
    -PrivateIpAddressVersion IPv6 `
    -LoadBalancerBackendAddressPool $backendPoolv6
    
  $NIC_1 = New-AzNetworkInterface `
    -Name "dsNIC1" `
    -ResourceGroupName $rg.ResourceGroupName `
    -Location $rg.Location  `
    -NetworkSecurityGroupId $nsg.Id `
    -IpConfiguration $Ip4Config,$Ip6Config 
    
  $Ip4Config=New-AzNetworkInterfaceIpConfig `
    -Name dsIp4Config `
    -Subnet $vnet.subnets[0] `
    -PrivateIpAddressVersion IPv4 `
    -LoadBalancerBackendAddressPool $backendPoolv4 `
    -PublicIpAddress  $RdpPublicIP_2  

  $NIC_2 = New-AzNetworkInterface `
    -Name "dsNIC2" `
    -ResourceGroupName $rg.ResourceGroupName `
    -Location $rg.Location  `
    -NetworkSecurityGroupId $nsg.Id `
    -IpConfiguration $Ip4Config,$Ip6Config 

```

### <a name="create-virtual-machines"></a>建立虛擬機器

使用 [Get-credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential) 來設定 VM 的系統管理員使用者名稱和密碼：

```azurepowershell-interactive
$cred = get-credential -Message "DUAL STACK VNET SAMPLE:  Please enter the Administrator credential to log into the VMs."
```

現在您可以使用 [New-AzVM](/powershell/module/az.compute/new-azvm) 建立 VM。 下列範例會建立兩個 VM 及必要的虛擬網路元件 (如果尚未存在)。 

```azurepowershell-interactive
$vmsize = "Standard_A2"
$ImagePublisher = "MicrosoftWindowsServer"
$imageOffer = "WindowsServer"
$imageSKU = "2016-Datacenter"

$vmName= "dsVM1"
$VMconfig1 = New-AzVMConfig -VMName $vmName -VMSize $vmsize -AvailabilitySetId $avset.Id 3> $null | Set-AzVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent 3> $null | Set-AzVMSourceImage -PublisherName $ImagePublisher -Offer $imageOffer -Skus $imageSKU -Version "latest" 3> $null | Set-AzVMOSDisk -Name "$vmName.vhd" -CreateOption fromImage  3> $null | Add-AzVMNetworkInterface -Id $NIC_1.Id  3> $null 
$VM1 = New-AzVM -ResourceGroupName $rg.ResourceGroupName  -Location $rg.Location  -VM $VMconfig1 

$vmName= "dsVM2"
$VMconfig2 = New-AzVMConfig -VMName $vmName -VMSize $vmsize -AvailabilitySetId $avset.Id 3> $null | Set-AzVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent 3> $null | Set-AzVMSourceImage -PublisherName $ImagePublisher -Offer $imageOffer -Skus $imageSKU -Version "latest" 3> $null | Set-AzVMOSDisk -Name "$vmName.vhd" -CreateOption fromImage  3> $null | Add-AzVMNetworkInterface -Id $NIC_2.Id  3> $null 
$VM2 = New-AzVM -ResourceGroupName $rg.ResourceGroupName  -Location $rg.Location  -VM $VMconfig2
```

## <a name="determine-ip-addresses-of-the-ipv4-and-ipv6-endpoints"></a>判斷一個 IPv4 和 IPv6 端點的 IP 位址
取得要彙總的 IP 的資源群組中的所有網路介面物件與此部署中使用`get-AzNetworkInterface`。 此外，還可使用 IPv4 和 IPv6 端點的負載平衡器的前端位址`get-AzpublicIpAddress`。

```azurepowershell-interactive
$rgName= "dsRG1"
$NICsInRG= get-AzNetworkInterface -resourceGroupName $rgName 
write-host `nSummary of IPs in this Deployment: 
write-host ******************************************
foreach ($NIC in $NICsInRG) {
 
    $VMid= $NIC.virtualmachine.id 
    $VMnamebits= $VMid.split("/") 
    $VMname= $VMnamebits[($VMnamebits.count-1)] 
    write-host `nPrivate IP addresses for $VMname 
    $IPconfigsInNIC= $NIC.IPconfigurations 
    foreach ($IPconfig in $IPconfigsInNIC) {
 
        $IPaddress= $IPconfig.privateipaddress 
        write-host "    "$IPaddress 
        IF ($IPconfig.PublicIpAddress.ID) {
 
            $IDbits= ($IPconfig.PublicIpAddress.ID).split("/")
            $PipName= $IDbits[($IDbits.count-1)]
            $PipObject= get-azPublicIpAddress -name $PipName -resourceGroup $rgName
            write-host "    "RDP address:  $PipObject.IpAddress
                 }
         }
 }
 
 
 
  write-host `nPublic IP addresses on Load Balancer:
 
  (get-AzpublicIpAddress -resourcegroupname $rgName | where { $_.name -notlike "RdpPublicIP*" }).IpAddress
```
下圖顯示範例輸出會列出兩個 Vm 的私人 IPv4 和 IPv6 位址和負載平衡器前端 IPv4 和 IPv6 IP 位址。

![IP 在 Azure 中的雙重堆疊 (IPv4/IPv6) 的應用程式部署摘要](./media/virtual-network-ipv4-ipv6-dual-stack-powershell/dual-stack-application-summary.png)

## <a name="view-ipv6-dual-stack-virtual-network-in-azure-portal"></a>在 Azure 入口網站中檢視虛擬網路的 IPv6 雙重堆疊
您可以在 Azure 入口網站中檢視 IPv6 雙重堆疊的虛擬網路，如下所示：
1. 在入口網站的搜尋列中，輸入*dsVnet*。
2. 當搜尋結果中出現 **myVirtualNetwork** 時加以選取。 這會啟動**概觀**名為雙重堆疊虛擬網路頁面*dsVnet*。 雙重堆疊的虛擬網路位於名為雙重堆疊子網路的 IPv4 和 IPv6 組態會顯示兩個 Nic *dsSubnet*。

  ![在 Azure 中 IPv6 雙重堆疊虛擬網路](./media/virtual-network-ipv4-ipv6-dual-stack-powershell/dual-stack-vnet.png)

> [!NOTE]
> Azure 虛擬網路的 IPv6 是在 Azure 入口網站中，您可以使用此預覽版本為唯讀。

## <a name="clean-up-resources"></a>清除資源

當不再需要時，您可以使用 [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) 命令來移除資源群組、VM 及所有相關資源。

```azurepowershell-interactive
Remove-AzResourceGroup -Name dsRG1
```

## <a name="next-steps"></a>後續步驟

在本文中中,，您可以建立基本的 Load Balancer 的雙重的前端 IP 設定 （IPv4 和 IPv6）。 您也會建立包含 Nic 具有雙重 IP 組態 （IPV4 + IPv6） 新增至負載平衡器的後端集區的兩部虛擬機器。 若要深入了解 Azure 虛擬網路中的 IPv6 支援，請參閱[什麼是 Azure 虛擬網路的 IPv6？](ipv6-overview.md)