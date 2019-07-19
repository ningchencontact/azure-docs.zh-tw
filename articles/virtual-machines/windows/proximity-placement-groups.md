---
title: 使用 Windows Vm 的鄰近放置群組 |Microsoft Docs
description: 瞭解如何在 Azure 中建立和使用 Windows 虛擬機器的鄰近放置群組。
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 07/01/2019
ms.author: cynthn
ms.openlocfilehash: 9e9b8dddf7f48fe672a37b0343d215e0735ed760
ms.sourcegitcommit: af58483a9c574a10edc546f2737939a93af87b73
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/17/2019
ms.locfileid: "68302509"
---
# <a name="preview-deploy-vms-to-proximity-placement-groups-using-powershell"></a>預覽：使用 PowerShell 將 Vm 部署至鄰近放置群組


若要盡可能接近 Vm, 達到最低可能的延遲, 您應該將它們部署在[鄰近放置群組](co-location.md#preview-proximity-placement-groups)內。

鄰近放置群組是一種邏輯群組, 用來確保 Azure 計算資源實際位於彼此接近的位置。 鄰近放置群組適用于需要低延遲的工作負載。

> [!IMPORTANT]
> 鄰近放置群組目前為公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。
>
> 在預覽期間, 這些區域無法使用鄰近放置群組:**日本東部**、**澳大利亞東部**和**印度中部**。


## <a name="create-a-proximity-placement-group"></a>建立鄰近放置群組
使用[AzProximityPlacementGroup](https://docs.microsoft.com/powershell/module/az.compute/new-azproximityplacementgroup) Cmdlet 建立鄰近放置群組。 

```azurepowershell-interactive
$resourceGroup = "myPPGResourceGroup"
$location = "East US"
$ppgName = "myPPG"
New-AzResourceGroup -Name $resourceGroup -Location $location
$ppg = New-AzProximityPlacementGroup `
   -Location $location `
   -Name $ppgName `
   -ResourceGroupName $resourceGroup `
   -ProximityPlacementGroupType Standard
```

## <a name="list-proximity-placement-groups"></a>列出鄰近放置群組

您可以使用[AzProximityPlacementGroup](/powershell/module/az.compute/get-azproximityplacementgroup) Cmdlet 來列出所有的鄰近放置群組。

```azurepowershell-interactive
Get-AzProximityPlacementGroup
```


## <a name="create-a-vm"></a>建立 VM

當您使用[update-azvm](https://docs.microsoft.com/powershell/module/az.compute/new-azvm)建立 vm 時, 請`-ProximityPlacementGroup $ppg.Id`使用在鄰近放置群組中建立 vm, 以參考鄰近放置群組識別碼。

```azurepowershell-interactive
$vmName = "myVM"

New-AzVm `
  -ResourceGroupName $resourceGroup `
  -Name $vmName `
  -Location $location `
  -OpenPorts 3389 `
  -ProximityPlacementGroup $ppg.Id
```

您可以使用[AzProximityPlacementGroup](/powershell/module/az.compute/get-azproximityplacementgroup)查看放置群組中的 VM。

```azurepowershell-interactive
Get-AzProximityPlacementGroup -ResourceId $ppg.Id |
    Format-Table -Property VirtualMachines -Wrap
```

## <a name="availability-sets"></a>可用性設定組 (Availability Sets)
您也可以在鄰近放置群組中建立可用性設定組。 使用與`-ProximityPlacementGroup` [new-azavailabilityset 組](/powershell/module/az.compute/new-azavailabilityset)指令程式相同的參數來建立可用性設定組, 而且在可用性設定組中建立的所有 vm 也會在相同的鄰近放置群組中建立。

## <a name="scale-sets"></a>擴展集

您也可以在鄰近放置群組中建立擴展集。 使用與`-ProximityPlacementGroup` [get-azvmss](https://docs.microsoft.com/powershell/module/az.compute/new-azvmss)相同的參數來建立擴展集, 而且所有實例將會在相同的鄰近放置群組中建立。

## <a name="next-steps"></a>後續步驟

您也可以使用[Azure CLI](../linux/proximity-placement-groups.md)來建立鄰近放置群組。
