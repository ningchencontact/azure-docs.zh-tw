---
title: Azure Kubernetes Service (AKS) 中的網路組態
description: 了解 Azure Kubernetes Service (AKS) 中的基本和進階網路組態。
services: container-service
author: mmacy
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 07/23/2018
ms.author: marsma
ms.openlocfilehash: cfe034d6dcac48d7c9e4b2ce17e4926a81a27886
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2018
ms.locfileid: "39216099"
---
# <a name="network-configuration-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) 中的網路組態

在建立 Azure Kubernetes Service (AKS) 叢集時，有兩個網路選項可供選取：**基本**或**進階**。

## <a name="basic-networking"></a>基本網路

**基本**網路選項是建立 AKS 叢集時的預設組態。 叢集的網路組態和其 Pod 完全是由 Azure 管理的，適合不需要自訂 VNet 組態的部署使用。 您沒有網路組態的控制權，例如，選取基本網路時指派給叢集的子網路或 IP 位址範圍。

設定為基本網路的 AKS 叢集節點會使用 [kubenet][kubenet] Kubernetes 外掛程式。

## <a name="advanced-networking"></a>進階網路


  **進階**網路會將 Pod 放置在您所設定的 Azure 虛擬網路 (VNet) 中，以便讓 Pod 能夠自動連線至 VNet 資源，並與 Vnet 所提供的一組豐富功能做整合。 使用 [Azure 入口網站][portal]、Azure CLI 或 Resource Manager 範本來部署 AKS 叢集時，可以使用進階網路功能。

設定為進階網路的 AKS 叢集節點會使用 [Azure 容器網路介面 (CNI)][cni-networking] Kubernetes 外掛程式。

![此圖表顯示兩個節點，且各有橋接器將其連線至單一 Azure VNet][advanced-networking-diagram-01]

## <a name="advanced-networking-features"></a>進階網路功能

進階網路提供下列優點：

* 將 AKS 叢集部署到現有 VNet，或為叢集建立新的 VNet 和子網路。
* 叢集中的每個 Pod 都會獲派 VNet 中的 IP 位址，並可直接與叢集中的其他 Pod 以及 VNet 中的其他節點通訊。
* Pod 可以連線至對等互連 VNet 中的其他服務，也可以透過 ExpressRoute 和站對站 (S2S) VPN 連線來連線至內部部署網路。 您也可以從內部部署環境連線到 Pod。
* 透過 Azure Load Balancer 在外部或內部公開 Kubernetes 服務。 基本網路也有這項功能。
* 在已啟用服務端點的子網路中，Pod 可以安全地連線到 Azure 服務，例如，Azure 儲存體和 SQL DB。
* 使用使用者定義的路由 (UDR) 將流量從 Pod 路由到網路虛擬設備。
* Pod 可以存取公用網際網路上的資源。 基本網路也有這項功能。

## <a name="advanced-networking-prerequisites"></a>進階網路功能的必要條件

* 適用於 AKS 叢集的 VNet 必須允許輸出網際網路連線。
* 請勿在相同子網路中建立多個 AKS 叢集。
* AKS 叢集可能不會針對 Kubernetes 服務位址範圍使用 `169.254.0.0/16`、`172.30.0.0/16` 或 `172.31.0.0/16`。
* AKS 叢集所使用的服務主體在您 VNet 內的子網路上必須至少具有[網路參與者](../role-based-access-control/built-in-roles.md#network-contributor)權限。 如果您想要定義[自訂角色](../role-based-access-control/custom-roles.md)，而不使用內建的網路參與者角色，則需要下列權限：
  * `Microsoft.Network/virtualNetworks/subnets/join/action`
  * `Microsoft.Network/virtualNetworks/subnets/read`

## <a name="plan-ip-addressing-for-your-cluster"></a>規劃叢集的 IP 位址

設定為使用進階網路的叢集需要進行額外的規劃。 您的 VNet 及其子網路的大小必須配合您規劃要執行的 Pod 數目以及叢集的節點數目。

Pod 和叢集節點的 IP 位址會從 VNet 內的指定子網路來指派。 每個節點都會設定主要 IP (也就是節點的 IP) 和 30 個額外的 IP 位址 (由 Azure CNI 預先設定，並指派給排程至節點的 Pod)。 當您將叢集相應放大時，每個節點同樣也會設定子網路中的 IP 位址。

適用於 AKS 叢集的 IP 位址方案會由一個 VNet、至少一個適用於節點和 Pod 的子網路，以及一個 Kubernetes 服務位址範圍所組成。

| 位址範圍 / Azure 資源 | 限制和調整大小 |
| --------- | ------------- |
| 虛擬網路 | Azure VNet 可以和 /8 一樣大，但可能只有 16,000 個已設定的 IP 位址。 |
| 子網路 | 必須大到足以容納節點、Pod，以及可能會在您叢集中佈建的所有 Kubernetes 和 Azure 資源。 例如，如果您部署內部 Azure Load Balancer，其前端 IP 會從叢集子網路配置，而不是從公用 IP 配置。 <p/>若要計算「最小」的子網路大小：`(number of nodes) + (number of nodes * pods per node)` <p/>50 個節點叢集的範例：`(50) + (50 * 30) = 1,550` (/21 或更大) |
| Kubernetes 服務位址範圍 | 此範圍不應由此 VNet 上或連線到此 VNet 的任何網路元素所使用。 服務位址 CIDR 必須小於 /12。 |
| Kubernetes DNS 服務 IP 位址 | 將由叢集服務探索 (kube-dns) 所使用之 Kubernetes 服務位址範圍內的 IP 位址。 |
| Docker 橋接器位址 | 用來作為節點上 Docker 橋接器 IP 位址的 IP 位址 (採用 CIDR 標記法)。 預設值為 172.17.0.1/16。 |

為了與 Azure CNI 外掛程式搭配使用所佈建的每個 VNet 限制只能有 **16,000 個已設定的 IP 位址**。

## <a name="maximum-pods-per-node"></a>每個節點的最大 Pod 數目

AKS 叢集中每個節點預設的最大 Pod 數目，會根據基本和進階網路功能以及叢集部署方法而有所不同。

### <a name="default-maximum"></a>預設的最大值

* 基本網路功能：**每個節點 110 個 Pod**
* 進階網路功能：**每個節點 30 個 Pod**

### <a name="configure-maximum"></a>設定最大值

根據您的部署方法而定，您可能可在 AKS 叢集中修改每個節點的最大 Pod 數目。

* **Azure CLI**：當您使用 [az aks create][ az-aks-create] 命令部署叢集時，請指定 `--max-pods` 引數。
* **Resource Manager 範本**：當您使用 Resource Manager 範本部署叢集時，請指定 [ManagedClusterAgentPoolProfile] 物件中的 `maxPods` 屬性。
* **Azure 入口網站**：當您使用 Azure 入口網站部署叢集時，無法修改每個節點的最大 Pod 數目。 在 Azure 入口網站中部署時，進階網路功能的叢集限制為每個節點 30 個 Pod。

## <a name="deployment-parameters"></a>部署參數

當您建立 AKS 叢集時，可針對進階網路功能設定下列參數：

**虛擬網路**：要作為 Kubernetes 叢集部署目的地的 VNet。 如果您要為叢集建立新的 VNet，請選取 [新建] 並遵循＜建立虛擬網路＞一節中的步驟。 VNet 限制為 16,000 個已設定的 IP 位址。

**子網路**：VNet 內要用來部署叢集的子網路。 如果您要為叢集建立新的 VNet 子網路，請選取 [新建] 並遵循＜建立子網路＞一節中的步驟。

**Kubernetes 服務位址範圍**：「Kubernetes 服務位址範圍」是要將其中的位址指派給叢集中 Kubernetes 服務的 IP 範圍 (如需有關 Kubernetes 服務的詳細資訊，請參閱 Kubernetes 文件中的[服務][ services])。

Kubernetes 服務 IP 位址範圍：

* 不得位於叢集的 VNet IP 位址範圍內
* 不得與叢集 VNet 對等的任何其他 VNet 重疊
* 不得與任何內部部署 IP 重疊

如果使用重疊的 IP 範圍，就會造成無法預期的行為。 例如，如果某個 Pod 嘗試存取叢集以外的 IP，而該 IP 也會成為服務 IP，則您可能會看見無法預期的行為和失敗。

**Kubernetes DNS 服務 IP 位址**：叢集 DNS 服務的 IP 位址。 此位址必須位於 Kubernetes 服務位址範圍內。

**Docker 橋接器位址**：要指派給 Docker 橋接器的 IP 位址和網路遮罩。 此 IP 位址不得位於叢集的 VNet IP 位址範圍內。

## <a name="configure-networking---cli"></a>設定網路功能 - CLI

當您使用 Azure CLI 來建立 AKS 叢集時，也可以設定進階網路功能。 使用下列命令來建立新的 AKS 叢集，並啟用進階網路能功能。

首先，針對將聯結 AKS 叢集的現有子網路取得子網路資源識別碼：

```console
$ az network vnet subnet list --resource-group myVnet --vnet-name myVnet --query [].id --output tsv

/subscriptions/d5b9d4b7-6fc1-46c5-bafe-38effaed19b2/resourceGroups/myVnet/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/default
```

使用 [az aks create][az-aks-create] 命令搭配 `--network-plugin azure` 參數來建立具備進階網路功能的叢集。 使用在上一個步驟中收集的子網路識別碼來更新 `--vnet-subnet-id` 值：

```azurecli
az aks create --resource-group myAKSCluster --name myAKSCluster --network-plugin azure --vnet-subnet-id <subnet-id> --docker-bridge-address 172.17.0.1/16 --dns-service-ip 10.2.0.10 --service-cidr 10.2.0.0/24
```

## <a name="configure-networking---portal"></a>設定網路功能 - 入口網站

下列擷取自 Azure 入口網站的螢幕擷取畫面，會顯示 AKS 叢集建立期間對這些設定進行配置的範例：

![Azure 入口網站中的進階網路組態][portal-01-networking-advanced]

## <a name="frequently-asked-questions"></a>常見問題集

下列問題和解答適用於**進階**網路組態。

* 是否可以在叢集子網路中部署 VM？

  否。 不支援在 Kubernetes 叢集所使用的子網路中部署 VM。 VM 可部署在相同 VNet 中，但不能部署在不同的子網路。

* 是否可以針對個別 Pod 設定網路原則？

  否。 目前不支援針對個別 Pod 設定網路原則。

* 是否可以設定可部署到節點的 Pod 數目上限？

  是，當您使用 Azure CLI 或 Resource Manager 範本部署叢集時。 請參閱[每個節點的最大 Pod 數目](#maximum-pods-per-node)。

* 如何針對在 AKS 叢集建立期間所建立的子網路設定其他屬性？例如，服務端點。

  在 AKS 叢集建立期間所建立的 VNet 和子網路屬性完整清單，均可在 Azure 入口網站的標準 VNet 組態頁面中進行設定。

## <a name="next-steps"></a>後續步驟

### <a name="networking-in-aks"></a>AKS 中的網路

在下列文章中深入了解 AKS 的網路功能：

[搭配 Azure Kubernetes Service (AKS) 負載平衡器使用靜態 IP 位址](static-ip.md)

[Azure Container Service (AKS) 上的 HTTPS 輸入](ingress.md)

[搭配 Azure Container Service (AKS) 使用內部負載平衡器](internal-lb.md)

### <a name="acs-engine"></a>ACS 引擎

[Azure Container Service 引擎 (ACS 引擎)][acs-engine] 是一項開放原始碼專案，會產生 Azure Resource Manager 範本供您用於在 Azure 上部署已啟用 Docker 的叢集。 Kubernetes、DC/OS、Swarm Mode 和 Swarm 協調器可與 ACS 引擎一起部署。

使用 ACS 引擎所建立的 Kubernetes 叢集同時支援 [kubenet][kubenet] 和 [Azure CNI][cni-networking] 外掛程式。 因此，基本和進階網路都是 ACS 引擎可支援的案例。

<!-- IMAGES -->
[advanced-networking-diagram-01]: ./media/networking-overview/advanced-networking-diagram-01.png
[portal-01-networking-advanced]: ./media/networking-overview/portal-01-networking-advanced.png

<!-- LINKS - External -->
[acs-engine]: https://github.com/Azure/acs-engine
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[kubenet]: https://kubernetes.io/docs/concepts/cluster-administration/network-plugins/#kubenet
[services]: https://kubernetes.io/docs/concepts/services-networking/service/
[portal]: https://portal.azure.com

<!-- LINKS - Internal -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[aks-ssh]: aks-ssh.md
[ManagedClusterAgentPoolProfile]: /azure/templates/microsoft.containerservice/managedclusters#managedclusteragentpoolprofile-object
