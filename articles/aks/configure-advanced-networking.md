---
title: 在 Azure Kubernetes Service (AKS) 中設定的進階網路
description: 了解如何在 Azure Kubernetes Service (AKS) 中設定進階網路，包括將 AKS 叢集部署到現有的虛擬網路和子網路中。
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 10/11/2018
ms.author: iainfou
ms.openlocfilehash: 4c60474c07a3853e409436359713578178b639fb
ms.sourcegitcommit: f6050791e910c22bd3c749c6d0f09b1ba8fccf0c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50024849"
---
# <a name="configure-advanced-networking-in-azure-kubernetes-service-aks"></a>在 Azure Kubernetes Service (AKS) 中設定進階網路

根據預設，AKS 叢集會使用*基本*網路功能，建立並設定用於叢集的虛擬網路和子網路。 若要進一步控制這些網路選項 (例如 IP 範圍)，您可以改為使用*進階*網路。 使用進階網路時，您也可以在現有的虛擬網路和子網路中建立 AKS 叢集。 這個現有的虛擬網路通常會使用 Azure ExpressRoute 或站對站 VPN 提供內部部署網路的連線。

本文將說明如何使用進階網路來建立虛擬網路並將其與 AKS 叢集搭配使用。 如需更多關於網路功能的一般資訊，請參閱 [Kubernetes 和 AKS 的網路概念][aks-network-concepts]。

## <a name="prerequisites"></a>必要條件

* 適用於 AKS 叢集的虛擬網路必須允許輸出網際網路連線.
* 請勿在相同子網路中建立多個 AKS 叢集。
* AKS 叢集可能不會針對 Kubernetes 服務位址範圍使用 `169.254.0.0/16`、`172.30.0.0/16` 或 `172.31.0.0/16`。
* AKS 叢集所使用的服務主體在您虛擬網路內的子網路上必須至少具有[網路參與者](../role-based-access-control/built-in-roles.md#network-contributor)權限。 如果您想要定義[自訂角色](../role-based-access-control/custom-roles.md)，而不使用內建的網路參與者角色，則需要下列權限：
  * `Microsoft.Network/virtualNetworks/subnets/join/action`
  * `Microsoft.Network/virtualNetworks/subnets/read`

## <a name="plan-ip-addressing-for-your-cluster"></a>規劃叢集的 IP 位址

設定為使用進階網路的叢集需要進行額外的規劃。 您的虛擬網路及其子網路的大小必須配合您規劃要執行的 Pod 數目以及叢集的節點數目。

Pod 和叢集節點的 IP 位址會從虛擬網路內的指定子網路來指派。 每個節點都以主要 IP 位址進行設定。 根據預設，Azure CNI 預先設定的 30 個額外 IP 位址會指派給在節點上排程的 Pod。 當您將叢集相應放大時，每個節點同樣也會設定子網路中的 IP 位址。 您也可以檢視[每個節點的最大 Pod 數目](#maximum-pods-per-node)。

適用於 AKS 叢集的 IP 位址方案會由一個虛擬網路、至少一個適用於節點和 Pod 的子網路，以及一個 Kubernetes 服務位址範圍所組成。

| 位址範圍 / Azure 資源 | 限制和調整大小 |
| --------- | ------------- |
| 虛擬網路 | Azure 虛擬網路可以和 /8 一樣大，但可能只有 65,536 個已設定的 IP 位址。 |
| 子網路 | 必須大到足以容納節點、Pod，以及可能會在您叢集中佈建的所有 Kubernetes 和 Azure 資源。 例如，如果您部署內部 Azure Load Balancer，其前端 IP 會從叢集子網路配置，而不是從公用 IP 配置。 <p/>若要計算「最小」的子網路大小：`(number of nodes) + (number of nodes * maximum pods per node that you configure)` <p/>50 個節點叢集的範例：`(50) + (50 * 30 (default)) = 1,550` (/21 或更大)<p>如果您未指定每個節點的最大 Pod 數目，當您建立叢集時，每個節點的最大 Pod 數目設定為 30。 IP 位址所需的最小數目是根據該值。 如果您以不同的最大值來計算最小 IP 位址需求，請參閱[如何設定每個節點的最大 Pod 數目](#configure-maximum---new-clusters)，在您部署叢集時設定此值。 |
| Kubernetes 服務位址範圍 | 此範圍不應由此虛擬網路上或連線到此虛擬網路的任何網路元素所使用。 服務位址 CIDR 必須小於 /12。 |
| Kubernetes DNS 服務 IP 位址 | 將由叢集服務探索 (kube-dns) 所使用之 Kubernetes 服務位址範圍內的 IP 位址。 |
| Docker 橋接器位址 | 用來作為節點上 Docker 橋接器 IP 位址的 IP 位址 (採用 CIDR 標記法)。 預設值為 172.17.0.1/16。 |

## <a name="maximum-pods-per-node"></a>每個節點的最大 Pod 數目

AKS 叢集中每個節點的最大 Pod 數目是 110。 每個節點預設的最大 Pod 數目，會根據「基本」和「進階」網路功能以及叢集部署方法而有所不同。

| 部署方法 | 基本預設值 | 進階預設值 | 可在部署時設定 |
| -- | :--: | :--: | -- |
| Azure CLI | 110 | 30 | 是 (最多 110) |
| Resource Manager 範本 | 110 | 30 | 是 (最多 110) |
| 入口網站 | 110 | 30 | 否 |

### <a name="configure-maximum---new-clusters"></a>設定最大值 - 新叢集

您*只能在叢集部署階段*設定每一節點的 Pod 數目上限。 如果您使用 Azure CLI 或 Resource Manager 範本進行部署，您可以為每一節點的 Pod 數目上限設定高達 110 的值。

* **Azure CLI**：當您使用 [az aks create][ az-aks-create] 命令部署叢集時，請指定 `--max-pods` 引數。 最大值為 110。
* **Resource Manager 範本**：當您使用 Resource Manager 範本部署叢集時，請指定 [ManagedClusterAgentPoolProfile] 物件中的 `maxPods` 屬性。 最大值為 110。
* **Azure 入口網站**：當您使用 Azure 入口網站部署叢集時，您無法變更每一節點的 Pod 數目上限。 使用 Azure 入口網站進行部署時，進階網路叢集限制為每一節點 30 個 Pod。

### <a name="configure-maximum---existing-clusters"></a>設定最大值 - 現有叢集

您無法在現有 AKS 叢集上變更每個節點的 Pod 數目上限。 只有在您一開始部署叢集時，才能調整此數目。

## <a name="deployment-parameters"></a>部署參數

當您建立 AKS 叢集時，可針對進階網路功能設定下列參數：

**虛擬網路**：要作為 Kubernetes 叢集部署目的地的虛擬網路。 如果您要為叢集建立新的虛擬網路，請選取 [新建] 並遵循＜建立虛擬網路＞一節中的步驟。 如需有關 Azure 虛擬網路限制和配額的資訊，請參閱 [Azure 訂用帳戶和服務限制、配額及條件約束](../azure-subscription-service-limits.md#azure-resource-manager-virtual-networking-limits)。

**子網路**：虛擬網路內要用來部署叢集的子網路。 如果您要在虛擬網路中為叢集建立新的子網路，請選取 [新建] 並遵循＜建立子網路＞一節中的步驟。

**Kubernetes 服務位址範圍**：這是 Kubernetes 指派給您叢集中[服務][services]的一組虛擬 IP。 您可以使用任何符合下列需求的私人位址範圍：

* 不得在叢集的虛擬網路 IP 位址範圍內
* 不得與叢集虛擬網路對等的任何其他虛擬網路重疊
* 不得與任何內部部署 IP 重疊
* 不得在 `169.254.0.0/16``172.30.0.0/16` 或 `172.31.0.0/16` 範圍內

雖然技術上有可能指定與您叢集相同虛擬網路內的服務位址範圍，但不建議這麼做。 如果使用重疊的 IP 範圍，就會造成無法預期的行為。 如需詳細資訊，請參閱本文的[常見問題集](#frequently-asked-questions)一節。 如需有關 Kubernetes 服務的詳細資訊，請參閱 Kubernetes 文件中的[服務][services]。

**Kubernetes DNS 服務 IP 位址**：叢集 DNS 服務的 IP 位址。 此位址必須位於 Kubernetes 服務位址範圍內。

**Docker 橋接器位址**：要指派給 Docker 橋接器的 IP 位址和網路遮罩。 IP 位址不得在叢集的虛擬網路 IP 位址範圍內。

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

  否。 不支援在 Kubernetes 叢集所使用的子網路中部署 VM。 虛擬機器可部署在相同虛擬網路中，但不能部署在不同的子網路。

* 是否可以針對個別 Pod 設定網路原則？

  否。 目前不支援針對個別 Pod 設定網路原則。

* 是否可以設定可部署到節點的 Pod 數目上限？

  是，當您使用 Azure CLI 或 Resource Manager 範本部署叢集時。 請參閱[每個節點的最大 Pod 數目](#maximum-pods-per-node)。

  您無法在現叢集上變更每個節點的 Pod 數目上限。

* 如何針對在 AKS 叢集建立期間所建立的子網路設定其他屬性？例如，服務端點。

  在 AKS 叢集建立期間所建立的虛擬網路和子網路屬性完整清單，均可在 Azure 入口網站的標準虛擬網路組態頁面中進行設定。

* *可以使用叢集虛擬網路內的不同子網路作為* **Kubernetes 服務位址範圍**嗎？

  不建議，但此組態是可行的。 服務位址範圍是 Kubernetes 指派給您叢集中服務的一組虛擬 IP (VIP)。 Azure 網路功能無法查看 Kubernetes 叢集的服務 IP 範圍。 因為無法查看叢集的服務位址範圍，所以稍後有可能在與服務位址範圍重疊的叢集虛擬網路中建立新的子網路。 如果發生這類重疊，Kubernetes 可能會將子網路中另一項資源已經使用的 IP 指派給服務，因而造成無法預期的行為或失敗。 您可藉由確保您使用叢集虛擬網路外部的位址範圍，避免此重疊風險。

## <a name="next-steps"></a>後續步驟

### <a name="networking-in-aks"></a>AKS 中的網路

在下列文章中深入了解 AKS 的網路功能：

- [搭配 Azure Kubernetes Service (AKS) 負載平衡器使用靜態 IP 位址](static-ip.md)
- [搭配 Azure Container Service (AKS) 使用內部負載平衡器](internal-lb.md)

- [建立具有外部網路連線的基本輸入控制器][aks-ingress-basic]
- [啟用 HTTP 應用程式路由附加元件][aks-http-app-routing]
- [建立使用內部私人網路和 IP 位址的輸入控制器][aks-ingress-internal]
- [使用動態公用 IP 建立輸入控制器，並設定 Let's Encrypt 以自動產生 TLS 憑證][aks-ingress-tls]
- [使用靜態公用 IP 建立輸入控制器，並設定 Let's Encrypt 以自動產生 TLS 憑證][aks-ingress-static-tls]

### <a name="acs-engine"></a>ACS 引擎

[Azure Container Service 引擎 (ACS 引擎)][acs-engine] 是一項開放原始碼專案，會產生 Azure Resource Manager 範本供您用於在 Azure 上部署已啟用 Docker 的叢集。 Kubernetes、DC/OS、Swarm Mode 和 Swarm 協調器可與 ACS 引擎一起部署。

使用 ACS 引擎所建立的 Kubernetes 叢集同時支援 [kubenet][kubenet] 和 [Azure CNI][cni-networking] 外掛程式。 因此，基本和進階網路都是 ACS 引擎可支援的案例。

<!-- IMAGES -->
[advanced-networking-diagram-01]: ./media/networking-overview/advanced-networking-diagram-01.png
[portal-01-networking-advanced]: ./media/networking-overview/portal-01-networking-advanced.png

<!-- LINKS - External -->
[acs-engine]: https://github.com/Azure/acs-engine
[services]: https://kubernetes.io/docs/concepts/services-networking/service/
[portal]: https://portal.azure.com
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[kubenet]: https://kubernetes.io/docs/concepts/cluster-administration/network-plugins/#kubenet

<!-- LINKS - Internal -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[aks-ssh]: ssh.md
[ManagedClusterAgentPoolProfile]: /azure/templates/microsoft.containerservice/managedclusters#managedclusteragentpoolprofile-object
[aks-network-concepts]: concepts-network.md
[aks-ingress-basic]: ingress-basic.md
[aks-ingress-tls]: ingress-tls.md
[aks-ingress-static-tls]: ingress-static-ip.md
[aks-http-app-routing]: http-application-routing.md
[aks-ingress-internal]: ingress-internal-ip.md
