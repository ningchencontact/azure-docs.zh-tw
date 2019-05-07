---
title: 升級 Azure Kubernetes Service (AKS) 叢集
description: 了解如何升級 Azure Kubernetes Service (AKS) 叢集
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 02/12/2019
ms.author: iainfou
ms.openlocfilehash: 59d52db8c3f5f8968eae1a544abe1e5c6bbaacca
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2019
ms.locfileid: "65072749"
---
# <a name="upgrade-an-azure-kubernetes-service-aks-cluster"></a>升級 Azure Kubernetes Service (AKS) 叢集

作為 AKS 叢集生命週期的一部分，您通常需要升級至最新的 Kubernetes 版本。 套用最新的 Kubernetes 安全性版本，或升級以取得最新的功能非常重要。 本文會示範如何升級現有的 AKS 叢集。

## <a name="before-you-begin"></a>開始之前

本文需要您執行 Azure CLI 2.0.56 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI][azure-cli-install]。

## <a name="check-for-available-aks-cluster-upgrades"></a>檢查可用的 AKS 叢集升級

若要檢查哪些 Kubernetes 版本可用於您的叢集，請使用 [az aks get-upgrades][az-aks-get-upgrades] 命令。 下列範例會在名為 *myResourceGroup* 的資源群組中，查看名為 *myAKSCluster* 的叢集的可用升級：

```azurecli-interactive
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster --output table
```

> [!NOTE]
> 當您升級 AKS 叢集時，無法略過 Kubernetes 次要版本。 例如，允許 *1.10.x* -> *1.11.x* 或 *1.11.x* -> *1.12.x* 之間的升級，但不允許 *1.10.x* -> *1.12.x* 的升級。
>
> 若要從 *1.10.x* -> *1.12.x* 升級，請先從 *1.10.x* -> *1.11.x* 升級，然後從 *1.11.x* -> *1.12.x* 升級。

下列範例輸出顯示叢集可以升級到版本 *1.11.5* 或 *1.11.6*：

```console
Name     ResourceGroup    MasterVersion    NodePoolVersion    Upgrades
-------  ---------------  ---------------  -----------------  --------------
default  myResourceGroup  1.10.12          1.10.12            1.11.5, 1.11.6
```

## <a name="upgrade-an-aks-cluster"></a>升級 AKS 叢集

透過適用於您的 AKS 叢集的可用版本清單，使用 [az aks upgrade][az-aks-upgrade] 命令進行升級。 在升級過程中，AKS 會將新節點新增至叢集，然後仔細地[隔離並清空][kubernetes-drain]節點 (一次一個)，將中斷執行中應用程式的情況降到最低。 下列範例將叢集升級為版本 *1.11.6*：

```azurecli-interactive
az aks upgrade --resource-group myResourceGroup --name myAKSCluster --kubernetes-version 1.11.6
```

叢集升級需要幾分鐘的時間，具體取決於您擁有多少節點。

若要確認升級是否成功，請使用 [az aks show][az-aks-show] 命令：

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --output table
```

下列範例輸出顯示叢集目前執行 *1.11.6*：

```json
Name          Location    ResourceGroup    KubernetesVersion    ProvisioningState    Fqdn
------------  ----------  ---------------  -------------------  -------------------  ---------------------------------------------------------------
myAKSCluster  eastus      myResourceGroup  1.11.6               Succeeded            myaksclust-myresourcegroup-19da35-90efab95.hcp.eastus.azmk8s.io
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
