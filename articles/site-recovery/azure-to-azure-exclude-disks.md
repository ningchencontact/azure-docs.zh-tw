---
title: Azure Site Recovery - 使用 Azure PowerShell 在 Azure 虛擬機器複寫期間排除磁碟 | Microsoft Docs
description: 了解如何使用 Azure PowerShell 搭配 Azure Site Recovery 來排除 Azure 虛擬機器的磁碟。
services: site-recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 02/18/2019
ms.author: asgang
ms.openlocfilehash: 978566eb9e0073c60046eca024e09ba63c642180
ms.sourcegitcommit: 75fef8147209a1dcdc7573c4a6a90f0151a12e17
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/20/2019
ms.locfileid: "56458225"
---
# <a name="exclude-disks-from-replication-of-azure-vms-to-azure-using-azure-powershell"></a>使用 Azure PowerShell 將 Azure VM 複寫的磁碟排除至 Azure

本文說明如何在複寫 Azure VM 時排除磁碟。 這種排除可以最佳化已使用的複寫頻寬，或最佳化此類磁碟使用的目標端資源。 這項功能目前只透過 Azure PowerShell 公開。

## <a name="prerequisites"></a>必要條件

開始之前：

- 請確定您了解[情節架構和元件](azure-to-azure-architecture.md)。
- 檢閱所有元件的[支援需求](azure-to-azure-support-matrix.md)。
- 您有 AzureRm PowerShell 模組的 5.7.0 版或更高版本。 如果您需要安裝或升級 Azure PowerShell，請按照此[安裝和設定 Azure PowerShell 指南](/powershell/azureps-cmdlets-docs)的說明。
- 您已建立復原服務保存庫，並至少完成保護虛擬機器一次。 如果沒有，請按照[此處](azure-to-azure-powershell.md)提到的文件 

## <a name="why-exclude-disks-from-replication"></a>為什麼要排除磁碟不要複寫？
排除磁碟不要複寫往往是因為︰

- 您的虛擬機器已達到[複寫資料變更率的 Azure Site Recovery 限制](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix#azure-site-recovery-limits-to-replicate-data-change-rates)

- 在排除的磁碟上變換的資料不重要或不需要複寫。

- 您想要儲存儲存體和網路資源時可以不要複寫此變換。


## <a name="how-to-exclude-disks-from-replication"></a>如何排除磁碟不要複寫？

在本文範例中，在「美國東部」區域具有 1 個 OS 和 3 個資料磁碟的虛擬機器會複寫到「美國西部 2」區域。 此範例所使用的虛擬機器名稱是 AzureDemoVM。 我們會排除磁碟 1，並保留磁碟 2 和 3。

## <a name="get-details-of-the-virtual-machines-to-be-replicated"></a>取得要複寫的虛擬機器詳細資料

```azurepowershell
# Get details of the virtual machine
$VM = Get-AzureRmVM -ResourceGroupName "A2AdemoRG" -Name "AzureDemoVM"

Write-Output $VM     
```

```
ResourceGroupName  : A2AdemoRG
Id                 : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/A2AdemoRG/providers/Microsoft.Compute/virtualMachines/AzureDemoVM
VmId               : 1b864902-c7ea-499a-ad0f-65da2930b81b
Name               : AzureDemoVM
Type               : Microsoft.Compute/virtualMachines
Location           : eastus
Tags               : {}
DiagnosticsProfile : {BootDiagnostics}
HardwareProfile    : {VmSize}
NetworkProfile     : {NetworkInterfaces}
OSProfile          : {ComputerName, AdminUsername, WindowsConfiguration, Secrets}
ProvisioningState  : Succeeded
StorageProfile     : {ImageReference, OsDisk, DataDisks}
```


取得虛擬機器磁碟的磁碟詳細資料。 稍後在開始複寫虛擬機器時，會用到磁碟詳細資料。

```azurepowershell
$OSDiskVhdURI = $VM.StorageProfile.OsDisk.Vhd
$DataDisk1VhdURI = $VM.StorageProfile.DataDisks[0].Vhd
```

## <a name="replicate-azure-virtual-machine"></a>複寫 Azure 虛擬機器

在下列範例中，我們假設您已擁有快取儲存體帳戶、複寫原則和對應。 如果沒有，請按照[此處](azure-to-azure-powershell.md)提到的文件 


以**受控磁碟**複寫 Azure 虛擬機器。

```azurepowershell

#Get the resource group that the virtual machine must be created in when failed over.
$RecoveryRG = Get-AzureRmResourceGroup -Name "a2ademorecoveryrg" -Location "West US 2"

#Specify replication properties for each disk of the VM that is to be replicated (create disk replication configuration)

#OsDisk
$OSdiskId =  $vm.StorageProfile.OsDisk.ManagedDisk.Id
$RecoveryOSDiskAccountType = $vm.StorageProfile.OsDisk.ManagedDisk.StorageAccountType
$RecoveryReplicaDiskAccountType =  $vm.StorageProfile.OsDisk.ManagedDisk.StorageAccountType

$OSDiskReplicationConfig = New-AzureRmRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $EastUSCacheStorageAccount.Id `
         -DiskId $OSdiskId -RecoveryResourceGroupId  $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType  $RecoveryReplicaDiskAccountType `
         -RecoveryTargetDiskAccountType $RecoveryOSDiskAccountType

# Data Disk 1 i.e StorageProfile.DataDisks[0] is excluded so we will provide it during the time of replication 

# Data disk 2
$datadiskId2  = $vm.StorageProfile.DataDisks[1].ManagedDisk.id
$RecoveryReplicaDiskAccountType =  $vm.StorageProfile.DataDisks[1]. StorageAccountType
$RecoveryTargetDiskAccountType = $vm.StorageProfile.DataDisks[1]. StorageAccountType

$DataDisk2ReplicationConfig  = New-AzureRmRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $CacheStorageAccount.Id `
         -DiskId $datadiskId2 -RecoveryResourceGroupId  $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType  $RecoveryReplicaDiskAccountType `
         -RecoveryTargetDiskAccountType $RecoveryTargetDiskAccountType

# Data Disk 3

$datadiskId3  = $vm.StorageProfile.DataDisks[2].ManagedDisk.id
$RecoveryReplicaDiskAccountType =  $vm.StorageProfile.DataDisks[2]. StorageAccountType
$RecoveryTargetDiskAccountType = $vm.StorageProfile.DataDisks[2]. StorageAccountType

$DataDisk3ReplicationConfig  = New-AzureRmRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $CacheStorageAccount.Id `
         -DiskId $datadiskId3 -RecoveryResourceGroupId  $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType  $RecoveryReplicaDiskAccountType `
         -RecoveryTargetDiskAccountType $RecoveryTargetDiskAccountType

#Create a list of disk replication configuration objects for the disks of the virtual machine that are to be replicated.
$diskconfigs = @()
$diskconfigs += $OSDiskReplicationConfig, $DataDisk2ReplicationConfig, $DataDisk3ReplicationConfig


#Start replication by creating replication protected item. Using a GUID for the name of the replication protected item to ensure uniqueness of name.
$TempASRJob = New-ASRReplicationProtectedItem -AzureToAzure -AzureVmId $VM.Id -Name (New-Guid).Guid -ProtectionContainerMapping $EusToWusPCMapping -AzureToAzureDiskReplicationConfiguration $diskconfigs -RecoveryResourceGroupId $RecoveryRG.ResourceId
```

複寫作業啟動成功後，系統就會將虛擬機器資料複寫至復原區域。

您可以前往 Azure 入口網站，在已複寫項目底下可看到將要複寫的虛擬機器。
複寫程序一開始會先在復原區域中植入虛擬機器複寫磁碟的複本。 這個階段稱為初始複寫階段。

初始複寫完成後，複寫會進入差異同步處理階段。 此時，虛擬機器已受到保護。 按一下受保護的虛擬機器 > 磁碟，即可查看是否有排除磁碟。

## <a name="next-steps"></a>後續步驟

[深入了解](site-recovery-test-failover-to-azure.md)執行測試容錯移轉。