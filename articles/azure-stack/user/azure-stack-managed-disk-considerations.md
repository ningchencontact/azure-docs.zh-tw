---
title: Azure Stack 中受控磁碟和受控映像的差異與考量 | Microsoft Docs
description: 使用 Azure Stack 中的受控磁碟和受控映像時，瞭解有關差異和注意事項。
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/23/2019
ms.author: sethm
ms.reviewer: jiahan
ms.lastreviewed: 03/23/2019
ms.openlocfilehash: c1975c885efc0a2a22b2ab478f8bc9afbcc8bce3
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/25/2019
ms.locfileid: "58400369"
---
# <a name="azure-stack-managed-disks-differences-and-considerations"></a>Azure Stack 受控磁碟：差異與注意事項

本文摘要說明 [Azure Stack 受控磁碟](azure-stack-manage-vm-disks.md)和 [Azure 受控磁碟](../../virtual-machines/windows/managed-disks-overview.md)之間的已知差異。 若要深入了解 Azure Stack 與 Azure 之間的大致差異，請參閱[主要考量](azure-stack-considerations.md)文章。

受控磁碟會管理與 VM 磁碟相關的[儲存體帳戶](../azure-stack-manage-storage-accounts.md)，從而簡化 IaaS VM 的磁碟管理。

> [!Note]  
> Azure Stack 上的受控磁碟可從 1808 更新取得使用。 使用從 1811 更新版本中的 Azure Stack 入口網站來建立虛擬機器時，依預設會啟用此受控磁碟。
  
## <a name="cheat-sheet-managed-disk-differences"></a>速查表：受控磁碟的差異

| 功能 | Azure (全域) | Azure Stack |
| --- | --- | --- |
|待用資料的加密 |Azure 儲存體服務加密 (SSE)、Azure 磁碟加密 (ADE)     |BitLocker 128 位元 AES 加密      |
|映像          | 支援受控自訂映像 |支援|
|備份選項 |支援 Azure 備份服務 |尚不支援 |
|災害復原選項 |支援 Azure Site Recovery |尚不支援|
|磁碟類型     |進階 SSD、標準 SSD (預覽) 和標準 HDD |進階 SSD、標準 HDD |
|進階磁碟  |完全支援 |可佈建，但無效能限制或保證  |
|進階磁碟 IOP  |取決於磁碟大小  |每一磁碟 2300 個 IOP |
|進階磁碟輸送量 |取決於磁碟大小 |每個磁碟 145 MB/秒 |
|磁碟大小  |Azure 進階磁碟：P4 (32 GiB) 至 P80 (32 TiB)<br>Azure 標準 SSD 磁碟：E10 (128 GiB) 至 E80 (32 TiB)<br>Azure 標準 HDD 磁碟：S4 (32 GiB) 至 S80 (32 TiB) |M4：32 GiB<br>M6：64 GiB<br>M10：128 GB<br>M15：256 GiB<br>M20：512 GB<br>M30：1024 GiB |
|磁碟快照複製|支援對連結至執行中 VM 的 Azure 受控磁碟進行快照|尚不支援 |
|磁碟效能分析 |彙總計量和每個磁碟支援的計量 |尚不支援 |
|移轉      |提供從現有非受控 Azure Resource Manager VM 進行移轉的工具，而不需要重新建立 VM  |尚不支援 |

> [!NOTE]  
> Azure Stack 中的受控磁碟 IOP 和輸送量是數量上限而不是佈建數量，可能會受到在 Azure Stack 中執行的硬體和工作負載影響。

## <a name="metrics"></a>度量

儲存體計量也有些差異：

- 使用 Azure Stack，儲存體度量中的交易資料並不區分內部或外部網路頻寬。
- 儲存體度量中的 Azure Stack 交易資料並不包含虛擬機器對所掛接磁碟的存取。

## <a name="api-versions"></a>API 版本

Azure Stack 受控磁碟支援下列 API 版本：

- 2017-03-30
- 2017-12-01

## <a name="convert-to-managed-disks"></a>轉換為受控磁碟

您可以使用下列指令碼，將目前佈建的 VM 從非受控磁碟轉換為受控磁碟。 請將預留位置取代為您自己的值：

```powershell
$subscriptionId = 'subid'

# The name of your resource group
$resourceGroupName ='rgmgd'

# The name of the managed disk
$diskName = 'unmgdvm'

# The size of the disks in GB. It should be greater than the VHD file size.
$diskSize = '50'

# The URI of the VHD file that will be used to create the managed disk.
# The VHD file can be deleted as soon as the managed disk is created.
$vhdUri = 'https://rgmgddisks347.blob.local.azurestack.external/vhds/unmgdvm20181109013817.vhd' 

# The storage type for the managed disk; PremiumLRS or StandardLRS.
$accountType = 'StandardLRS'

# The Azure Stack location where the managed disk is located.
# The location should be the same as the location of the storage account in which VHD file is stored.
$location = 'local'
$virtualMachineName = 'mgdvm'
$virtualMachineSize = 'Standard_D1'
$pipname = 'unmgdvm-ip'
$virtualNetworkName = 'rgmgd-vnet'
$nicname = 'unmgdvm295'

# Set the context to the subscription ID in which the managed disk will be created
Select-AzureRmSubscription -SubscriptionId $SubscriptionId

$diskConfig = New-AzureRmDiskConfig -AccountType $accountType  -Location $location -DiskSizeGB $diskSize -SourceUri $vhdUri -CreateOption Import

# Create managed disk
New-AzureRmDisk -DiskName $diskName -Disk $diskConfig -ResourceGroupName $resourceGroupName
$disk = get-azurermdisk -DiskName $diskName -ResourceGroupName $resourceGroupName
$VirtualMachine = New-AzureRmVMConfig -VMName $virtualMachineName -VMSize $virtualMachineSize

# Use the managed disk resource ID to attach it to the virtual machine.
# Change the OS type to Linux if the OS disk has Linux OS.
$VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -ManagedDiskId $disk.Id -CreateOption Attach -Linux

# Create a public IP for the VM
$publicIp = Get-AzureRmPublicIpAddress -Name $pipname -ResourceGroupName $resourceGroupName 

# Get the virtual network where the virtual machine will be hosted
$vnet = Get-AzureRmVirtualNetwork -Name $virtualNetworkName -ResourceGroupName $resourceGroupName

# Create NIC in the first subnet of the virtual network
$nic = Get-AzureRmNetworkInterface -Name $nicname -ResourceGroupName $resourceGroupName

$VirtualMachine = Add-AzureRmVMNetworkInterface -VM $VirtualMachine -Id $nic.Id

# Create the virtual machine with managed disk
New-AzureRmVM -VM $VirtualMachine -ResourceGroupName $resourceGroupName -Location $location
```

## <a name="managed-images"></a>受控映像

Azure Stack 支援*受控映像*，可讓您在一般化 VM (非受控和受控) 上建立受控映像物件，往後只能建立受控磁碟 VM。 受控映像會啟用下列兩種案例：

- 您已一般化非受控 VM，而且從現在開始想要使用受控磁碟。
- 您有一個一般化的受控 VM，並想要建立多個類似的受控 VM。

### <a name="step-1-generalize-the-vm"></a>步驟 1：一般化 VM

若使用 Windows，請遵循[使用 Sysprep 將 Windows VM 一般化](/azure/virtual-machines/windows/capture-image-resource#generalize-the-windows-vm-using-sysprep)一節進行。 若使用 Linux，請遵循[此處](/azure/virtual-machines/linux/capture-image#step-1-deprovision-the-vm)的步驟 1。

> [!NOTE]
> 請務必將 VM 一般化。 使用未一般化的映像建立 VM 會導致 **VMProvisioningTimeout** 錯誤。

### <a name="step-2-create-the-managed-image"></a>步驟 2：建立受控映像

您可以使用入口網站、PowerShell 或 CLI 來建立受控映像。 遵循[此處](/azure/virtual-machines/windows/capture-image-resource)的 Azure 文章內容進行。

### <a name="step-3-choose-the-use-case"></a>步驟 3：選擇使用案例

#### <a name="case-1-migrate-unmanaged-vms-to-managed-disks"></a>案例 1：將非受控 VM 移轉到受控磁碟

執行此步驟之前，請務必先正確地將 VM 一般化。 一般化之後，您就無法再使用此 VM。 使用未一般化的映像建立 VM 會導致 **VMProvisioningTimeout** 錯誤。

請依照[這裡](../../virtual-machines/windows/capture-image-resource.md#create-an-image-from-a-vhd-in-a-storage-account)的指示，從儲存體帳戶中的一般化 VHD 建立受控映像。 此映像可用於建立接下來的受控 VM。

#### <a name="case-2-create-managed-vm-from-managed-image-using-powershell"></a>案例 2︰使用 PowerShell 從受控映像建立受控 VM

使用[這裡](../../virtual-machines/windows/capture-image-resource.md#create-an-image-from-a-managed-disk-using-powershell)的指令碼從現有受控磁碟 VM 建立映像後，下列範例指令碼會從現有的映像物件建立類似的 Linux VM：

Azure Stack PowerShell 模組 1.7.0 或更新版本：請遵循[此處](../../virtual-machines/windows/create-vm-generalized-managed.md)的指示進行。

Azure Stack PowerShell 模組 1.6.0 或更新版本：

```powershell
# Variables for common values
$resourceGroup = "myResourceGroup"
$location = "redmond"
$vmName = "myVM"
$imagerg = "managedlinuxrg"
$imagename = "simplelinuxvmm-image-2019122"

# Create user object
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

# Create a resource group
New-AzureRmResourceGroup -Name $resourceGroup -Location $location

# Create a subnet configuration
$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24

# Create a virtual network
$vnet = New-AzureRmVirtualNetwork -ResourceGroupName $resourceGroup -Location $location `
  -Name MYvNET -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig

# Create a public IP address and specify a DNS name
$pip = New-AzureRmPublicIpAddress -ResourceGroupName $resourceGroup -Location $location `
  -Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4

# Create an inbound network security group rule for port 3389
$nsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleRDP  -Protocol Tcp `
  -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 3389 -Access Allow

# Create a network security group
$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $resourceGroup -Location $location `
  -Name myNetworkSecurityGroup -SecurityRules $nsgRuleRDP

# Create a virtual network card and associate with public IP address and NSG
$nic = New-AzureRmNetworkInterface -Name myNic -ResourceGroupName $resourceGroup -Location $location `
  -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id

$image = get-azurermimage -ResourceGroupName $imagerg -ImageName $imagename

# Create a virtual machine configuration
$vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize Standard_D1 | `
Set-AzureRmVMOperatingSystem -Linux -ComputerName $vmName -Credential $cred | `
Set-AzureRmVMSourceImage -Id $image.Id | `
Add-AzureRmVMNetworkInterface -Id $nic.Id

# Create a virtual machine
New-AzureRmVM -ResourceGroupName $resourceGroup -Location $location -VM $vmConfig
```

您也可以使用入口網站，從受控映像建立 VM。 如需詳細資訊，請參閱 Azure 受控映像文章：[在 Azure 中建立一般化 VM 的受控映像](../../virtual-machines/windows/capture-image-resource.md)與[從受控映像建立 VM](../../virtual-machines/windows/create-vm-generalized-managed.md)。

## <a name="configuration"></a>組態

套用 1808 更新或更新版本後，您必須先執行下列設定，再使用受控磁碟：

- 如果訂用帳戶是 1808 版更新之前建立的，請遵循下列步驟來更新訂用帳戶。 否則，在此訂用帳戶中部署 VM 可能會失敗，並出現錯誤訊息「磁碟管理員發生內部錯誤。」
   1. 在租用戶入口網站中，移至 [訂用帳戶] 並尋找訂用帳戶。 按一下 [資源提供者]，按一下 [Microsoft.Compute]，然後按一下 [重新註冊]。
   2. 在相同的訂用帳戶底下，移至 [存取控制 (IAM)]，並確認 [Azure Stack - 受控磁碟] 已列出。
- 如果您使用多租用戶環境，請要求您的雲端操作員 (可以是您自己組織中的或來自服務提供者) 依照[此文章](../azure-stack-enable-multitenancy.md#registering-azure-stack-with-the-guest-directory)中的步驟重新設定每個來賓目錄。 否則，在與該來賓目錄相關聯的訂用帳戶中部署 VM 可能會失敗，並出現錯誤訊息「磁碟管理員中發生內部錯誤。」

## <a name="next-steps"></a>後續步驟

- [了解 Azure Stack 虛擬機器](azure-stack-compute-overview.md)
