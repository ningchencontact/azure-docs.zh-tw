---
title: 使用加速網路來建立 Azure 虛擬機器 | Microsoft Docs
description: 了解如何使用加速網路建立 Linux 虛擬機器。
services: virtual-network
documentationcenter: ''
author: gsilva5
manager: gedegrac
editor: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 01/04/2018
ms.author: gsilva
ms.openlocfilehash: ef6086afa17f1ab864d70678a6da6df2a78e0c16
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "65190279"
---
# <a name="create-a-windows-virtual-machine-with-accelerated-networking"></a>建立使用加速網路的 Windows 虛擬機器

在本教學課程中，您將了解如何使用加速網路來建立 Windows 虛擬機器 (VM)。 若要建立使用加速網路的 Linux VM，請參閱[建立使用加速網路的 Linux VM](create-vm-accelerated-networking-cli.md)。 加速網路可以對 VM 啟用 Single Root I/O Virtualization (SR-IOV)，大幅提升其網路效能。 這個高效能路徑會略過資料路徑的主機，進而減少延遲、抖動和 CPU 使用率，供支援的 VM 類型中最嚴苛的網路工作負載使用。 下圖顯示兩部 VM 之間的通訊，一部具備加速網路而另一步沒有︰

![比較](./media/create-vm-accelerated-networking/accelerated-networking.png)

如果沒有加速網路，進出 VM 的所有網路流量都必須周遊主機和虛擬交換器。 虛擬交換器對網路流量提供所有原則強制執行，例如網路安全性群組、存取控制清單、隔離性以及其他網路虛擬化服務。 若要深入了解虛擬交換器，請參閱[HYPER-V 網路虛擬化和虛擬交換器](https://technet.microsoft.com/library/jj945275.aspx)。

如果使用加速網路，網路流量就會先送達 VM 的網路介面 (NIC)，然後轉送到 VM。 虛擬交換器套用的所有網路原則現在皆已卸載，並在硬體中套用。 在硬體中套用原則會讓 NIC 略過主機和虛擬交換器，同時在主機中維護套用的所有原則，直接將網路流量轉送到 VM。

加速網路的優點只適用於已啟用此功能的 VM。 為了獲得最佳結果，最好在至少兩部連線到相同 Azure 虛擬網路 (VNet) 的 VM 上啟用此功能。 當透過 VNet 通訊或連線內部部署時，此功能對整體延遲的影響可以降到最低。

## <a name="benefits"></a>優點
* **較低的延遲 / 較高的每秒封包數目 (pps)：** 從資料路徑移除虛擬交換器會減少主機中封包在處理原則時所花的時間，並增加 VM 內可處理的封包數目。
* **減少抖動︰** 虛擬交換器處理視需要套用的原則數量和正在進行處理的 CPU 工作負載而定。 將原則強制執行卸載到硬體透過將封包直接傳遞到 VM、移除主機到 VM 的通訊，以及所有軟體插斷和環境切換，而減少變化。
* **降低 CPU 使用率︰** 略過主機中的虛擬交換器可減少處理網路流量的 CPU 使用率。

## <a name="limitations-and-constraints"></a>限制和條件約束

### <a name="supported-operating-systems"></a>受支援的作業系統
您可以從 Azure 資源庫直接使用下列發行版本：
* **Windows Server 2016 Datacenter** 
* **Windows Server 2012 R2 Datacenter**

### <a name="supported-vm-instances"></a>支援的 VM 執行個體
大多數一般用途和具有 2 個以上 vCPU 的計算最佳化執行個體大小，皆支援加速網路。  這些支援的系列為：D/DSv2 和 F/Fs

在支援超執行緒的執行個體中，加速網路可在具有 4 個以上 vCPU 的 VM 執行個體上進行支援作業。 支援的系列為：D/Dsv3、 E/Esv3、 Fsv2、 Lsv2、 Ms/Mms 和 Ms/Mmsv2。

如需 VM 執行個體的詳細資訊，請參閱 [Windows VM 大小](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。

### <a name="regions"></a>Regions
適用於所有公用 Azure 區域和 Azure 政府雲端。

### <a name="enabling-accelerated-networking-on-a-running-vm"></a>在執行中的 VM 上啟用加速網路
支援的 VM 大小若沒啟用加速網路，則只能在停止或解除配置 VM 時啟用此功能。

### <a name="deployment-through-azure-resource-manager"></a>透過 Azure Resource Manager 進行部署
虛擬機器 (傳統) 無法透過加速網路部署。

## <a name="create-a-windows-vm-with-azure-accelerated-networking"></a>建立使用加速網路的 Azure Windows VM
## <a name="portal-creation"></a>建立入口網站
雖然這篇文章提供具有加速網路，使用 Azure Powershell 建立虛擬機器的步驟，您也可以[使用 Azure 入口網站建立虛擬機器](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。 在中建立虛擬機器，在入口網站時**建立虛擬機器**刀鋒視窗中，選擇**網路** 索引標籤。在此索引標籤中，沒有適合**加速網路**。  如果您已選擇[支援的作業系統](#supported-operating-systems)並[VM 大小](#supported-vm-instances)，此選項便會自動填入為 [開啟]。  如果沒有，則會填入加速網路的 「 關閉 」 選項，並授與使用者為什麼它不會啟用的原因。   
* *附註：* 可以透過入口網站中啟用僅支援的作業系統。  如果您使用自訂映像，而且您的映像支援加速網路，請建立您的 VM 使用 CLI 或 Powershell。 

建立虛擬機器之後，您可以確認加速網路會啟用的下列已啟用加速網路的 [確認] 的指示進行。

## <a name="powershell-creation"></a>Powershell 建立
## <a name="create-a-virtual-network"></a>建立虛擬網路

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

安裝[Azure PowerShell](/powershell/azure/install-az-ps) 1.0.0 版或更新版本。 若要尋找您目前安裝的版本，請執行 `Get-Module -ListAvailable Az`。 如果您需要安裝或升級，安裝最新版的 Az 模組，從[PowerShell 資源庫](https://www.powershellgallery.com/packages/Az)。 在 PowerShell 工作階段中，登入 Azure 帳戶使用[Connect AzAccount](/powershell/module/az.accounts/connect-azaccount)。

在下列範例中，請以您自己的值取代範例參數名稱。 範例參數名稱包含 *myResourceGroup*、*myNic* 和 *myVM*。

使用 [New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup) 來建立資源群組。 下列範例會在 *centralus* 位置建立名為 *myResourceGroup* 的資源群組：

```powershell
New-AzResourceGroup -Name "myResourceGroup" -Location "centralus"
```

首先，建立的子網路設定[新增 AzVirtualNetworkSubnetConfig](/powershell/module/az.Network/New-azVirtualNetworkSubnetConfig)。 下列範例會建立名為 mySubnet  的子網路：

```powershell
$subnet = New-AzVirtualNetworkSubnetConfig `
    -Name "mySubnet" `
    -AddressPrefix "192.168.1.0/24"
```

建立與虛擬網路[新增 AzVirtualNetwork](/powershell/module/az.Network/New-azVirtualNetwork)，以*mySubnet*子網路。

```powershell
$vnet = New-AzVirtualNetwork -ResourceGroupName "myResourceGroup" `
    -Location "centralus" `
    -Name "myVnet" `
    -AddressPrefix "192.168.0.0/16" `
    -Subnet $Subnet
```

## <a name="create-a-network-security-group"></a>建立網路安全性群組

首先，建立網路安全性群組規則與[新增 AzNetworkSecurityRuleConfig](/powershell/module/az.Network/New-azNetworkSecurityRuleConfig)。

```powershell
$rdp = New-AzNetworkSecurityRuleConfig `
    -Name 'Allow-RDP-All' `
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

建立網路安全性群組，其具有[新增 AzNetworkSecurityGroup](/powershell/module/az.Network/New-azNetworkSecurityGroup) ，並將指派*允許 RDP 全部*安全性規則給它。 除了 *Allow-RDP-All* 規則之外，網路安全性群組還包含數個預設規則。 一個預設規則會停用來自網際網路的所有輸入存取，這也是將 *Allow-RDP-All* 指派給網路安全性原則的原因，讓您在建立虛擬機器之後，能夠從遠端連線。

```powershell
$nsg = New-AzNetworkSecurityGroup `
    -ResourceGroupName myResourceGroup `
    -Location centralus `
    -Name "myNsg" `
    -SecurityRules $rdp
```

網路安全性群組，若要建立關聯*mySubnet*子網路[組 AzVirtualNetworkSubnetConfig](/powershell/module/az.Network/Set-azVirtualNetworkSubnetConfig)。 網路安全性群組中的規則適用於子網路中部署的所有資源。

```powershell
Set-AzVirtualNetworkSubnetConfig `
    -VirtualNetwork $vnet `
    -Name 'mySubnet' `
    -AddressPrefix "192.168.1.0/24" `
    -NetworkSecurityGroup $nsg
```

## <a name="create-a-network-interface-with-accelerated-networking"></a>使用加速網路建立網路介面
使用 [New-AzPublicIpAddress](/powershell/module/az.Network/New-azPublicIpAddress) 建立公用 IP 位址。 如果您不打算從網際網路存取虛擬機器，那麼您就不需要公用 IP 位址，但如果要完成本文中的步驟，就會需要公用 IP 位址。

```powershell
$publicIp = New-AzPublicIpAddress `
    -ResourceGroupName myResourceGroup `
    -Name 'myPublicIp' `
    -location centralus `
    -AllocationMethod Dynamic
```

建立的網路介面[新增 AzNetworkInterface](/powershell/module/az.Network/New-azNetworkInterface)且啟用加速網路，並將公用 IP 位址指派給網路介面。 下列範例會在 *myVnet* 虛擬網路的 *mySubnet*子網路中建立名為 *myNic* 的網路介面，並指派*myPublicIp* 公用 IP 位址給它：

```powershell
$nic = New-AzNetworkInterface `
    -ResourceGroupName "myResourceGroup" `
    -Name "myNic" `
    -Location "centralus" `
    -SubnetId $vnet.Subnets[0].Id `
    -PublicIpAddressId $publicIp.Id `
    -EnableAcceleratedNetworking
```

## <a name="create-the-virtual-machine"></a>建立虛擬機器

使用 [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential) 將您的 VM 認證設定為 `$cred` 變數︰

```powershell
$cred = Get-Credential
```

首先，定義您的 VM[新增 AzVMConfig](/powershell/module/az.compute/new-azvmconfig)。 下列範例會使用支援加速網路的 VM 大小來定義名為 *myVM* 的 VM (*Standard_DS4_v2*)：

```powershell
$vmConfig = New-AzVMConfig -VMName "myVm" -VMSize "Standard_DS4_v2"
```

如需所有 VM 大小和特性的清單，請參閱 [Windows VM 大小](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。

使用 [Set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem) 和 [Set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage) 建立其餘的 VM 組態。 下列範例會建立 Windows Server 2016 VM：

```powershell
$vmConfig = Set-AzVMOperatingSystem -VM $vmConfig `
    -Windows `
    -ComputerName "myVM" `
    -Credential $cred `
    -ProvisionVMAgent `
    -EnableAutoUpdate
$vmConfig = Set-AzVMSourceImage -VM $vmConfig `
    -PublisherName "MicrosoftWindowsServer" `
    -Offer "WindowsServer" `
    -Skus "2016-Datacenter" `
    -Version "latest"
```

連接您先前建立的網路介面[新增 AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface):

```powershell
$vmConfig = Add-AzVMNetworkInterface -VM $vmConfig -Id $nic.Id
```

最後，建立您的 VM [New-azvm](/powershell/module/az.compute/new-azvm):

```powershell
New-AzVM -VM $vmConfig -ResourceGroupName "myResourceGroup" -Location "centralus"
```

## <a name="confirm-the-driver-is-installed-in-the-operating-system"></a>確認作業系統中已安裝驅動程式

一旦您在 Azure 中建立 VM，請與 VM 連線，然後確認驅動程式已安裝在 Windows 中。

1. 從網際網路瀏覽器開啟 Azure [入口網站](https://portal.azure.com)，並以您的 Azure 帳戶登入。
2. 在 Azure 入口網站頂端包含「搜尋資源」  文字的方塊中，輸入 myVm  。 當搜尋結果中出現 **myVm** 時，按一下它。 如果在 [連線]  按鈕底下看到 [建立中]  ，則表示 Azure 還未建立好 VM。 請在 [連線]  按鈕底下已沒有 [建立中]  字樣時，才按下概觀左上角的 [連線]  。
3. 輸入您在[建立虛擬機器](#create-the-virtual-machine)中所輸入的使用者名稱和密碼。 如果您從未連線到 Azure 中的 Windows VM，請參閱[連線至虛擬機器](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#connect-to-virtual-machine)。
4. 以滑鼠右鍵按一下 Windows 的 [開始] 按鈕，然後按一下 [裝置管理員]  。 展開 [網路介面卡]  節點。 確認 **Mellanox ConnectX-3 Virtual Function Ethernet Adapter** 有出現，如下圖所示︰

    ![裝置管理員](./media/create-vm-accelerated-networking/device-manager.png)

現在已啟用您 VM 的加速網路。

## <a name="enable-accelerated-networking-on-existing-vms"></a>在現有的 VM 上啟用加速網路
如果您已建立不含加速網路的 VM，那麼在現有 VM 上啟用此功能是可能的。  VM 必須符合前面也說明過的下列必要條件，才能支援加速網路：

* VM 必須是支援加速網路的大小
* VM 必須是支援的 Azure 資源庫映像 (及 Linux 的核心版本)
* 可用性設定組中的所有 VM 或 VMSS 必須先停止/解除配置，然後才能在任何 NIC 上啟用加速網路

### <a name="individual-vms--vms-in-an-availability-set"></a>個別 VM 與可用性設定組中的多個 VM
首先要停止/解除配置 VM 或可用性設定組中的所有 VM：

```azurepowershell
Stop-AzureRmVM -ResourceGroup "myResourceGroup" `
    -Name "myVM"
```

請務必注意，如果您的 VM 是個別建立，沒有使用可用性設定組，則您只需要停止/解除配置該個別 VM，即可啟用加速網路。  如果您的 VM 是使用可用性設定組建立的，則包含在可用性設定組中的所有 VM 皆必須先停止/解除配置之後，才能在任何 NIC 上啟用加速網路。 

停止之後，在您 VM 的 NIC 上啟用加速網路：

```azurepowershell
$nic = Get-AzureRMNetworkInterface -ResourceGroupName "myResourceGroup" `
    -Name "myNic"

$nic.EnableAcceleratedNetworking = $true

$nic | Set-AzureRMNetworkInterface
```

在可用性設定組，集合中的所有 Vm 重新啟動您的 VM 或，如果，並確認已啟用加速網路：

```azurepowershell
Start-AzureRmVM -ResourceGroup "myResourceGroup" `
    -Name "myVM"
```

### <a name="vmss"></a>VMSS
VMSS 稍有不同，但會遵循相同的工作流程。  首先，停止 VM：

```azurepowershell
Stop-AzVmss -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet"
```

停止 VM 後，更新網路介面下的加速網路屬性：

```azurepowershell
$vmss = Get-AzVmss -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet"

$vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].EnableAcceleratedNetworking = $true

Update-AzVmss -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet" `
    -VirtualMachineScaleSet $vmss
```

請注意，VMSS 會升級 VM，其使用三種不同的設定 (自動、輪流及手動) 來套用更新。  將這些指示中的原則設為自動，以便 VMSS 在重新啟動之後，立即採用變更。  將其設定為自動，讓所做的變更可立即採用：

```azurepowershell
$vmss.UpgradePolicy.AutomaticOSUpgrade = $true

Update-AzVmss -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet" `
    -VirtualMachineScaleSet $vmss
```

最後，重新啟動 VMSS：

```azurepowershell
Start-AzVmss -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet"
```

重新啟動後，等候升級完成，但是一旦完成後，VF 會出現在 VM 內。  (請確定您是使用支援的 OS 和 VM 大小。)

### <a name="resizing-existing-vms-with-accelerated-networking"></a>調整使用加速網路的現有 VM 大小

針對已啟用加速網路的 VM，您只能將其大小調整為其他支援加速網路的 VM。  

您無法使用調整作業將已啟用加速網路的 VM 大小，調整為不支援加速網路的 VM 執行個體。  相反的，若要為這些 VM 的其中一個調整大小，請執行下列動作：

* 停止/解除配置 VM 或可用性設定組/VMSS 中的所有 VM。
* 必須在 VM 或可用性設定組/VMSS 中所有 VM 的 NIC 上停用加速網路。
* 一旦停用加速網路之後，VM/可用性設定組/VMSS 就可以移到不支援加速網路的新大小，並重新啟動。
