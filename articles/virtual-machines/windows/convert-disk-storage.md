---
title: 轉換 Azure 受控磁碟儲存體從標準，為進階或標準的進階 |Microsoft Docs
description: 如何將轉換 Azure 受控磁碟從標準進階或標準的 Premium 藉由使用 Azure PowerShell。
services: virtual-machines-windows
documentationcenter: ''
author: roygara
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 02/22/2019
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 5687e6d0094083a9ee58455cc72b0b2e4da32d65
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/30/2019
ms.locfileid: "66417141"
---
# <a name="update-the-storage-type-of-a-managed-disk"></a>更新受控磁碟的儲存體類型

有四個磁碟類型的 Azure 受控磁碟：Azure 的強力 Ssd （預覽）、 進階 SSD、 標準的 SSD 和 HDD 標準。 您可以切換的三種 GA 磁碟類型 (進階 SSD、 標準的 SSD 和 HDD 標準) 根據效能需求。 您尚不能夠切換或強力的 SSD，您必須部署新的資源。

這項功能不支援非受控磁碟的功能。 但您可以輕鬆地[將非受控的磁碟轉換為受控磁碟](convert-unmanaged-to-managed-disks.md)能夠切換磁碟類型。

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>必要條件

* 因為轉換需要重新啟動虛擬機器 (VM)，您應該排程在預先存在的維護期間的磁碟儲存體移轉。
* 如果您的磁碟不受管理，第一次[將它轉換為受控磁碟](convert-unmanaged-to-managed-disks.md)讓您可以切換儲存體選項。

## <a name="switch-all-managed-disks-of-a-vm-between-premium-and-standard"></a>切換所有受控的磁碟的進階和標準之間的 VM

此範例示範如何將轉換的所有 VM 的磁碟從標準到進階儲存體或從 Premium 升級到標準儲存體。 若要使用進階受控磁碟，VM 必須使用可支援進階儲存體的 [VM 大小](sizes.md)。 此範例也會切換成可支援進階儲存體的大小：

```azurepowershell-interactive
# Name of the resource group that contains the VM
$rgName = 'yourResourceGroup'

# Name of the your virtual machine
$vmName = 'yourVM'

# Choose between Standard_LRS and Premium_LRS based on your scenario
$storageType = 'Premium_LRS'

# Premium capable size
# Required only if converting storage from Standard to Premium
$size = 'Standard_DS2_v2'

# Stop and deallocate the VM before changing the size
Stop-AzVM -ResourceGroupName $rgName -Name $vmName -Force

$vm = Get-AzVM -Name $vmName -resourceGroupName $rgName

# Change the VM size to a size that supports Premium storage
# Skip this step if converting storage from Premium to Standard
$vm.HardwareProfile.VmSize = $size
Update-AzVM -VM $vm -ResourceGroupName $rgName

# Get all disks in the resource group of the VM
$vmDisks = Get-AzDisk -ResourceGroupName $rgName 

# For disks that belong to the selected VM, convert to Premium storage
foreach ($disk in $vmDisks)
{
    if ($disk.ManagedBy -eq $vm.Id)
    {
        $diskUpdateConfig = New-AzDiskUpdateConfig –AccountType $storageType
        Update-AzDisk -DiskUpdate $diskUpdateConfig -ResourceGroupName $rgName `
        -DiskName $disk.Name
    }
}

Start-AzVM -ResourceGroupName $rgName -Name $vmName
```

## <a name="switch-individual-managed-disks-between-standard-and-premium"></a>切換 Standard 和 Premium 之間個別的受控的磁碟

您的開發/測試工作負載，您可能想混合標準和進階磁碟，以降低成本。 您可以選擇升級那些需要更佳的效能的磁碟。 此範例示範如何將轉換的單一 VM 磁碟從標準到進階儲存體或從 Premium 升級到標準儲存體。 若要使用進階受控磁碟，VM 必須使用可支援進階儲存體的 [VM 大小](sizes.md)。 此範例也示範如何切換到支援進階儲存體的大小：

```azurepowershell-interactive

$diskName = 'yourDiskName'
# resource group that contains the managed disk
$rgName = 'yourResourceGroupName'
# Choose between Standard_LRS and Premium_LRS based on your scenario
$storageType = 'Premium_LRS'
# Premium capable size 
$size = 'Standard_DS2_v2'

$disk = Get-AzDisk -DiskName $diskName -ResourceGroupName $rgName

# Get parent VM resource
$vmResource = Get-AzResource -ResourceId $disk.ManagedBy

# Stop and deallocate the VM before changing the storage type
Stop-AzVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name -Force

$vm = Get-AzVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name 

# Change the VM size to a size that supports Premium storage
# Skip this step if converting storage from Premium to Standard
$vm.HardwareProfile.VmSize = $size
Update-AzVM -VM $vm -ResourceGroupName $rgName

# Update the storage type
$diskUpdateConfig = New-AzDiskUpdateConfig -AccountType $storageType -DiskSizeGB $disk.DiskSizeGB
Update-AzDisk -DiskUpdate $diskUpdateConfig -ResourceGroupName $rgName `
-DiskName $disk.Name

Start-AzVM -ResourceGroupName $vm.ResourceGroupName -Name $vm.Name
```

## <a name="convert-managed-disks-from-standard-to-premium-in-the-azure-portal"></a>將受控的磁碟從標準轉換至進階，Azure 入口網站

請遵循下列步驟：

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 從清單中選取 VM**虛擬機器**入口網站中。
3. 如果未停止 VM，請選取**停止**頂端的 [VM**概觀**] 窗格中，並停止 VM 的等候。
3. 在 [vm] 窗格中選取**磁碟**從功能表。
4. 選取您想要轉換的磁碟。
5. 選取 **組態**從功能表。
6. 變更**帳戶類型**從**標準的 HDD**來**進階 SSD**。
7. 按一下 **儲存**，並關閉 磁碟 窗格。

磁碟類型轉換是在瞬間完成。 您可以在轉換之後，重新啟動您的 VM。

## <a name="switch-managed-disks-between-standard-hdd-and-standard-ssd"></a>切換之間標準 HDD 和 SSD 標準受控的磁碟 

此範例示範如何轉換單一 VM 磁碟從標準的 HDD 變成標準的 SSD 或標準 SSD 至標準的 HDD:

```azurepowershell-interactive

$diskName = 'yourDiskName'
# resource group that contains the managed disk
$rgName = 'yourResourceGroupName'
# Choose between Standard_LRS and StandardSSD_LRS based on your scenario
$storageType = 'StandardSSD_LRS'

$disk = Get-AzDisk -DiskName $diskName -ResourceGroupName $rgName

# Get parent VM resource
$vmResource = Get-AzResource -ResourceId $disk.ManagedBy

# Stop and deallocate the VM before changing the storage type
Stop-AzVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name -Force

$vm = Get-AzVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name 

# Update the storage type
$diskUpdateConfig = New-AzDiskUpdateConfig -AccountType $storageType -DiskSizeGB $disk.DiskSizeGB
Update-AzDisk -DiskUpdate $diskUpdateConfig -ResourceGroupName $rgName `
-DiskName $disk.Name

Start-AzVM -ResourceGroupName $vm.ResourceGroupName -Name $vm.Name
```

## <a name="next-steps"></a>後續步驟

使用[快照集](snapshot-copy-managed-disk.md)來製作 VM 的唯讀複本。
