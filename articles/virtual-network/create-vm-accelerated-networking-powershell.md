---
title: "使用加速網路來建立 Azure 虛擬機器 | Microsoft Docs"
description: "了解如何以加速網路建立 Linux 虛擬機器。"
services: virtual-network
documentationcenter: 
author: jdial
manager: jeconnoc
editor: 
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 01/04/2018
ms.author: jimdial
ms.openlocfilehash: f4908963e0650be9b12b745f6868a1ba6ad933e4
ms.sourcegitcommit: d6984ef8cc057423ff81efb4645af9d0b902f843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/05/2018
---
# <a name="create-a-windows-virtual-machine-with-accelerated-networking"></a>建立 Windows 虛擬機器使用加速網路功能

> [!IMPORTANT] 
> 必須具有啟用加速網路建立虛擬機器。 無法在現有的虛擬機器上啟用此功能。 您可以依照下列步驟來啟用加速的網路
>   1. 刪除虛擬機器
>   2. 加速網路啟用與重新建立虛擬機器
>

在本教學課程中，您會學習如何建立 Windows 虛擬機器 (VM) 具有加速網路。 加速網路可以對 VM 啟用 Single Root I/O Virtualization (SR-IOV)，大幅提升其網路效能。 這個高效能的路徑會略過主應用程式資料路徑，從減少延遲、 抖動和支援的 VM 類型的最嚴苛網路工作負載搭配使用的 CPU 使用率。 下圖顯示兩個 Vm 時或無加速網路之間的通訊：

![比較](./media/create-vm-accelerated-networking/accelerated-networking.png)

如果沒有加速網路，進出 VM 的所有網路流量都必須周遊主機和虛擬交換器。 虛擬交換器對網路流量提供所有原則強制執行，例如網路安全性群組、存取控制清單、隔離性以及其他網路虛擬化服務。 若要深入了解虛擬交換器，請閱讀 [Hyper-V Network Virtualization and Virtual Switch (Hyper-V 網路虛擬化和虛擬交換器)](https://technet.microsoft.com/library/jj945275.aspx) 文章。

如果使用加速網路，網路流量就會先送達 VM 的網路介面 (NIC)，然後轉送到 VM。 所有適用於虛擬交換器的網路原則現在會卸載，而套用的硬體。 在硬體中套用原則會讓 NIC 略過主機和虛擬交換器，同時在主機中維護套用的所有原則，直接將網路流量轉送到 VM。

加速網路的優點只適用於已啟用此功能的 VM。 為了獲得最佳結果，最好在至少兩部連線到相同 Azure 虛擬網路 (VNet) 的 VM 上啟用此功能。 當透過 VNet 通訊或連線內部部署時，此功能對整體延遲的影響可以降到最低。

## <a name="benefits"></a>優點
* **較低的延遲 / 較高的每秒封包數目 (pps)：** 從資料路徑移除虛擬交換器會減少主機中封包在處理原則時所花的時間，並增加 VM 內可處理的封包數目。
* **減少抖動︰** 虛擬交換器處理視需要套用的原則數量和正在進行處理的 CPU 工作負載而定。 將原則強制執行卸載到硬體透過將封包直接傳遞到 VM、移除主機到 VM 的通訊，以及所有軟體插斷和環境切換，而減少變化。
* **降低 CPU 使用率︰** 略過主機中的虛擬交換器可減少處理網路流量的 CPU 使用率。

## <a name="supported-operating-systems"></a>受支援的作業系統
Microsoft Windows Server 2012 R2 Datacenter 及 Windows Server 2016。

## <a name="supported-vm-instances"></a>支援的 VM 執行個體
加速網路功能支援大多數的一般用途和 4 或更多 Vcpu 計算最佳化的執行個體大小。 執行個體上例如 D/DSv3 或 E/ESv3 支援超執行緒，加速網路都支援具有 8 個以上 Vcpu 的 VM 執行個體。 支援的數列： D/DSv2、 D/DSv3、 E/ESv3、 F/Fs/Fsv2 和 Ms/Mms。

如需有關 VM 執行個體的詳細資訊，請參閱[Windows VM 大小](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。

## <a name="regions"></a>區域
適用於所有公用 Azure 區域和 Azure 政府雲端。 

## <a name="limitations"></a>限制
使用這項功能時，有下列限制︰

* **網路介面建立︰**您只能對新的 NIC 啟用加速網路。 無法對現有 NIC 來啟用。
* **VM 建立：**啟用加速網路的 NIC 只能在 VM 建立之後附加至 VM。 NIC 無法附加至現有的 VM。 如果將 VM 加入至現有的可用性設定，在可用性設定組的所有 Vm 必須也有都加速網路啟用。
* **部署只透過 Azure 資源管理員：**無法部署虛擬機器 （傳統），以加速網路。

## <a name="create-a-virtual-network"></a>建立虛擬網路

安裝[Azure PowerShell](/powershell/azure/install-azurerm-ps)版本 5.1.1 或更新版本。 若要尋找您目前安裝的版本，請執行`Get-Module -ListAvailable AzureRM`。 如果您需要安裝或升級，請從 [PowerShell 資源庫](https://www.powershellgallery.com/packages/AzureRM)安裝最新版的 AzureRM 模組。 在 PowerShell 工作階段中，登入 Azure 帳戶使用[新增 AzureRmAccount](/powershell/module/AzureRM.Profile/Add-AzureRmAccount)。

在下列範例中，請以您自己的值取代範例參數名稱。 所包含的範例參數名稱*myResourceGroup*， *myNic*，和*myVM*。

使用 [New-AzureRmResourceGroup](/powershell/module/AzureRM.Resources/New-AzureRmResourceGroup)建立資源群組。 下列範例會建立名為的資源群組*myResourceGroup*中*centralus*位置：

```powershell
New-AzureRmResourceGroup -Name "myResourceGroup" -Location "centralus"
```

首先，建立具有的子網路組態[新增 AzureRmVirtualNetworkSubnetConfig](/powershell/module/AzureRM.Network/New-AzureRmVirtualNetworkSubnetConfig)。 下列範例會建立名為的子網路*mySubnet*:

```powershell
$subnet = New-AzureRmVirtualNetworkSubnetConfig `
    -Name "mySubnet" `
    -AddressPrefix "192.168.1.0/24"
```

建立虛擬網路與[新增 AzureRmVirtualNetwork](/powershell/module/AzureRM.Network/New-AzureRmVirtualNetwork)，與*mySubnet*子網路。

```powershell
$vnet = New-AzureRmVirtualNetwork -ResourceGroupName "myResourceGroup" `
    -Location "centralus" `
    -Name "myVnet" `
    -AddressPrefix "192.168.0.0/16" `
    -Subnet $Subnet
```

## <a name="create-a-network-security-group"></a>建立網路安全性群組

首先，建立網路安全性群組規則與[新增 AzureRmNetworkSecurityRuleConfig](/powershell/module/AzureRM.Network/New-AzureRmNetworkSecurityRuleConfig)。

```powershell
$rdp = New-AzureRmNetworkSecurityRuleConfig `
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

建立網路安全性群組與[新增 AzureRmNetworkSecurityGroup](/powershell/module/AzureRM.Network/New-AzureRmNetworkSecurityGroup)並指派*允許 RDP 全都*安全性規則。 除了*允許 RDP 全都*規則的網路安全性群組包含數個預設規則。 一個預設規則會停用所有輸入從網際網路存取，這也是為什麼*允許 RDP 全都*規則指派給網路安全性群組，讓您可以遠端連線到虛擬機器，一旦建立之後。

```powershell
$nsg = New-AzureRmNetworkSecurityGroup `
    -ResourceGroupName myResourceGroup `
    -Location centralus `
    -Name "myNsg" `
    -SecurityRules $rdp
```

建立網路安全性群組關聯*mySubnet*子網路與[組 AzureRmVirtualNetworkSubnetConfig](/powershell/module/AzureRM.Network/Set-AzureRmVirtualNetworkSubnetConfig)。 中的網路安全性群組規則是有效的子網路中部署的所有資源。

```powershell
Set-AzureRmVirtualNetworkSubnetConfig `
    -VirtualNetwork $vnet `
    -Name 'mySubnet' `
    -AddressPrefix "192.168.1.0/24" `
    -NetworkSecurityGroup $nsg
```

## <a name="create-a-network-interface-with-accelerated-networking"></a>建立含加速網路功能的網路介面
使用 [New-AzureRmPublicIpAddress](/powershell/module/AzureRM.Network/New-AzureRmPublicIpAddress) 建立公用 IP 位址。 如果您不打算從網際網路存取虛擬機器，但若要完成這篇文章中的步驟，不需要公用 IP 位址，它是必要。

```powershell
$publicIp = New-AzureRmPublicIpAddress `
    -ResourceGroupName myResourceGroup `
    -Name 'myPublicIp' `
    -location centralus `
    -AllocationMethod Dynamic
```

建立的網路介面[新增 AzureRmNetworkInterface](/powershell/module/AzureRM.Network/New-AzureRmNetworkInterface)與加速網路啟用，並將公用 IP 位址指派給網路介面。 下列範例會建立名為的網路介面*myNic*中*mySubnet*子網路的*myVnet*虛擬網路並指派*myPublicIp*公用 IP 位址給它：

```powershell
$nic = New-AzureRmNetworkInterface `
    -ResourceGroupName "myResourceGroup" `
    -Name "myNic" `
    -Location "centralus" `
    -SubnetId $vnet.Subnets[0].Id `
    -PublicIpAddressId $publicIp.Id `
    -EnableAcceleratedNetworking
```

## <a name="create-the-virtual-machine"></a>建立虛擬機器

設定您 VM 的認證為`$cred`變數使用[Get-credential](/powershell/module/microsoft.powershell.security/get-credential):

```powershell
$cred = Get-Credential
```

首先，會定義具有您 VM[新增 AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig)。 下列範例會定義名為的 VM *myVM*支援加速網路的 VM 大小 (*Standard_DS4_v2*):

```powershell
$vmConfig = New-AzureRmVMConfig -VMName "myVm" -VMSize "Standard_DS4_v2"
```

如需所有 VM 大小和特性的清單，請參閱[Windows VM 大小](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。

使用 [Set-AzureRmVMOperatingSystem](/powershell/module/azurerm.compute/set-azurermvmoperatingsystem) 和 [Set-AzureRmVMSourceImage](/powershell/module/azurerm.compute/set-azurermvmsourceimage) 建立其餘的 VM 組態。 下列範例會建立 Windows Server 2016 VM：

```powershell
$vmConfig = Set-AzureRmVMOperatingSystem -VM $vmConfig `
    -Windows `
    -ComputerName "myVM" `
    -Credential $cred `
    -ProvisionVMAgent `
    -EnableAutoUpdate
$vmConfig = Set-AzureRmVMSourceImage -VM $vmConfig `
    -PublisherName "MicrosoftWindowsServer" `
    -Offer "WindowsServer" `
    -Skus "2016-Datacenter" `
    -Version "latest"
```

將您先前建立的網路介面[新增 AzureRmVMNetworkInterface](/powershell/module/azurerm.compute/add-azurermvmnetworkinterface):

```powershell
$vmConfig = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $nic.Id
```

最後，使用 [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) 建立 VM：

```powershell
New-AzureRmVM -VM $vmConfig -ResourceGroupName "myResourceGroup" -Location "centralus"
```

## <a name="confirm-the-driver-is-installed-in-the-operating-system"></a>確認在作業系統中安裝的驅動程式

一旦您在 Azure 中建立 VM，連接到 VM，然後確認驅動程式已安裝在 Windows 中。 

1. 從網際網路瀏覽器中，開啟 Azure[入口網站](https://portal.azure.com)並以您的 Azure 帳戶登入。
2. 在方塊中包含的文字*搜尋資源*在 Azure 入口網站頂端，輸入*myVm*。 當**myVm**會出現在搜尋結果中，按一下它。 如果**建立**底下看見**連接**按鈕時，Azure 尚未完成建立 VM。 按一下**連接**只之後概觀的左上角中不會再看見**建立**下**連接** 按鈕。
3. 輸入使用者名稱和密碼，您在輸入[建立虛擬機器](#create-the-virtual-machine)。 如果您從未已連接到 Azure 中的 Windows VM，請參閱[連線至虛擬機器](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#connect-to-virtual-machine)。
4. 以滑鼠右鍵按一下 Windows 的 [開始] 按鈕，然後按一下 [裝置管理員]。 展開 [網路介面卡] 節點。 確認 **Mellanox ConnectX-3 Virtual Function Ethernet Adapter** 有出現，如下圖所示︰
   
    ![裝置管理員](./media/create-vm-accelerated-networking/device-manager.png)

現在已啟用您 VM 的加速網路。
