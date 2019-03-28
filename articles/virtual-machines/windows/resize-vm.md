---
title: 使用 PowerShell 來調整 Azure 中 Windows VM 的大小 | Microsoft Docs
description: 使用 Azure Powershell 可調整以 Resource Manager 部署模型建立的 Windows 虛擬機器大小。
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 057ff274-6dad-415e-891c-58f8eea9ed78
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 05/30/2018
ms.author: cynthn
ms.openlocfilehash: f54ff738199d433308a8eaba6a643861c57b4abb
ms.sourcegitcommit: cf971fe82e9ee70db9209bb196ddf36614d39d10
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2019
ms.locfileid: "58540681"
---
# <a name="resize-a-windows-vm"></a>調整 Windows VM 大小

本文說明如何使用 Azure PowerShell 將 VM 變更為不同的 [VM 大小](sizes.md)。

建立虛擬機器 (VM) 後，您可以透過變更 VM 的大小來放大或縮小 VM。 在某些情况下，必须先解除分配 VM。 如果新的大小不適用於目前裝載 VM 的硬體叢集上，就會發生此情況。

如果您的 VM 使用進階儲存體，請確實選擇 **s** 版本的大小，以取得進階儲存體支援。 例如，請選擇 Standard_E4**s**_v3，而不是 Standard_E4_v3。

[!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)]

## <a name="resize-a-windows-vm-not-in-an-availability-set"></a>調整不在可用性設定組中 Windows VM 的大小

設定一些變數。 使用您自己的資訊取代這些值。

```powershell
$resourceGroup = "myResourceGroup"
$vmName = "myVM"
```

列出裝載 VM 的硬體叢集上適用的 VM 大小。 
   
```powershell
Get-AzVMSize -ResourceGroupName $resourceGroup -VMName $vmName 
```

如果您要的大小有列出，請執行以下命令以調整 VM 大小。 如果所需的大小未列出，請移至步驟 3。
   
```powershell
$vm = Get-AzVM -ResourceGroupName $resourceGroup -VMName $vmName
$vm.HardwareProfile.VmSize = "<newVMsize>"
Update-AzVM -VM $vm -ResourceGroupName $resourceGroup
```

如果您要的大小未列出，請執行以下命令以解除配置 VM、調整其大小，然後重新啟動 VM。 取代 **\<newVMsize >** 與您想要的大小。
   
```powershell
Stop-AzVM -ResourceGroupName $resourceGroup -Name $vmName -Force
$vm = Get-AzVM -ResourceGroupName $resourceGroup -VMName $vmName
$vm.HardwareProfile.VmSize = "<newVMSize>"
Update-AzVM -VM $vm -ResourceGroupName $resourceGroup
Start-AzVM -ResourceGroupName $resourceGroup -Name $vmName
```

> [!WARNING]
> 解除配置 VM 會將指派給 VM 的任何動態 IP 位址釋出。 不會影響作業系統和資料磁碟。 
> 
> 

## <a name="resize-a-windows-vm-in-an-availability-set"></a>調整在可用性設定組中 Windows VM 的大小

如果可用性設定組中的 VM 新大小，不適用於目前裝載 VM 的硬體叢集，則必須解除配置可用性設定組中所有的 VM，才能調整 VM 大小。 在已調整某個 VM 的大小後，您也可能需要更新可用性設定組中其他 VM 的大小。 若要調整可用性設定組中 VM 的大小，請執行下列步驟。

```powershell
$resourceGroup = "myResourceGroup"
$vmName = "myVM"
```

列出裝載 VM 的硬體叢集上適用的 VM 大小。 
   
```powershell
Get-AzVMSize -ResourceGroupName $resourceGroup -VMName $vmName 
```

如果所需的大小有列出，請執行以下命令調整 VM 大小。 如果未列出，請移至下一節。
   
```powershell
$vm = Get-AzVM -ResourceGroupName $resourceGroup -VMName $vmName 
$vm.HardwareProfile.VmSize = "<newVmSize>"
Update-AzVM -VM $vm -ResourceGroupName $resourceGroup
```
    
如果您要的大小未列出，請繼續進行下列步驟，以解除配置可用性設定組中所有的 VM、調整 VM 大小，然後重新啟動 VM。

停止可用性設定組中的所有 VM。
   
```powershell
$as = Get-AzAvailabilitySet -ResourceGroupName $resourceGroup
$vmIds = $as.VirtualMachinesReferences
foreach ($vmId in $vmIDs){
    $string = $vmID.Id.Split("/")
    $vmName = $string[8]
    Stop-AzVM -ResourceGroupName $resourceGroup -Name $vmName -Force
    } 
```

將可用性設定組中的 VM 調整大小後重新啟動。
   
```powershell
$newSize = "<newVmSize>"
$as = Get-AzAvailabilitySet -ResourceGroupName $resourceGroup
$vmIds = $as.VirtualMachinesReferences
  foreach ($vmId in $vmIDs){
    $string = $vmID.Id.Split("/")
    $vmName = $string[8]
    $vm = Get-AzVM -ResourceGroupName $resourceGroup -Name $vmName
    $vm.HardwareProfile.VmSize = $newSize
    Update-AzVM -ResourceGroupName $resourceGroup -VM $vm
    Start-AzVM -ResourceGroupName $resourceGroup -Name $vmName
    }
```

## <a name="next-steps"></a>後續步驟

如需提高延展性，可執行多個 VM 執行個體並相應放大。如需詳細資訊，請參閱[在虛擬機器擴展集中自動調整機器](../../virtual-machine-scale-sets/virtual-machine-scale-sets-windows-autoscale.md)。

