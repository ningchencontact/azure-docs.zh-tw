---
title: 使用適用于 Linux Vm 的鄰近放置群組預覽 |Microsoft Docs
description: 瞭解如何在 Azure 中建立和使用 Linux 虛擬機器的鄰近放置群組。
services: virtual-machines-linux
author: cynthn
manager: jeconnoc
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/01/2019
ms.author: cynthn
ms.openlocfilehash: a3009c9863d8322e3fe34bd99d64b93f0aa3d858
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/22/2019
ms.locfileid: "68385739"
---
# <a name="preview-deploy-vms-to-proximity-placement-groups-using-azure-cli"></a>預覽：使用 Azure CLI 將 Vm 部署至鄰近放置群組

若要盡可能接近 Vm, 達到最低可能的延遲, 您應該將它們部署在[鄰近放置群組](co-location.md#preview-proximity-placement-groups)內。

鄰近放置群組是一種邏輯群組, 用來確保 Azure 計算資源實際位於彼此接近的位置。 鄰近放置群組適用于需要低延遲的工作負載。

> [!IMPORTANT]
> 鄰近放置群組目前為公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。
>
> 在預覽期間, 這些區域無法使用鄰近放置群組:**日本東部**、**澳大利亞東部**和**印度中部**。

## <a name="create-the-proximity-placement-group"></a>建立鄰近位置群組
使用[`az ppg create`](/cli/azure/ppg#az-ppg-create)建立鄰近放置群組。 

```azurecli-interactive
az group create --name myPPGGroup --location westus
az ppg create \
   -n myPPG \
   -g myPPGGroup \
   -l westus \
   -t standard 
```

## <a name="list-proximity-placement-groups"></a>列出鄰近放置群組

您可以使用[az ppg list](/cli/azure/ppg#az-ppg-list)來列出所有的鄰近放置群組。

```azurecli-interactive
az ppg list -o table
```

## <a name="create-a-vm"></a>建立 VM

使用[新的 az VM](/cli/azure/vm#az-vm-create), 在鄰近位置群組中建立 VM。
```azurecli-interactive
az vm create \
   -n myVM \
   -g myPPGGroup \
   --image UbuntuLTS \
   --ppg myPPG  \
   --generate-ssh-keys \
   --size Standard_D1_v2  \
   -l westus
```

您可以使用[az ppg show](/cli/azure/ppg#az-ppg-show)在鄰近放置群組中查看 VM。

```azurecli-interactive
az ppg show --name myppg --resource-group myppggroup --query "virtualMachines"
```

## <a name="availability-sets"></a>可用性設定組
您也可以在鄰近放置群組中建立可用性設定組。 使用相同`--ppg`的參數搭配[az vm availability-set create](/cli/azure/vm/availability-set#az-vm-availability-set-create)來建立可用性設定組, 而且可用性設定組中的所有 vm 也會在相同的鄰近放置群組中建立。

## <a name="scale-sets"></a>擴展集

您也可以在鄰近放置群組中建立擴展集。 使用與`--ppg` [az vmss create](/cli/azure/vmss?view=azure-cli-latest#az-vmss-create)相同的參數來建立擴展集, 並在相同的鄰近放置群組中建立所有實例。

## <a name="next-steps"></a>後續步驟

深入瞭解鄰近放置群組的[Azure CLI](/cli/azure/ppg)命令。
