---
title: 調整 Azure Kubernetes Service (AKS) 叢集
description: 了解如何在 Azure Kubernetes Service (AKS) 叢集中調整節點數目。
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 05/31/2019
ms.author: iainfoulds
ms.openlocfilehash: de3f8613c93715aecf7e9e066a8ad1d82e4379e3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66475141"
---
# <a name="scale-the-node-count-in-an-azure-kubernetes-service-aks-cluster"></a>在 Azure Kubernetes Service (AKS) 叢集中調整節點計數

如果資源需要應用程式變更，您可以手動調整 AKS 叢集，以執行不同數目的節點。 縮減規模時，會將節點仔細地[隔離並清空][kubernetes-drain] \(英文\)，以將對執行中應用程式造成的中斷情況降到最低。 當您相應增加時，AKS 會等到節點會標示`Ready`pod 排定在它們之前，Kubernetes 叢集。

## <a name="scale-the-cluster-nodes"></a>調整叢集節點

首先，取得*名稱*節點集區使用[az aks show] [ az-aks-show]命令。 下列範例會取得名為叢集的節點集區名稱*myAKSCluster*中*myResourceGroup*資源群組：

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --query agentPoolProfiles
```

下列範例輸出顯示 *name* 是 *nodepool1*：

```console
$ az aks show --resource-group myResourceGroup --name myAKSCluster --query agentPoolProfiles

[
  {
    "count": 1,
    "maxPods": 110,
    "name": "nodepool1",
    "osDiskSizeGb": 30,
    "osType": "Linux",
    "storageProfile": "ManagedDisks",
    "vmSize": "Standard_DS2_v2"
  }
]
```

使用[az aks 擴展][ az-aks-scale]命令來調整叢集節點。 下列範例會將名為 *myAKSCluster* 的叢集調整成單一節點。 提供來自前一個命令的您自身 *--nodepool-name*，例如 *nodepool1*：

```azurecli-interactive
az aks scale --resource-group myResourceGroup --name myAKSCluster --node-count 1 --nodepool-name <your node pool name>
```

下列範例輸出顯示叢集已成功調整為一個節點，如 *agentPoolProfiles* 區段中所示：

```json
{
  "aadProfile": null,
  "addonProfiles": null,
  "agentPoolProfiles": [
    {
      "count": 1,
      "maxPods": 110,
      "name": "nodepool1",
      "osDiskSizeGb": 30,
      "osType": "Linux",
      "storageProfile": "ManagedDisks",
      "vmSize": "Standard_DS2_v2",
      "vnetSubnetId": null
    }
  ],
  [...]
}
```

## <a name="next-steps"></a>後續步驟

在本文中，您可以手動調整 AKS 叢集中增加或減少的節點數目。 您也可以使用[叢集 autoscaler] [ cluster-autoscaler] （目前在 AKS 中的預覽） 來自動調整您的叢集。

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-aks-scale]: /cli/azure/aks#az-aks-scale
[cluster-autoscaler]: cluster-autoscaler.md
