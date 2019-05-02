---
title: 關聯至虛擬機器的公用 IP 位址
titlesuffix: Azure Virtual Network
description: 了解如何建立關聯的虛擬機器的公用 IP 位址。
services: virtual-network
documentationcenter: ''
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/21/2019
ms.author: kumud
ms.openlocfilehash: 69460a111e6fd879807b4025d6832b3ac515a9b4
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2019
ms.locfileid: "64691998"
---
# <a name="associate-a-public-ip-address-to-a-virtual-machine"></a>關聯至虛擬機器的公用 IP 位址

在本文中，您了解如何將現有的虛擬機器 (VM) 的公用 IP 位址產生關聯。 如果您想要從網際網路連線至 VM，VM 必須有與其相關聯的公用 IP 位址。 如果您想要建立新的 VM 與公用 IP 位址，做法使用[Azure 入口網站](virtual-network-deploy-static-pip-arm-portal.md)，則[Azure 命令列介面 (CLI)](virtual-network-deploy-static-pip-arm-cli.md)，或[PowerShell](virtual-network-deploy-static-pip-arm-ps.md)。 公用 IP 位址需要少許費用。 如需詳細資料，請參閱[定價](https://azure.microsoft.com/pricing/details/ip-addresses/)。 沒有公用 IP 位址，您可以使用每個訂用帳戶數目限制。 如需詳細資訊，請參閱 <<c0> [ 限制](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#publicip-address)。

您可以使用[Azure 入口網站](#azure-portal)，Azure[命令列介面](#azure-cli)(CLI)，或[PowerShell](#powershell)關聯至 VM 的公用 IP 位址。

## <a name="azure-portal"></a>Azure 入口網站

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 瀏覽或搜尋您想要新增公用 IP 位址，然後選取它的虛擬機器。
3. 底下**設定**，選取**網路**，然後選取您想要新增公用 IP 位址，如下圖所示的網路介面：

   ![選取網路介面](./media/associate-public-ip-address-vm/select-nic.png)

   > [!NOTE]
   > 公用 IP 位址是與網路介面連結至 VM。 在上圖中，VM 只有一個網路介面。 如果 VM 有多個網路介面，它們全都會顯示，並選取您想要關聯的公用 IP 位址的網路介面。

4. 選取  **IP 組態**，然後選取 IP 組態，如下圖所示：

   ![選取 IP 組態](./media/associate-public-ip-address-vm/select-ip-configuration.png)

   > [!NOTE]
   > 公用 IP 位址是網路介面的 IP 組態產生關聯。 在上圖中，網路介面會有一個 IP 組態。 如果網路介面有多個 IP 組態，它們全都會顯示在清單中，您會選取您想要關聯的公用 IP 位址的 IP 組態。

5. 選取  **Enabled**，然後選取**IP 位址 (*設定必要設定*)**。 選擇現有的公用 IP 位址，它會自動關閉**選擇公用 IP 位址** 方塊中。 如果您沒有列出任何可用公用 IP 位址，您需要建立一個。 若要深入了解，請參閱[建立公用 IP 位址](virtual-network-public-ip-address.md#create-a-public-ip-address)。 選取 **儲存**、 之後，然後關閉 IP 組態的方塊的圖片所示。

   ![啟用公用 IP 位址](./media/associate-public-ip-address-vm/enable-public-ip-address.png)

   > [!NOTE]
   > 顯示的公用 IP 位址是存在於與 VM 相同的區域。 如果您有多個區域中建立的公用 IP 位址時，所有會出現在這裡。 如果任何呈現灰色，這是因為位址已關聯到另一個資源的支援。

6. 檢視指派給 IP 組態，公用 IP 位址，如下圖所示。 可能需要幾秒鐘，才會顯示 「 IP 位址。

   ![檢視已指派公用 IP 位址](./media/associate-public-ip-address-vm/view-assigned-public-ip-address.png)

   > [!NOTE]
   > 從每個 Azure 區域中使用的位址集區來指派位址。 若要查看每個區域使用的位址集區的清單，請參閱[Microsoft Azure Datacenter IP 範圍](https://www.microsoft.com/download/details.aspx?id=41653)。 指派的位址可以是任何區域使用的集區中的位址。 如果您需要從特定集區的區域中指派的位址時，使用[公用 IP 位址首碼](public-ip-address-prefix.md)。

7. [允許的 vm 網路流量](#allow-network-traffic-to-the-vm)使用中的網路安全性群組安全性規則。

## <a name="azure-cli"></a>Azure CLI

安裝[Azure CLI](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json)，或使用 Azure Cloud Shell。 Azure Cloud Shell 是免費的 Bash Shell，您可以直接在 Azure 入口網站內執行。 它具有預先安裝和設定的 Azure CLI，可與您的帳戶搭配使用。 選取 **試試**CLI 中的按鈕命令的。 選取**試試**叫用可讓您可以使用 Azure 帳戶登入 Cloud Shell。

1. 如果在本機使用 CLI 在 Bash 中，登入 Azure 中使用`az login`。
2. 公用 IP 位址是與連結至 VM 的網路介面的 IP 組態產生關聯。 使用[az 網路 nic ip 設定更新](/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-update)命令，以將公用 IP 位址給 IP 組態產生關聯。 下列範例會將名為現有公用 IP 位址*myVMPublicIP*名為的 IP 組態*ipconfigmyVM*現有的網路介面名稱為*myVMVMNic*存在於資源群組名稱*myResourceGroup*。
  
   ```azurecli-interactive
   az network nic ip-config update \
     --name ipconfigmyVM \
     --nic-name myVMVMNic \
     --resource-group myResourceGroup \
     --public-ip-address myVMPublicIP
   ```

   - 如果您沒有現有的公用 IP 位址，使用[az 網路公用 ip 建立](/cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-create)命令建立一個。 例如，下列命令會建立名為公用 IP 位址*myVMPublicIP*資源群組中名為*myResourceGroup*。
  
     ```azurecli-interactive
     az network public-ip create --name myVMPublicIP --resource-group myResourceGroup
     ```

     > [!NOTE]
     > 前一個命令會建立公用 IP 位址的數項設定，您可能想要自訂預設值。 若要深入了解所有公用 IP 位址設定，請參閱[建立公用 IP 位址](virtual-network-public-ip-address.md#create-a-public-ip-address)。 用於每個 Azure 區域的公用 IP 位址集區指派的位址。 若要查看每個區域使用的位址集區的清單，請參閱[Microsoft Azure Datacenter IP 範圍](https://www.microsoft.com/download/details.aspx?id=41653)。

   - 如果您不知道網路介面連結至 VM 的名稱，使用[az vm nic 清單](/cli/azure/vm/nic?view=azure-cli-latest#az-vm-nic-list)命令來檢視它們。 例如，下列命令會列出連結至 VM，名為的網路介面的名稱*myVM*資源群組中名為*myResourceGroup*:

     ```azurecli-interactive
     az vm nic list --vm-name myVM --resource-group myResourceGroup
     ```

     輸出包含一或多個項目，類似於下列範例：
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
     ```

     在上述範例中， *myVMVMNic*是網路介面的名稱。

   - 如果您不知道網路介面的 IP 組態的名稱，使用[az 網路 nic ip 組態清單](/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-list)命令來擷取它們。 例如，下列命令會列出名為的網路介面的 IP 組態的名稱*myVMVMNic*資源群組中名為*myResourceGroup*:

     ```azurecli-interactive
     az network nic ip-config list --nic-name myVMVMNic --resource-group myResourceGroup --out table
     ```

3. 檢視指派給 IP 組態的公用 IP 位址[az vm 列出 ip 位址](/cli/azure/vm?view=azure-cli-latest#az-vm-list-ip-addresses)命令。 下列範例會示範名為指派給現有的 VM 的 IP 位址*myVM*資源群組中名為*myResourceGroup*。

   ```azurecli-interactive
   az vm list-ip-addresses --name myVM --resource-group myResourceGroup --out table
   ```

   > [!NOTE]
   > 從每個 Azure 區域中使用的位址集區來指派位址。 若要查看每個區域使用的位址集區的清單，請參閱[Microsoft Azure Datacenter IP 範圍](https://www.microsoft.com/download/details.aspx?id=41653)。 指派的位址可以是任何區域使用的集區中的位址。 如果您需要從特定集區的區域中指派的位址時，使用[公用 IP 位址首碼](public-ip-address-prefix.md)。

4. [允許的 vm 網路流量](#allow-network-traffic-to-the-vm)使用中的網路安全性群組安全性規則。

## <a name="powershell"></a>PowerShell

安裝[PowerShell](/powershell/azure/install-az-ps)，或使用 Azure Cloud Shell。 Azure Cloud Shell 是免費的殼層，您可以直接在 Azure 入口網站內執行。 它具有預先安裝和設定的 PowerShell，可與您的帳戶搭配使用。 選取 **試試**在 PowerShell 中的按鈕命令的。 選取**試試**叫用可讓您可以使用 Azure 帳戶登入 Cloud Shell。

1. 如果在本機使用 PowerShell，登入 Azure 中使用`Connect-AzAccount`。
2. 公用 IP 位址是與連結至 VM 的網路介面的 IP 組態產生關聯。 使用[Get AzVirtualNetwork](/powershell/module/Az.Network/Get-AzVirtualNetwork)並[Get AzVirtualNetworkSubnetConfig](/powershell/module/Az.Network/Get-AzVirtualNetworkSubnetConfig)命令來取得虛擬網路和網路介面所在子網路。 接下來，使用[Get AzNetworkInterface](/powershell/module/Az.Network/Get-AzNetworkInterface)命令，以取得網路介面和[Get AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress)命令，以取得現有的公用 IP 位址。 然後使用[組 AzNetworkInterfaceIpConfig](/powershell/module/Az.Network/Set-AzNetworkInterfaceIpConfig)命令，將關聯公用 IP 位址給 IP 組態並[組 AzNetworkInterface](/powershell/module/Az.Network/Set-AzNetworkInterface)寫入至新的 IP 組態的命令網路介面。

   下列範例會將名為現有公用 IP 位址*myVMPublicIP*名為的 IP 組態*ipconfigmyVM*現有的網路介面名稱為*myVMVMNic*存在於名為的子網路*myVMSubnet*一個名為的虛擬網路中*myVMVNet*。 資源群組中的所有資源都皆*myResourceGroup*。
  
   ```azurepowershell-interactive
   $vnet = Get-AzVirtualNetwork -Name myVMVNet -ResourceGroupName myResourceGroup
   $subnet = Get-AzVirtualNetworkSubnetConfig -Name myVMSubnet -VirtualNetwork $vnet
   $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
   $pip = Get-AzPublicIpAddress -Name myVMPublicIP -ResourceGroupName myResourceGroup
   $nic | Set-AzNetworkInterfaceIpConfig -Name ipconfigmyVM -PublicIPAddress $pip -Subnet $subnet
   $nic | Set-AzNetworkInterface
   ```

   - 如果您沒有現有的公用 IP 位址，使用[新增 AzPublicIpAddress](/powershell/module/Az.Network/New-AzPublicIpAddress)命令建立一個。 例如，下列命令會建立*動態*名為的公用 IP 位址*myVMPublicIP*在資源群組中名為*myResourceGroup*在*eastus*區域。
  
     ```azurepowershell-interactive
     New-AzPublicIpAddress -Name myVMPublicIP -ResourceGroupName myResourceGroup -AllocationMethod Dynamic -Location eastus
     ```

     > [!NOTE]
     > 前一個命令會建立公用 IP 位址的數項設定，您可能想要自訂預設值。 若要深入了解所有公用 IP 位址設定，請參閱[建立公用 IP 位址](virtual-network-public-ip-address.md#create-a-public-ip-address)。 用於每個 Azure 區域的公用 IP 位址集區指派的位址。 若要查看每個區域使用的位址集區的清單，請參閱[Microsoft Azure Datacenter IP 範圍](https://www.microsoft.com/download/details.aspx?id=41653)。

   - 如果您不知道網路介面連結至 VM 的名稱，使用[Get AzVM](/powershell/module/Az.Compute/Get-AzVM)命令來檢視它們。 例如，下列命令會列出連結至 VM，名為的網路介面的名稱*myVM*資源群組中名為*myResourceGroup*:

     ```azurepowershell-interactive
     $vm = Get-AzVM -name myVM -ResourceGroupName myResourceGroup
     $vm.NetworkProfile
     ```

     輸出包含一或多個項目，類似於接下來的範例。 在範例輸出中， *myVMVMNic*是網路介面的名稱。
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
     ```

   - 如果您不知道虛擬網路或網路介面所在子網路的名稱，使用`Get-AzNetworkInterface`命令來檢視資訊。 例如，下列命令會取得名為的網路介面的虛擬網路和子網路資訊*myVMVMNic*資源群組中名為*myResourceGroup*:

     ```azurepowershell-interactive
     $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
     $ipConfigs = $nic.IpConfigurations
     $ipConfigs.Subnet | Select Id
     ```

     輸出包含一或多個項目，類似於接下來的範例。 在範例輸出中， *myVMVNET*是虛擬網路的名稱並*myVMSubnet*是子網路的名稱。
  
     ```
     "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVMVNET/subnets/myVMSubnet",
     ```

   - 如果您不知道網路介面的 IP 組態的名稱，使用[Get AzNetworkInterface](/powershell/module/Az.Network/Get-AzNetworkInterface)命令來擷取它們。 例如，下列命令會列出名為的網路介面的 IP 組態的名稱*myVMVMNic*資源群組中名為*myResourceGroup*:

     ```azurepowershell-interactive
     $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
     $nic.IPConfigurations
     ```

     輸出包含一或多個項目，類似於接下來的範例。 在範例輸出中， *ipconfigmyVM* IP 組態的名稱。
  
     ```
     Id     : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic/ipConfigurations/ipconfigmyVM
     ```

3. 檢視指派給 IP 組態的公用 IP 位址[Get AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress)命令。 下列範例會顯示指派給名為 公用 ip 位址*myVMPublicIP*資源群組中名為*myResourceGroup*。

   ```azurepowershell-interactive
   Get-AzPublicIpAddress -Name myVMPublicIP -ResourceGroupName myResourceGroup | Select IpAddress
   ```

   如果您不知道指派給 IP 組態的公用 IP 位址的名稱，請執行下列命令來取得它：

   ```azurepowershell-interactive
   $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
   $nic.IPConfigurations
   $address = $nic.IPConfigurations.PublicIpAddress
   $address | Select Id
   ```

   輸出包含一或多個項目，類似於接下來的範例。 在範例輸出中， *myVMPublicIP*是指派給 IP 組態的公用 IP 位址的名稱。

   ```
   "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myVMPublicIP"
   ```

   > [!NOTE]
   > 從每個 Azure 區域中使用的位址集區來指派位址。 若要查看每個區域使用的位址集區的清單，請參閱[Microsoft Azure Datacenter IP 範圍](https://www.microsoft.com/download/details.aspx?id=41653)。 指派的位址可以是任何區域使用的集區中的位址。 如果您需要從特定集區的區域中指派的位址時，使用[公用 IP 位址首碼](public-ip-address-prefix.md)。

4. [允許的 vm 網路流量](#allow-network-traffic-to-the-vm)使用中的網路安全性群組安全性規則。

## <a name="allow-network-traffic-to-the-vm"></a>允許的 vm 網路流量

您可以從網際網路連線至公用 IP 位址之前，請確定您有任何網路安全性群組，您可能會有相關聯的網路介面、 網路介面所在子網路或兩者中開啟必要的連接埠。 雖然安全性群組篩選流量到私人 IP 位址的網路介面，一旦輸入的網際網路流量送達的公用 IP 位址，Azure 會將轉譯公用位址的私人 IP 解決，因此如果網路安全性群組可防止交通流量的公用 IP 位址的通訊會失敗。 您可以檢視網路介面和它的子網路使用的有效安全性規則[入口網站](diagnose-network-traffic-filter-problem.md#diagnose-using-azure-portal)， [CLI](diagnose-network-traffic-filter-problem.md#diagnose-using-azure-cli)，或[PowerShell](diagnose-network-traffic-filter-problem.md#diagnose-using-powershell)。

## <a name="next-steps"></a>後續步驟

透過網路安全性群組允許輸入的網際網路流量，您的 vm。 若要了解如何建立網路安全性群組，請參閱[使用網路安全性群組](manage-network-security-group.md#work-with-network-security-groups)。 若要深入了解網路安全性群組，請參閱[安全性群組](security-overview.md)。
