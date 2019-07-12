---
title: 升級 Azure Kubernetes Service (AKS) 叢集
description: 了解如何升級 Azure Kubernetes Service (AKS) 叢集
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 05/31/2019
ms.author: mlearned
ms.openlocfilehash: dd88b5a044fe495da374178be8774f45bdd30f61
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/07/2019
ms.locfileid: "67614055"
---
# <a name="upgrade-an-azure-kubernetes-service-aks-cluster"></a>升級 Azure Kubernetes Service (AKS) 叢集

作為 AKS 叢集生命週期的一部分，您通常需要升級至最新的 Kubernetes 版本。 套用最新的 Kubernetes 安全性版本，或升級以取得最新的功能非常重要。 這篇文章會示範如何升級的主要元件或單一的預設值為 AKS 叢集中的節點集區。

AKS 叢集使用多個節點的集區或 Windows Server 的節點 （包括目前處於預覽狀態，AKS 中），請參閱[升級 AKS 中的節點集區][nodepool-upgrade]。

## <a name="before-you-begin"></a>開始之前

本文會要求您執行 Azure CLI 2.0.65 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI][azure-cli-install]。

## <a name="check-for-available-aks-cluster-upgrades"></a>檢查可用的 AKS 叢集升級

若要檢查哪些 Kubernetes 版本可供您的叢集，請使用[az aks get-升級][az-aks-get-upgrades]命令。 下列範例會在名為 *myResourceGroup* 的資源群組中，查看名為 *myAKSCluster* 的叢集的可用升級：

```azurecli-interactive
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster --output table
```

> [!NOTE]
> 當您升級 AKS 叢集時，無法略過 Kubernetes 次要版本。 例如，升級之間*1.11.x* -> *1.12.x*或是*1.12.x* -> *1.13.x*允許，不過*1.11.x* -> *1.13.x*不是。
>
> 若要升級，從*1.11.x* -> *1.13.x*，首先從升級*1.11.x* -> *1.12.x*，接著再升級從*1.12.x* -> *1.13.x*。

下列範例輸出顯示，可以將叢集升級為版本*1.12.7*或是*1.12.8*:

```console
Name     ResourceGroup    MasterVersion  NodePoolVersion  Upgrades
-------  ---------------  -------------  ---------------  --------------
default  myResourceGroup  1.11.9         1.11.9           1.12.7, 1.12.8
```

## <a name="upgrade-an-aks-cluster"></a>升級 AKS 叢集

為您的 AKS 叢集的可用版本清單，請使用[az aks upgrade][az-aks-upgrade] command to upgrade. During the upgrade process, AKS adds a new node to the cluster that runs the specified Kubernetes version, then carefully [cordon and drains][kubernetes-drain]其中一個可執行應用程式的中斷情況降到舊的節點。 確認新的節點做為執行中的應用程式 pod 時，就會刪除舊的節點。 要等到升級叢集中所有節點之後，就會重複此程序。

下列範例會將叢集升級到版本*1.12.8*:

```azurecli-interactive
az aks upgrade --resource-group myResourceGroup --name myAKSCluster --kubernetes-version 1.12.8
```

叢集升級需要幾分鐘的時間，具體取決於您擁有多少節點。

若要確認升級是否成功，請使用[az aks 顯示][az-aks-show]命令：

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --output table
```

下列範例輸出會顯示叢集現在執行*1.12.8*:

```json
Name          Location    ResourceGroup    KubernetesVersion    ProvisioningState    Fqdn
------------  ----------  ---------------  -------------------  -------------------  ---------------------------------------------------------------
myAKSCluster  eastus      myResourceGroup  1.12.8               Succeeded            myaksclust-myresourcegroup-19da35-90efab95.hcp.eastus.azmk8s.io
```

## <a name="next-steps"></a>後續步驟

本文說明如何升級現有的 AKS 叢集。 若要深入了解部署和管理 AKS 叢集，請參閱教學課程集合。

> [!div class="nextstepaction"]
> [AKS 教學課程][aks-tutorial-prepare-app]

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-get-upgrades]: /cli/azure/aks#az-aks-get-upgrades
[az-aks-upgrade]: /cli/azure/aks#az-aks-upgrade
[az-aks-show]: /cli/azure/aks#az-aks-show
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
