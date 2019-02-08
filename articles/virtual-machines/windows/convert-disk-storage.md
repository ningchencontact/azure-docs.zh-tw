---
title: 將 Azure 受控磁碟儲存體從標準轉換至進階，反之亦然 | Microsoft Docs
description: 如何使用 Azure PowerShell 將 Azure 受控磁碟從標準轉換至進階，反之亦然。
services: virtual-machines-windows
documentationcenter: ''
author: ramankumarlive
manager: kavithag
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/04/2018
ms.author: ramankum
ms.subservice: disks
ms.openlocfilehash: 4c13708ad785a2291da3db61d739f604a2c3bb88
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/31/2019
ms.locfileid: "55475885"
---
# <a name="update-the-storage-type-of-a-managed-disk"></a>更新受控磁碟的儲存體類型

Azure 受控磁碟提供三個儲存體類型選項：[進階 SSD](../windows/premium-storage.md)、[標準 SSD](../windows/disks-standard-ssd.md) 和[標準 HDD](../windows/standard-storage.md)。 您可以根據您的效能需求，以最少的停機時間在儲存體類型之間切換受控磁碟。 不支援在非受控磁碟的儲存體類型之間切換；不過，您可以輕鬆地[將非受控磁碟轉換為受控磁碟](convert-unmanaged-to-managed-disks.md)。

本文說明如何使用 Azure PowerShell 將受控磁碟從標準轉換至進階，反之亦然。 如果您需要安裝或升級 PowerShell，請參閱[安裝和設定 Azure PowerShell](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps?view=azurermps-6.8.1)。

## <a name="prerequisites"></a>必要條件

* 轉換需要重新啟動虛擬機器 (VM)，因此您應在預先存在的維護期間排定磁碟儲存體移轉。 
* 如果您使用非受控磁碟，請先[將它轉換為受控磁碟](convert-unmanaged-to-managed-disks.md)，才可讓您在儲存體類型之間切換它。 
* 本文中的範例需要 Azure PowerShell 模組 6.0.0 版或更新版本。 執行 `Get-Module -ListAvailable AzureRM` 以尋找版本。 如果您需要升級，請參閱[安裝 Azure PowerShell 模組](/powershell/azure/azurerm/install-azurerm-ps)。 執行 [Connect-AzureRmAccount](https://docs.microsoft.com/powershell/module/azurerm.profile/connect-azurermaccount) 以建立與 Azure 的連線。


## <a name="convert-all-the-managed-disks-of-a-vm-from-standard-to-premium"></a>將 VM 的所有受控磁碟從標準轉換至進階

在下列範例中，我們會示範如何將 VM 的所有磁碟從標準儲存體切換成進階儲存體。 若要使用進階受控磁碟，您的 VM 必須使用可支援進階儲存體的 [VM 大小](sizes.md)。 此範例也會切換成可支援進階儲存體的大小：

```azurepowershell-interactive
# Name of the resource group that contains the VM
$rgName = 'yourResourceGroup'

# Name of the your virtual machine
$vmName = 'yourVM'

# Choose between StandardLRS and PremiumLRS based on your scenario
$storageType = 'Premium_LRS'

# Premium capable size
# Required only if converting storage from standard to premium
$size = 'Standard_DS2_v2'

# Stop and deallocate the VM before changing the size
Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName -Force

$vm = Get-AzureRmVM -Name $vmName -resourceGroupName $rgName

# Change the VM size to a size that supports premium storage
# Skip this step if converting storage from premium to standard
$vm.HardwareProfile.VmSize = $size
Update-AzureRmVM -VM $vm -ResourceGroupName $rgName

# Get all disks in the resource group of the VM
$vmDisks = Get-AzureRmDisk -ResourceGroupName $rgName 

# For disks that belong to the selected VM, convert to premium storage
foreach ($disk in $vmDisks)
{
    if ($disk.ManagedBy -eq $vm.Id)
    {
        $diskUpdateConfig = New-AzureRmDiskUpdateConfig –AccountType $storageType
        Update-AzureRmDisk -DiskUpdate $diskUpdateConfig -ResourceGroupName $rgName `
        -DiskName $disk.Name
    }
}

Start-AzureRmVM -ResourceGroupName $rgName -Name $vmName
```

## <a name="convert-a-managed-disk-from-standard-to-premium"></a>將所有受控磁碟從標準轉換至進階

對於您的開發/測試工作負載，您可能希望混合標準和進階磁碟，以降低成本。 若要這麼做，請升級至進階儲存體，但僅限需要更佳效能的磁碟。 下列範例們會示範如何將 VM 的單一磁碟從標準儲存體切換成進階儲存體，反之亦然。 若要使用進階受控磁碟，您的 VM 必須使用可支援進階儲存體的 [VM 大小](sizes.md)。 此範例也會示範如何切換成可支援進階儲存體的大小：

```azurepowershell-interactive

$diskName = 'yourDiskName'
# resource group that contains the managed disk
$rgName = 'yourResourceGroupName'
# Choose between StandardLRS and PremiumLRS based on your scenario
$storageType = 'Premium_LRS'
# Premium capable size 
$size = 'Standard_DS2_v2'

$disk = Get-AzureRmDisk -DiskName $diskName -ResourceGroupName $rgName

# Get parent VM resource
$vmResource = Get-AzureRmResource -ResourceId $disk.ManagedBy

# Stop and deallocate the VM before changing the storage type
Stop-AzureRmVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name -Force

$vm = Get-AzureRmVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name 

# Change the VM size to a size that supports premium storage
# Skip this step if converting storage from premium to standard
$vm.HardwareProfile.VmSize = $size
Update-AzureRmVM -VM $vm -ResourceGroupName $rgName

# Update the storage type
$diskUpdateConfig = New-AzureRmDiskUpdateConfig -AccountType $storageType -DiskSizeGB $disk.DiskSizeGB
Update-AzureRmDisk -DiskUpdate $diskUpdateConfig -ResourceGroupName $rgName `
-DiskName $disk.Name

Start-AzureRmVM -ResourceGroupName $vm.ResourceGroupName -Name $vm.Name
```

## <a name="convert-a-managed-disk-from-standard-hdd-to-standard-ssd"></a>將受控磁碟從標準 HDD 轉換成標準 SSD

下列範例會示範如何將 VM 的單一磁碟從標準 HDD 切換成標準 SSD，反之亦然：

```azurepowershell-interactive

$diskName = 'yourDiskName'
# resource group that contains the managed disk
$rgName = 'yourResourceGroupName'
# Choose between Standard_LRS and StandardSSD_LRS based on your scenario
$storageType = 'StandardSSD_LRS'

$disk = Get-AzureRmDisk -DiskName $diskName -ResourceGroupName $rgName

# Get parent VM resource
$vmResource = Get-AzureRmResource -ResourceId $disk.ManagedBy

# Stop and deallocate the VM before changing the storage type
Stop-AzureRmVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name -Force

$vm = Get-AzureRmVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name 

# Update the storage type
$diskUpdateConfig = New-AzureRmDiskUpdateConfig -AccountType $storageType -DiskSizeGB $disk.DiskSizeGB
Update-AzureRmDisk -DiskUpdate $diskUpdateConfig -ResourceGroupName $rgName `
-DiskName $disk.Name

Start-AzureRmVM -ResourceGroupName $vm.ResourceGroupName -Name $vm.Name
```

## <a name="next-steps"></a>後續步驟

使用[快照集](snapshot-copy-managed-disk.md)來製作 VM 的唯讀複本。

