---
title: 建立具有靜態私人 IP 位址-Azure PowerShell 的 VM |Microsoft Docs
description: 了解如何使用私人 IP 位址，使用 PowerShell 建立虛擬機器。
services: virtual-network
documentationcenter: na
author: jimdial
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: d5f18929-15e3-40a2-9ee3-8188bc248ed8
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/07/2019
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: 812b3752bfffd16c09b466b036fb0ac91e77d5a4
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/18/2019
ms.locfileid: "58104347"
---
# <a name="create-a-virtual-machine-with-a-static-private-ip-address-using-powershell"></a>使用靜態私人 IP 位址使用 PowerShell 建立虛擬機器

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

您可以使用靜態的私人 IP 位址建立虛擬機器 (VM)。 如果您想要選取哪一個子網路的位址指派給 VM，請指派靜態的私人 IP 位址，而不是動態位址。 深入了解[靜態私人 IP 位址](virtual-network-ip-addresses-overview-arm.md#allocation-method)。 若要變更指派給現有的 VM 中至靜態、 動態私人 IP 位址，或使用公用 IP 位址，請參閱[新增、 變更或移除 IP 位址](virtual-network-network-interface-addresses.md)。

## <a name="create-a-virtual-machine"></a>建立虛擬機器

您可以從本機電腦或使用 Azure Cloud Shell 來完成下列步驟。 若要使用您的本機電腦，請確定已[安裝 Azure PowerShell](/powershell/azure/install-az-ps?toc=%2fazure%2fvirtual-network%2ftoc.json)。 若要使用 Azure Cloud Shell，請選取後續任何命令方塊右上角的 [試試看]。 Cloud Shell 可讓您登入 Azure。

1. 如果使用 Cloud Shell，請跳至步驟 2。 開啟命令工作階段，然後使用 `Connect-AzAccount` 登入 Azure。
2. 使用 [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) 命令來建立資源群組。 下列範例會在美國東部 Azure 區域中建立一個資源群組：

   ```azurepowershell-interactive
   $RgName = "myResourceGroup"
   $Location = "eastus"
   New-AzResourceGroup -Name $RgName -Location $Location
   ```

3. 建立子網路設定和使用虛擬網路[新增 AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig)並[新增 AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork)命令：

   ```azurepowershell-interactive
   # Create a subnet configuration
   $SubnetConfig = New-AzVirtualNetworkSubnetConfig `
   -Name MySubnet `
   -AddressPrefix 10.0.0.0/24

   # Create a virtual network
   $VNet = New-AzVirtualNetwork `
   -ResourceGroupName $RgName `
   -Location $Location `
   -Name MyVNet `
   -AddressPrefix 10.0.0.0/16 `
   -Subnet $subnetConfig

   # Get the subnet object for use in a later step.
   $Subnet = Get-AzVirtualNetworkSubnetConfig -Name $SubnetConfig.Name -VirtualNetwork $VNet
   ```

4. 建立虛擬網路中的網路介面，並將從子網路的私人 IP 位址指派給網路介面[新增 AzNetworkInterfaceIpConfig](/powershell/module/Az.Network/New-AzNetworkInterfaceIpConfig)並[新增 AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface)命令：

   ```azurepowershell-interactive
   $IpConfigName1 = "IPConfig-1"
   $IpConfig1     = New-AzNetworkInterfaceIpConfig `
     -Name $IpConfigName1 `
     -Subnet $Subnet `
     -PrivateIpAddress 10.0.0.4 `
     -Primary

   $NIC = New-AzNetworkInterface `
     -Name MyNIC `
     -ResourceGroupName $RgName `
     -Location $Location `
     -IpConfiguration $IpConfig1
   ```

5. 建立 VM 組態[新增 AzVMConfig](/powershell/module/Az.Compute/New-AzVMConfig)，然後建立 VM [New-azvm](/powershell/module/az.Compute/New-azVM)。 出現提示時，提供使用者名稱和密碼以作為 VM 的登入認證：

   ```azurepowershell-interactive
   $VirtualMachine = New-AzVMConfig -VMName MyVM -VMSize "Standard_DS3"
   $VirtualMachine = Set-AzVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName MyServerVM -ProvisionVMAgent -EnableAutoUpdate
   $VirtualMachine = Add-AzVMNetworkInterface -VM $VirtualMachine -Id $NIC.Id
   $VirtualMachine = Set-AzVMSourceImage -VM $VirtualMachine -PublisherName 'MicrosoftWindowsServer' -Offer 'WindowsServer' -Skus '2012-R2-Datacenter' -Version latest
   New-AzVM -ResourceGroupName $RgName -Location $Location -VM $VirtualMachine -Verbose
   ```

> [!WARNING]
> 雖然您可以將私人 IP 位址設定新增至作業系統，我們建議您沒有這麼做的話在讀取之前[將私人 IP 位址新增至作業系統](virtual-network-network-interface-addresses.md#private)。
> 
> 
> <a name = "change-the-allocation-method-for-a-private-ip-address-assigned-to-a-network-interface"></a>
> 
> [!IMPORTANT]
> 若要從網際網路存取 VM，您必須指派給 VM 的公用 IP 位址。 您也可以變更動態的私人 IP 位址指派至靜態指派。 如需詳細資訊，請參閱 <<c0> [ 新增或變更 IP 位址](virtual-network-network-interface-addresses.md)。 此外，建議您的 VM 來限制網路流量，藉由建立關聯的網路介面、 子網路建立網路介面，或兩者的網路安全性群組。 如需詳細資訊，請參閱 <<c0> [ 管理網路安全性群組](manage-network-security-group.md)。

## <a name="clean-up-resources"></a>清除資源

當不再需要您可以使用[移除 AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup)來移除資源群組和所有其包含之資源：

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>後續步驟

- 深入了解[私人 IP 位址](virtual-network-ip-addresses-overview-arm.md#private-ip-addresses)，並將指派[靜態私人 IP 位址](virtual-network-network-interface-addresses.md#add-ip-addresses)到 Azure 虛擬機器。
- 了解如何建立[Linux](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json)並[Windows](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json)虛擬機器。
