---
title: Azure Kubernetes Service （AKS）中的 API 伺服器授權 IP 範圍
description: 瞭解如何使用 IP 位址範圍來保護您的叢集，以存取 Azure Kubernetes Service 中的 API 伺服器（AKS）
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 05/06/2019
ms.author: mlearned
ms.openlocfilehash: 8418499cc3e094162ac7483aaa6c71e74db95ae1
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2019
ms.locfileid: "73472964"
---
# <a name="secure-access-to-the-api-server-using-authorized-ip-address-ranges-in-azure-kubernetes-service-aks"></a>在 Azure Kubernetes Service （AKS）中使用授權的 IP 位址範圍來保護 API 伺服器的存取

在 Kubernetes 中，API 伺服器會接收在叢集中執行動作的要求，例如建立資源或調整節點數目。 API 伺服器是與叢集互動和管理叢集的主要方式。 為了改善叢集安全性並將攻擊降至最低，您只能從一組有限的 IP 位址範圍存取 API 伺服器。

本文說明如何使用 API 伺服器授權的 IP 位址範圍，來限制哪些 IP 位址和 CIDRs 可以存取控制平面。

> [!IMPORTANT]
> 在新叢集上，只有*標準*SKU 負載平衡器才支援 API 伺服器授權的 IP 位址範圍。 已設定*基本*SKU 負載平衡器和 API 伺服器授權 IP 位址範圍的現有叢集將會繼續正常執行。 這些現有的 clusers 也可以升級，而且它們會繼續工作。

## <a name="before-you-begin"></a>開始之前

本文假設您使用的是使用[kubenet][kubenet]的叢集。  使用以[Azure Container 網路介面（CNI）][cni-networking]為基礎的叢集，您將不需要用來保護存取的必要路由表。  您將需要手動建立路由表。  如需詳細資訊，請參閱[管理路由表](https://docs.microsoft.com/azure/virtual-network/manage-route-table)。

API 伺服器授權的 IP 範圍僅適用于您所建立的新 AKS 叢集。 本文說明如何使用 Azure CLI 建立 AKS 叢集。

您需要安裝並設定 Azure CLI 版本2.0.76 或更新版本。 執行  `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱 [安裝 Azure CLI][install-azure-cli]。

## <a name="limitations"></a>限制

當您設定 API 伺服器授權的 IP 範圍時，適用下列限制：

* 您目前無法使用 Azure Dev Spaces，因為與 API 伺服器的通訊也會遭到封鎖。

## <a name="overview-of-api-server-authorized-ip-ranges"></a>API 伺服器授權的 IP 範圍總覽

Kubernetes API 伺服器是基礎 Kubernetes Api 的公開方式。 此元件可提供管理工具的互動，例如 `kubectl` 或 Kubernetes 儀表板。 AKS 提供具有專用 API 伺服器的單一租使用者叢集主機。 根據預設，會將公用 IP 位址指派給 API 伺服器，而且您應該使用角色型存取控制（RBAC）來控制存取權。

若要安全存取其他可公開存取的 AKS 控制平面/API 伺服器，您可以啟用並使用已授權的 IP 範圍。 這些授權的 IP 範圍只允許已定義的 IP 位址範圍與 API 伺服器通訊。 從不屬於這些授權 IP 範圍的 IP 位址對 API 伺服器提出的要求會遭到封鎖。 您應該繼續使用 RBAC 來授權使用者和他們所要求的動作。

如需有關 API 伺服器和其他叢集元件的詳細資訊，請參閱[AKS 的 Kubernetes 核心概念][concepts-clusters-workloads]。

## <a name="create-an-aks-cluster"></a>建立 AKS 叢集

API 伺服器授權的 IP 範圍僅適用于新的 AKS 叢集。 您無法在叢集建立作業中啟用授權的 IP 範圍。 如果您嘗試在叢集建立程式中啟用授權的 IP 範圍，叢集節點將無法在部署期間存取 API 伺服器，因為輸出 IP 位址未在該時間點定義。

首先，使用[az aks create][az-aks-create]命令來建立叢集。 下列範例會在名為*myResourceGroup*的資源群組中建立名為*myAKSCluster*的單一節點叢集。

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location eastus

# Create an AKS cluster
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 1 \
    --vm-set-type VirtualMachineScaleSets \
    --load-balancer-sku standard \
    --generate-ssh-keys

# Get credentials to access the cluster
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

## <a name="update-cluster-with-authorized-ip-ranges"></a>以授權的 IP 範圍更新叢集

根據預設，您的叢集會使用[標準 SKU 負載平衡器][standard-sku-lb]，您可以用它來設定輸出閘道。 使用[az network public ip list][az-network-public-ip-list]並指定 AKS 叢集的資源群組，其通常以*MC_* 開頭。 這會顯示您的叢集的公用 IP，您可以允許它。 使用[az aks update][az-aks-update]命令並指定 *--api-伺服器授權的 ip 範圍*參數，以允許叢集的 ip。 例如：

```azurecli-interactive
RG=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv)
SLB_PublicIP=$(az network public-ip list --resource-group $RG --query [].ipAddress -o tsv)
az aks update --api-server-authorized-ip-ranges $SLB_PublicIP --resource-group myResourceGroup --name myAKSCluster
```

若要啟用 API 伺服器授權的 IP 範圍，請使用[az aks update][az-aks-update]命令並指定 *--API-伺服器授權的 ip 範圍*參數，以提供授權的 ip 位址範圍清單。 這些 IP 位址範圍通常是內部部署網路或公用 Ip 所使用的位址範圍。 當您指定 CIDR 範圍時，請從範圍中的第一個 IP 位址開始。 例如， *137.117.106.90/29*是有效的範圍，但請務必指定範圍中的第一個 IP 位址，例如*137.117.106.88/29*。

下列範例會在名為*myResourceGroup*的資源群組中，于名為*myAKSCluster*的叢集上啟用 API 伺服器授權的 IP 範圍。 要授權的 IP 位址範圍是*172.0.0.0/16* （Pod/節點位址範圍）和*168.10.0.0/18* （ServiceCidr）：

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --api-server-authorized-ip-ranges 172.0.0.0/16,168.10.0.0/18
```

> [!NOTE]
> 您應該將這些範圍新增至允許清單：
> - 防火牆公用 IP 位址
> - 服務 CIDR
> - 子網的位址範圍，包含節點和 pod
> - 任何範圍，代表您將從中管理叢集的網路

## <a name="disable-authorized-ip-ranges"></a>停用授權的 IP 範圍

若要停用授權的 IP 範圍，請使用[az aks update][az-aks-update]並指定空的範圍來停用 API 伺服器授權的 IP 範圍。 例如：

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --api-server-authorized-ip-ranges ""
```

## <a name="next-steps"></a>後續步驟

在本文中，您已啟用 API 伺服器授權的 IP 範圍。 此方法是您可以如何執行安全 AKS 叢集的其中一個部分。

如需詳細資訊，請參閱[AKS 中應用程式和叢集的安全性概念][concepts-security]和[AKS 中叢集安全性和升級的最佳做法][operator-best-practices-cluster-security]。

<!-- LINKS - external -->
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[kubenet]: https://kubernetes.io/docs/concepts/extend-kubernetes/compute-storage-net/network-plugins/#kubenet

<!-- LINKS - internal -->
[az-aks-update]: /cli/azure/ext/aks-preview/aks#ext-aks-preview-az-aks-update
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-network-public-ip-list]: /cli/azure/network/public-ip#az-network-public-ip-list
[concepts-clusters-workloads]: concepts-clusters-workloads.md
[concepts-security]: concepts-security.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-cluster-security]: operator-best-practices-cluster-security.md
[standard-sku-lb]: load-balancer-standard.md
