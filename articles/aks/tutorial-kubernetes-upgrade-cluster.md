---
title: Azure 上的 Kubernetes 教學課程 - 更新叢集
description: Azure 上的 Kubernetes 教學課程 - 更新叢集
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: tutorial
ms.date: 06/29/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: d66197b69a0804a49fabb72e9b97c77e000bdf88
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/29/2018
ms.locfileid: "37131638"
---
# <a name="tutorial-upgrade-kubernetes-in-azure-kubernetes-service-aks"></a>教學課程：在 Azure Kubernetes Service (AKS) 中升級 Kubernetes

使用 Azure CLI 可以升級 Azure Kubernetes Service (AKS) 叢集。 若要將執行應用程式的中斷情況降到最低，則會在升級過程中仔細地[隔離並清空][kubernetes-drain] Kubernetes 節點。

在本教學課程 (七個章節的第七部分) 中升級了 Kubernetes 叢集。 您完成的工作包括：

> [!div class="checklist"]
> * 識別目前和可用的 Kubernetes 版本
> * 升級 Kubernetes 節點
> * 驗證升級成功

## <a name="before-you-begin"></a>開始之前

在先前的教學課程中，已將應用程式封裝成容器映像、將此映像上傳至 Azure Container Registry，並已建立 Kubernetes 叢集。 該應用程式接著便在 Kubernetes 叢集上執行。

如果您尚未完成這些步驟，而想要跟著做，請回到[教學課程 1 – 建立容器映像][aks-tutorial-prepare-app]。

## <a name="get-cluster-versions"></a>取得叢集版本

升級叢集之前，請使用 [az aks get-upgrades][] 命令檢查哪些 Kubernetes 版本可進行升級。

```azurecli
az aks get-upgrades --name myAKSCluster --resource-group myResourceGroup --output table
```

在下列範例中，目前的節點版本是 1.9.6，以及 Upgrades 資料行下顯示的可用版本。

```
Name     ResourceGroup    MasterVersion    NodePoolVersion    Upgrades
-------  ---------------  ---------------  -----------------  ----------
default  myResourceGroup  1.9.6            1.9.6              1.10.3
```

## <a name="upgrade-cluster"></a>升級叢集

使用 [az aks upgrade][] 命令升級叢集節點。 下列範例將叢集更新為版本 *1.10.3*。

```azurecli
az aks upgrade --name myAKSCluster --resource-group myResourceGroup --kubernetes-version 1.10.3
```

下列扼要的範例輸出會顯示 *kubernetesVersion*，現在回報 1.10.3：

```json
{
  "agentPoolProfiles": [
    {
      "count": 3,
      "maxPods": 110,
      "name": "nodepool1",
      "osType": "Linux",
      "storageProfile": "ManagedDisks",
      "vmSize": "Standard_DS1_v2",
    }
  ],
  "dnsPrefix": "myAKSClust-myResourceGroup-19da35",
  "enableRbac": false,
  "fqdn": "myaksclust-myresourcegroup-19da35-bd54a4be.hcp.eastus.azmk8s.io",
  "id": "/subscriptions/<Subscription ID>/resourcegroups/myResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster",
  "kubernetesVersion": "1.10.3",
  "location": "eastus",
  "name": "myAKSCluster",
  "type": "Microsoft.ContainerService/ManagedClusters"
}
```

## <a name="validate-upgrade"></a>驗證升級

使用 [az aks show][] 命令，確認升級是否成功。

```azurecli
az aks show --name myAKSCluster --resource-group myResourceGroup --output table
```

輸出：

```json
Name          Location    ResourceGroup    KubernetesVersion    ProvisioningState    Fqdn
------------  ----------  ---------------  -------------------  -------------------  ----------------------------------------------------------------
myAKSCluster  eastus      myResourceGroup  1.10.3               Succeeded            myaksclust-myresourcegroup-19da35-bd54a4be.hcp.eastus.azmk8s.io
```

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已在 AKS 叢集中升級 Kubernetes。 已完成下列工作：

> [!div class="checklist"]
> * 識別目前和可用的 Kubernetes 版本
> * 升級 Kubernetes 節點
> * 驗證升級成功

跟著此連結以深入了解 AKS。

> [!div class="nextstepaction"]
> [AKS 概觀][aks-intro]

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-intro]: ./intro-kubernetes.md
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[az aks show]: /cli/azure/aks#az-aks-show
[az aks get-upgrades]: /cli/azure/aks#az-aks-get-upgrades
[az aks upgrade]: /cli/azure/aks#az-aks-upgrade