---
title: Azure Site Recovery 排除磁碟期間使用 Azure PowerShell 的 Azure 虛擬機器的複寫 |Microsoft Docs
description: 了解如何使用 Azure PowerShell，在 Azure Site Recovery 期間排除的 Azure 虛擬機器的磁碟。
services: site-recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 02/18/2019
ms.author: asgang
ms.openlocfilehash: 54a32d7f7aa4bcab73f5828da3e7eba9d25276be
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/17/2019
ms.locfileid: "59678270"
---
# <a name="exclude-disks-from-powershell-replication-of-azure-vms"></a>排除磁碟不要從 PowerShell 的 Azure Vm 的複寫

本文說明如何排除磁碟，當您將 Azure Vm 複寫。 您可能會排除磁碟，以最佳化已使用的複寫頻寬或使用這些磁碟的目標端資源。 這項功能目前僅透過 Azure PowerShell。


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>必要條件

開始之前：

- 請確定您已了解[災害復原架構和元件](azure-to-azure-architecture.md)。
- 檢閱所有元件的[支援需求](azure-to-azure-support-matrix.md)。
- 請確定您有 AzureRm PowerShell"Az"模組。 若要安裝或更新 PowerShell，請參閱[安裝 Azure PowerShell 模組](https://docs.microsoft.com/powershell/azure/install-az-ps)。
- 請確定您已建立復原服務保存庫，並受保護的虛擬機器至少一次。 如果您尚未完成這些項目，請依照下列程序，在[設定為使用 Azure PowerShell 的 Azure 虛擬機器的災害復原](azure-to-azure-powershell.md)。

## <a name="why-exclude-disks-from-replication"></a>為什麼要從複寫排除磁碟
您可能需要從複寫排除磁碟，因為：

- 您的虛擬機器已達[Azure Site Recovery 限制來複寫資料變更率](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix)。

- 排除的磁碟變換的資料並不重要或不需要複寫。

- 您想要儲存不複寫資料的儲存體和網路資源。

## <a name="how-to-exclude-disks-from-replication"></a>如何排除磁碟不要複寫

在本例中，我們擁有一個作業系統的虛擬機器將和複寫到美國西部 2 區域的美國東部區域中的三個資料磁碟。 虛擬機器的名稱是*AzureDemoVM*。 我們會排除磁碟 1，並保留磁碟 2 和 3。

## <a name="get-details-of-the-virtual-machines-to-replicate"></a>取得要複寫的虛擬機器的詳細資料

```azurepowershell
# Get details of the virtual machine
$VM = Get-AzVM -ResourceGroupName "A2AdemoRG" -Name "AzureDemoVM"

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

取得虛擬機器的磁碟詳細資料。 當您啟動 VM 的複寫時，將更新版本使用這項資訊。

```azurepowershell
$OSDiskVhdURI = $VM.StorageProfile.OsDisk.Vhd
$DataDisk1VhdURI = $VM.StorageProfile.DataDisks[0].Vhd
```

## <a name="replicate-an-azure-virtual-machine"></a>複寫 Azure 虛擬機器

下列範例中，我們假設您已經有快取儲存體帳戶、 複寫原則和對應。 如果您沒有這些項目，請依照下列程序，在[設定為使用 Azure PowerShell 的 Azure 虛擬機器的災害復原](azure-to-azure-powershell.md)。

複寫 Azure 虛擬機器*受控磁碟*。

```azurepowershell

#Get the resource group that the virtual machine must be created in when failed over.
$RecoveryRG = Get-AzResourceGroup -Name "a2ademorecoveryrg" -Location "West US 2"

#Specify replication properties for each disk of the VM that is to be replicated (create disk replication configuration).

#OsDisk
$OSdiskId =  $vm.StorageProfile.OsDisk.ManagedDisk.Id
$RecoveryOSDiskAccountType = $vm.StorageProfile.OsDisk.ManagedDisk.StorageAccountType
$RecoveryReplicaDiskAccountType =  $vm.StorageProfile.OsDisk.ManagedDisk.StorageAccountType

$OSDiskReplicationConfig = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $EastUSCacheStorageAccount.Id `
         -DiskId $OSdiskId -RecoveryResourceGroupId  $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType  $RecoveryReplicaDiskAccountType `
         -RecoveryTargetDiskAccountType $RecoveryOSDiskAccountType

# Data Disk 1 i.e StorageProfile.DataDisks[0] is excluded, so we will provide it during the time of replication. 

# Data disk 2
$datadiskId2  = $vm.StorageProfile.DataDisks[1].ManagedDisk.id
$RecoveryReplicaDiskAccountType =  $vm.StorageProfile.DataDisks[1]. StorageAccountType
$RecoveryTargetDiskAccountType = $vm.StorageProfile.DataDisks[1]. StorageAccountType

$DataDisk2ReplicationConfig  = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $CacheStorageAccount.Id `
         -DiskId $datadiskId2 -RecoveryResourceGroupId  $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType  $RecoveryReplicaDiskAccountType `
         -RecoveryTargetDiskAccountType $RecoveryTargetDiskAccountType

# Data Disk 3

$datadiskId3  = $vm.StorageProfile.DataDisks[2].ManagedDisk.id
$RecoveryReplicaDiskAccountType =  $vm.StorageProfile.DataDisks[2]. StorageAccountType
$RecoveryTargetDiskAccountType = $vm.StorageProfile.DataDisks[2]. StorageAccountType

$DataDisk3ReplicationConfig  = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $CacheStorageAccount.Id `
         -DiskId $datadiskId3 -RecoveryResourceGroupId  $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType  $RecoveryReplicaDiskAccountType `
         -RecoveryTargetDiskAccountType $RecoveryTargetDiskAccountType

#Create a list of disk replication configuration objects for the disks of the virtual machine that are to be replicated.
$diskconfigs = @()
$diskconfigs += $OSDiskReplicationConfig, $DataDisk2ReplicationConfig, $DataDisk3ReplicationConfig


#Start replication by creating a replication protected item. Using a GUID for the name of the replication protected item to ensure uniqueness of name.
$TempASRJob = New-ASRReplicationProtectedItem -AzureToAzure -AzureVmId $VM.Id -Name (New-Guid).Guid -ProtectionContainerMapping $EusToWusPCMapping -AzureToAzureDiskReplicationConfiguration $diskconfigs -RecoveryResourceGroupId $RecoveryRG.ResourceId
```

啟動複寫作業成功時，VM 資料會複寫至復原區域中。

您可以前往 Azure 入口網站，並查看複寫的 Vm，在 「 複寫的項目 」。

複寫程序一開始會植入復原區域中的虛擬機器的複寫磁碟的複本。 這個階段會呼叫 「 初始複寫 」 階段。

初始複寫完成之後，複寫會移至差異同步處理階段。 此時，虛擬機器已受到保護。 選取受保護的虛擬機器，請參閱是否任何磁碟都會被排除。

## <a name="next-steps"></a>後續步驟

深入了解[執行測試容錯移轉](site-recovery-test-failover-to-azure.md)。
