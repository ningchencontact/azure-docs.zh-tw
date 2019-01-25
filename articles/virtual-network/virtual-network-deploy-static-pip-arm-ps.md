---
title: 建立具有靜態公用 IP 位址的 VM - PowerShell | Microsoft Docs
description: 了解如何使用 PowerShell 建立具有靜態公用 IP 位址的 VM。
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ad975ab9-d69f-45c1-9e45-0d3f0f51e87e
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/08/2018
ms.author: jdial
ms.openlocfilehash: f5ddc4a85148cee3e8c8b4d2bf1955f233ebdbc1
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/22/2019
ms.locfileid: "54426517"
---
# <a name="create-a-virtual-machine-with-a-static-public-ip-address-using-powershell"></a>使用 PowerShell 建立具有靜態公用 IP 位址的虛擬機器

您可以建立具有靜態公用 IP 位址的虛擬機器。 公用 IP 位址可讓您從網際網路與虛擬機器通訊。 指派靜態公用 IP 位址 (而非動態位址)，以確保位址永遠不會變更。 深入了解[靜態公用 IP 位址](virtual-network-ip-addresses-overview-arm.md#allocation-method)。 若要將指派給現有虛擬機器的公用 IP 位址從動態變更為靜態，或要處理私人 IP 位址，請參閱[新增、變更或移除 IP 位址](virtual-network-network-interface-addresses.md)。 公用 IP 位址有[象徵性費用](https://azure.microsoft.com/pricing/details/ip-addresses)，而每個訂用帳戶可用的公用 IP 位址數目都有[限制](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)。

## <a name="create-a-virtual-machine"></a>建立虛擬機器

您可以從本機電腦或使用 Azure Cloud Shell 來完成下列步驟。 若要使用您的本機電腦，請確定已[安裝 Azure PowerShell](/powershell/azure/azurerm/install-azurerm-ps?toc=%2fazure%2fvirtual-network%2ftoc.json)。 若要使用 Azure Cloud Shell，請選取後續任何命令方塊右上角的 [試試看]。 Cloud Shell 可讓您登入 Azure。

1. 如果使用 Cloud Shell，請跳至步驟 2。 開啟命令工作階段，然後使用 `Connect-AzureRmAccount` 登入 Azure。
2. 使用 [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) 命令建立資源群組。 下列範例會在美國東部 Azure 區域中建立一個資源群組：

   ```azurepowershell-interactive
   New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
   ```

3. 使用 [New-AzureRmVM](/powershell/module/AzureRM.Compute/New-AzureRmVM) 命令建立虛擬機器。 `-AllocationMethod "Static"` 選項可將靜態公用 IP 位址指派給虛擬機器。 下列範例會建立 Windows Server 虛擬機器，其具有名為 *myPublicIpAddress* 的靜態、基本 SKU 公用 IP 位址。 出現提示時，請提供使用者名稱與密碼以作為虛擬機器的登入認證：

   ```azurepowershell-interactive
   New-AzureRmVm `
     -ResourceGroupName "myResourceGroup" `
     -Name "myVM" `
     -Location "East US" `
     -PublicIpAddressName "myPublicIpAddress" `
     -AllocationMethod "Static"
   ```

   如果公用 IP 位址必須是標準 SKU，您必須在不同的步驟中[建立公用 IP 位址](virtual-network-public-ip-address.md#create-a-public-ip-address)[建立網路介面](virtual-network-network-interface.md#create-a-network-interface)[將公用 IP 位址指派給網路介面](virtual-network-network-interface-addresses.md#add-ip-addresses)，然後[透過網路介面建立虛擬機器](virtual-network-network-interface-vm.md#add-existing-network-interfaces-to-a-new-vm)。 深入了解[公用 IP 位址 SKU](virtual-network-ip-addresses-overview-arm.md#sku)。 如果虛擬機器將會新增至公用 Azure Load Balancer 的後端集區，則虛擬機器公用 IP 位址的 SKU 必須符合負載平衡器公用 IP 位址的 SKU。 如需詳細資訊，請參閱 [Azure Load Balancer](../load-balancer/load-balancer-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#skus)。

4. 使用 [Get-azurermpublicipaddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) 檢視指派的公用 IP 位址，並確認它已建立為靜態位址：

   ```azurepowershell-interactive
   Get-AzureRmPublicIpAddress `
     -ResourceGroupName "myResourceGroup" `
     -Name "myPublicIpAddress" `
     | Select "IpAddress", "PublicIpAllocationMethod" `
     | Format-Table
   ```

   Azure 已從您建立虛擬機器所在區域使用的位址中指派公用 IP 位址。 您可以針對 Azure [公開](https://www.microsoft.com/download/details.aspx?id=56519)、[美國政府](https://www.microsoft.com/download/details.aspx?id=57063)、[中國](https://www.microsoft.com/download/details.aspx?id=57062)及[德國](https://www.microsoft.com/download/details.aspx?id=57064)雲端，下載範圍 (前置詞) 清單。

> [!WARNING]
請勿修改虛擬機器的作業系統內的 IP 位址設定。 作業系統不會察覺 Azure 公用 IP 位址。 雖然您可以將私人 IP 位址設定新增至作業系統，但是建議不要這麼做 (除非有需要)，而且在閱讀[將私人 IP 位址新增至作業系統](virtual-network-network-interface-addresses.md#private)之後才能這麼做。

## <a name="clean-up-resources"></a>清除資源

您可以使用 [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) 來移除不再需要的資源群組，以及其所包含的所有資源：

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>後續步驟

- 深入了解 Azure 中的[公用 IP 位址](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses)
- 深入了解所有[公用 IP 位址設定](virtual-network-public-ip-address.md#create-a-public-ip-address)
- 深入了解[私人 IP 位址](virtual-network-ip-addresses-overview-arm.md#private-ip-addresses)，並將[靜態私人 IP 位址](virtual-network-network-interface-addresses.md#add-ip-addresses)指派給 Azure 虛擬機器
- 深入了解如何建立 [Linux](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 和 [Windows](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 虛擬機器