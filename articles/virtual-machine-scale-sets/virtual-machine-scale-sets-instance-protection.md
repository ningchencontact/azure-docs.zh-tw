---
title: 執行個體保護的 Azure 虛擬機器擴展集執行個體 |Microsoft Docs
description: 了解如何保護 Azure 虛擬機器擴展集執行個體從相應縮小和擴展集的作業。
services: virtual-machine-scale-sets
documentationcenter: ''
author: mayanknayar
manager: drewm
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/22/2019
ms.author: manayar
ms.openlocfilehash: 6c271c2c9feb1520951b2a8e301da4878970d60a
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/28/2019
ms.locfileid: "66259418"
---
# <a name="instance-protection-for-azure-virtual-machine-scale-set-instances-preview"></a>執行個體保護的 Azure 虛擬機器擴展集執行個體 （預覽）
Azure 虛擬機器擴展集可讓您的工作負載，透過較佳的彈性[自動調整規模](virtual-machine-scale-sets-autoscale-overview.md)，因此當您的基礎結構需要相應放大時，您可以設定和時它標尺。 擴展集也可讓您集中管理、 設定及更新透過不同的 Vm 大量[升級原則](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model)設定。 您可以設定擴展集模型更新和新的組態會自動套用至每個擴展集執行個體如果您已經設定為自動或正在復原的升級原則。

因為您的應用程式處理流量，可以有您想要以不同方式處理，小數位數的其餘部分的特定執行個體集合執行個體。 比方說，在擴展集中的特定執行個體無法執行長時間執行的作業，並不想要調整入作業完成之前這些執行個體。 您可能也有專用的擴展集來執行其他或不同的工作，比其他成員的擴展集內的少數執行個體。 您需要這些不是要修改與擴展集內的其他執行個體的 「 特殊 」 Vm。 執行個體保護提供額外的控制項，可讓這些和其他案例，您的應用程式。

本文說明如何套用，並搭配擴展集執行個體使用不同的執行個體保護功能。

> [!NOTE]
>執行個體保護目前處於公開預覽狀態。 沒有選擇加入程序才能使用如下所述的公開預覽功能。 執行個體保護預覽僅適用於 api 2019-03-01 版和更新版本。

## <a name="types-of-instance-protection"></a>類型的執行個體保護
擴展集提供兩種類型的執行個體保護功能：

-   **保護從相應縮小**
    - 透過啟用**protectFromScaleIn**標尺上的屬性集的執行個體
    - 保護執行個體從起始的自動調整相應縮小
    - 使用者起始的執行個體作業 （包括執行個體刪除）**未遭到封鎖**
    - 擴展集上所起始的作業 （升級、 重新安裝映像、 解除配置等） 是**未遭到封鎖**

-   **保護從擴展集動作**
    - 透過啟用**protectFromScaleSetActions**標尺上的屬性集的執行個體
    - 保護執行個體從起始的自動調整相應縮小
    - 保護執行個體從擴展集上所起始的作業 （例如升級，請重新安裝映像、 解除配置，等等）。
    - 使用者起始的執行個體作業 （包括執行個體刪除）**未遭到封鎖**
    - 完整的擴展集的刪除**未遭到封鎖**

## <a name="protect-from-scale-in"></a>保護從相應縮小
執行個體保護可以套用至擴展集執行個體之後建立的執行個體。 保護是套用，而且只有在修改[執行個體模型](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-vm-model-view)而不是在[擴展集模型](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-model)。

有多種方式來套用相應縮小保護在您的擴展集執行個體在下列範例中所述。

### <a name="rest-api"></a>REST API

下列範例會套用到擴展集中的執行個體的相應縮小保護。

```
PUT on `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/virtualMachines/{instance-id}?api-version=2019-03-01`
```

```json
{
  "properties": {
    "protectionPolicy": {
      "protectFromScaleIn": true
    }
  }        
}

```

> [!NOTE]
>執行個體保護僅適用於 api 2019-03-01 版和更新版本

### <a name="azure-powershell"></a>Azure PowerShell

使用[更新 AzVmssVM](/powershell/module/az.compute/update-azvmssvm) cmdlet，將相應縮小保護套用至您的擴展集執行個體。

下列範例適用於相應縮小保護中擁有執行個體識別碼 0 的擴展集的執行個體。

```azurepowershell-interactive
Update-AzVmssVM `
  -ResourceGroupName "myResourceGroup" `
  -VMScaleSetName "myVMScaleSet" `
  -InstanceId 0 `
  -ProtectFromScaleIn $true
```

### <a name="azure-cli-20"></a>Azure CLI 2.0

使用[az vmss 更新](/cli/azure/vmss#az-vmss-update)套用相應縮小保護您的擴展集執行個體。

下列範例適用於相應縮小保護中擁有執行個體識別碼 0 的擴展集的執行個體。

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --instance-id 0 \
  --protect-from-scale-in true
```

## <a name="protect-from-scale-set-actions"></a>保護從擴展集動作
執行個體保護可以套用至擴展集執行個體之後建立的執行個體。 保護是套用，而且只有在修改[執行個體模型](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-vm-model-view)而不是在[擴展集模型](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-model)。

保護執行個體從擴展集動作也會保護執行個體從起始的自動調整相應縮小。

有多種方式來套用調整動作上設定保護您的擴展集執行個體在下列範例中所述。

### <a name="rest-api"></a>REST API

下列範例會套用到擴展集中的執行個體從擴展集動作的保護。

```
PUT on `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vMScaleSetName}/virtualMachines/{instance-id}?api-version=2019-03-01`
```

```json
{
  "properties": {
    "protectionPolicy": {
      "protectFromScaleIn": true,
      "protectFromScaleSetActions": true
    }
  }        
}

```

> [!NOTE]
>執行個體保護僅適用於 api 2019-03-01 版和更新版本。</br>
保護執行個體從擴展集動作也會保護執行個體從起始的自動調整相應縮小。 您無法指定 「 protectFromScaleIn": false 時設定 「 protectFromScaleSetActions": true

### <a name="azure-powershell"></a>Azure PowerShell

使用[更新 AzVmssVM](/powershell/module/az.compute/update-azvmssvm) cmdlet，將套用 距標尺的保護設定為您的擴展集執行個體的動作。

下列範例會從擴展集動作中擁有執行個體識別碼 0 的擴展集執行個體的保護。

```azurepowershell-interactive
Update-AzVmssVM `
  -ResourceGroupName "myResourceGroup" `
  -VMScaleSetName "myVMScaleSet" `
  -InstanceId 0 `
  -ProtectFromScaleIn $true `
  -ProtectFromScaleSetAction $true
```

### <a name="azure-cli-20"></a>Azure CLI 2.0

使用[az vmss 更新](/cli/azure/vmss#az-vmss-update)套用到您的擴展集執行個體從擴展集動作的保護。

下列範例會從擴展集動作中擁有執行個體識別碼 0 的擴展集執行個體的保護。

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --instance-id 0 \
  --protect-from-scale-in true \
  --protect-from-scale-set-actions true
```

## <a name="troubleshoot"></a>疑難排解
### <a name="no-protectionpolicy-on-scale-set-model"></a>在擴展集模型沒有 protectionPolicy
只有適用於擴展集執行個體上，而不是在擴展集模型執行個體保護。

### <a name="no-protectionpolicy-on-scale-set-instance-model"></a>在擴展集執行個體模型上沒有 protectionPolicy
根據預設，保護原則不套用至執行個體建立時。

您可以套用至擴展集執行個體之後建立的執行個體, 的執行個體保護。

### <a name="not-able-to-apply-instance-protection"></a>無法將執行個體保護
執行個體保護僅適用於 api 2019-03-01 版和更新版本。 檢查所使用的 API 版本，並視需要更新。 此外，您可能也需要更新為最新版本的 PowerShell 或 CLI。

## <a name="next-steps"></a>後續步驟
了解如何在虛擬機器擴展集上[部署您的應用程式](virtual-machine-scale-sets-deploy-app.md)。
