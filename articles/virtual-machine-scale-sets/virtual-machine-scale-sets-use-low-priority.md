---
title: 建立使用低優先順序 VM 的 Azure 擴展集 (預覽) | Microsoft Docs
description: 了解如何建立使用低優先順序 VM 的 Azure 虛擬機器擴展集以節省成本
services: virtual-machine-scale-sets
documentationcenter: ''
author: mmccrory
manager: rajraj
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/15/2018
ms.author: memccror
ms.openlocfilehash: f25e4d1e3906a610e7c60e348f872a78d7db8fd3
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2018
---
# <a name="low-priority-vms-on-scale-sets-preview"></a>擴展集中的低優先順序 VM (預覽)

在擴展集中使用低優先順序的 VM，可讓您以大幅降低的成本使用我們未運用的容量。 在任何時候，只要 Azure 又需要這些容量，Azure 基礎結構就會收回低優先順序 VM。 因此，低優先順序 VM 對於可因應中斷的工作負載來說很好用，例如批次處理作業、開發/測試環境、大量運算工作負載等。

可使用的未運用容量數量取決於大小、區域、時段等因素。 如果在擴展集上部署低優先順序 VM，當有可使用的容量時 Azure 便會配置 VM，但這些 VM 沒有服務等級協定 (SLA)。 低優先順序擴展集是部署在單一容錯網域中，且不保證高可用性。

> [!NOTE]
> 低優先順序擴展集為預覽版功能，可供開發和測試案例使用。 

## <a name="eviction-policy"></a>收回原則

當 Azure 收回您的低優先順序擴展集 VM 時，依預設它們會進入已停止 (取消配置) 狀態。 使用此收回原則的情況下，您可以重新部署已收回的執行個體，但不保證配置會成功。 已停止的 VM 會依擴展集執行個體配額計算，您將需要支付您的基礎磁碟費用。 

如果您想要低優先順序擴展集中的 VM 在收回時一併刪除，可以在您的 [Azure Resource Manager 範本](#use-azure-resource-manager-templates)中將收回原則設定為刪除。 收回原則設定為刪除的情況下，您可以藉由增加擴展集執行個體計數屬性來建立新的 VM。 Azure 會一同刪除已收回的 VM 與其基礎磁碟，因此您將不需要支付此儲存體的費用。 您也可以使用擴展集的自動調整功能，自動嘗試和補償收回的 VM，不過不保證配置會成功。 建議您只在收回原則設定為刪除時，才讓低優先順序擴展集使用自動調整功能，以節省磁碟成本並避免達到配額限制。 

> [!NOTE]
> 在預覽期間，您能夠使用 [Azure Resource Manager 範本](#use-azure-resource-manager-templates)設定收回原則。 

## <a name="deploying-low-priority-vms-on-scale-sets"></a>在擴展集上部署低優先順序 VM

若要在擴展集上部署低優先順序 VM，可將新的 Priority 旗標設為 Low。 擴展集中的所有 VM 都會設定為低優先順序。 若要建立使用低優先順序 VM 的擴展集，請使用下列其中一個方法︰
- [Azure CLI 2.0](#use-the-azure-cli-20)
- [Azure PowerShell](#use-azure-powershell)
- [Azure 資源管理員範本](#use-azure-resource-manager-templates)

## <a name="use-the-azure-cli-20"></a>使用 Azure CLI 2.0

建立使用低優先順序 VM 之擴展集的程序，與[使用者入門文章](quick-create-cli.md)中所述的程序相同。 只要在 CLI 呼叫中新增 '--priority' 參數，並將它設定為 Low，如下列範例所示：

```azurecli
az vmss create \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --image UbuntuLTS \
    --upgrade-policy-mode automatic \
    --admin-username azureuser \
    --generate-ssh-keys \
    --priority Low
```

## <a name="use-azure-powershell"></a>使用 Azure PowerShell

建立使用低優先順序 VM 之擴展集的程序，與[使用者入門文章](quick-create-powershell.md)中所述的程序相同。
只要在 [New-AzureRmVmssConfig](/powershell/module/azurerm.compute/new-azurermvmssconfig) 中新增 '-priority' 參數，並將它設定為 Low，如下列範例所示：

```powershell
$vmssConfig = New-AzureRmVmssConfig `
    -Location "East US 2" `
    -SkuCapacity 2 `
    -SkuName "Standard_DS2" `
    -UpgradePolicyMode Automatic `
    -Priority "Low"
```

## <a name="use-azure-resource-manager-templates"></a>使用 Azure Resource Manager 範本

建立使用低優先順序 VM 之擴展集的程序，與 [Linux](quick-create-template-linux.md) 或 [Windows](quick-create-template-windows.md) 的使用者入門文章中所述的程序相同。 將 'priority' 屬性新增至範本中的 Microsoft.Compute/virtualMachineScaleSets/virtualMachineProfile 資源類型，並指定其值為 Low。 請務必使用 2017-10-30-preview API 版本或更高版本。 

為了要將收回原則設定為刪除，請新增 'evictionPolicy' 參數，並將它設定為 delete (刪除)。

下列範例會在「美國中西部」建立名為 myScaleSet 的 Linux 低優先順序擴展集，並會在收回擴展集中的 VM 時刪除 VM：

```json
{
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  "name": "myScaleSet",
  "location": "East US 2",
  "apiVersion": "2017-12-01",
  "sku": {
    "name": "Standard_DS2_v2",
    "capacity": "2"
  },
  "properties": {
    "upgradePolicy": {
      "mode": "Automatic"
    },
    "virtualMachineProfile": {
       "priority": "Low",
       "evictionPolicy": "delete",
       "storageProfile": {
        "osDisk": {
          "caching": "ReadWrite",
          "createOption": "FromImage"
        },
        "imageReference":  {
          "publisher": "Canonical",
          "offer": "UbuntuServer",
          "sku": "16.04-LTS",
          "version": "latest"
        }
      },
      "osProfile": {
        "computerNamePrefix": "myvmss",
        "adminUsername": "azureuser",
        "adminPassword": "P@ssw0rd!"
      }
    }
  }
}
```
## <a name="next-steps"></a>後續步驟
您已經建立使用低優先順序 VM 的擴展集，接下來可開始嘗試部署[使用低優先順序自動調整規模的範本](https://github.com/Azure/vm-scale-sets/tree/master/preview/lowpri)。

如需定價詳細資料，請參閱[虛擬機器擴展集定價網頁](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/linux/)。