---
title: Azure Kubernetes Service (AKS) 中的網路組態
description: 了解 Azure Kubernetes Service (AKS) 中的基本和進階網路組態。
services: container-service
author: mmacy
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 05/07/2018
ms.author: marsma
ms.openlocfilehash: 80d12d1f5d6b388c46ed90eb84b7bc00250e17ff
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/07/2018
---
# <a name="network-configuration-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) 中的網路組態

在建立 Azure Kubernetes Service (AKS) 叢集時，有兩個網路選項可供選取：**基本**或**進階**。

## <a name="basic-networking"></a>基本網路

**基本**網路選項是建立 AKS 叢集時的預設組態。 叢集的網路組態和其 Pod 完全是由 Azure 管理的，適合不需要自訂 VNet 組態的部署使用。 您沒有網路組態的控制權，例如，選取基本網路時指派給叢集的子網路或 IP 位址範圍。

設定為基本網路的 AKS 叢集節點會使用 [kubenet][kubenet] Kubernetes 外掛程式。

## <a name="advanced-networking"></a>進階網路

**進階**網路會將 Pod 放置在您所設定的 Azure 虛擬網路 (VNet) 中，以便讓 Pod 能夠自動連線至 VNet 資源，並與 Vnet 所提供的一組豐富功能做整合。
目前，當您在 [Azure 入口網站][portal]中部署 AKS 叢集或使用 Resource Manager 範本來部署時，才能使用進階網路。

設定為進階網路的 AKS 叢集節點會使用 [Azure 容器網路介面 (CNI)][cni-networking] Kubernetes 外掛程式。

![此圖表顯示兩個節點，且各有橋接器將其連線至單一 Azure VNet][advanced-networking-diagram-01]

## <a name="advanced-networking-features"></a>進階網路功能

進階網路提供下列優點：

* 將 AKS 叢集部署到現有 VNet，或為叢集建立新的 VNet 和子網路。
* 叢集中的每個 Pod 都會獲派 VNet 中的 IP 位址，並可直接與叢集中的其他 Pod 以及 VNet 中的其他 VM 通訊。
* Pod 可以連線至對等互連 VNet 中的其他服務，也可以透過 ExpressRoute 和站對站 (S2S) VPN 連線來連線至內部部署網路。 您也可以從內部部署環境連線到 Pod。
* 透過 Azure Load Balancer 在外部或內部公開 Kubernetes 服務。 基本網路也有這項功能。
* 在已啟用服務端點的子網路中，Pod 可以安全地連線到 Azure 服務，例如，Azure 儲存體和 SQL DB。
* 使用使用者定義的路由 (UDR) 將流量從 Pod 路由到網路虛擬設備。
* Pod 可以存取公用網際網路上的資源。 基本網路也有這項功能。

> [!IMPORTANT]
> 設定為進階網路的每個 AKS 叢集節點最多可以裝載 **30 個 Pod**。 為了與 Azure CNI 外掛程式搭配使用所佈建的每個 VNet 限制只能有 **4096 個 IP 位址** (/20)。

## <a name="configure-advanced-networking"></a>設定進階網路

當您在 Azure 入口網站中[建立 AKS 叢集](kubernetes-walkthrough-portal.md)時，可針對進階網路設定下列參數：

**虛擬網路**：要作為 Kubernetes 叢集部署目的地的 VNet。 如果您要為叢集建立新的 VNet，請選取 [新建] 並遵循＜建立虛擬網路＞一節中的步驟。

**子網路**：VNet 內要用來部署叢集的子網路。 如果您要為叢集建立新的 VNet 子網路，請選取 [新建] 並遵循＜建立子網路＞一節中的步驟。

**Kubernetes 服務位址範圍**：Kubernetes 叢集服務 IP 的 IP 位址範圍。 此範圍不得位於叢集的 VNet IP 位址範圍內。

**Kubernetes DNS 服務 IP 位址**：叢集 DNS 服務的 IP 位址。 此位址必須位於 Kubernetes 服務位址範圍內。

**Docker 橋接器位址**：要指派給 Docker 橋接器的 IP 位址和網路遮罩。 此 IP 位址不得位於叢集的 VNet IP 位址範圍內。

下列擷取自 Azure 入口網站的螢幕擷取畫面，會顯示 AKS 叢集建立期間對這些設定進行配置的範例：

![Azure 入口網站中的進階網路組態][portal-01-networking-advanced]

## <a name="plan-ip-addressing-for-your-cluster"></a>規劃叢集的 IP 位址

設定為使用進階網路的叢集需要進行額外的規劃。 VNet 和其子網路的大小必須配合您規劃要在叢集中同時執行的 Pod 數目，並配合您的大小調整需求。

Pod 和叢集節點的 IP 位址會從 VNet 內的指定子網路來指派。 每個節點都會設定主要 IP (也就是節點本身的 IP) 和 30 個額外的 IP 位址 (由 Azure CNI 預先設定，並指派給排程至節點的 Pod)。 當您將叢集相應放大時，每個節點同樣也會設定子網路中的 IP 位址。

如先前所述，為了與 Azure CNI 外掛程式搭配使用所佈建的每個 VNet 限制只能有 **4096 個 IP 位址** (/20)。 設定為進階網路的每個叢集節點最多可以裝載 **30 個 Pod**。

## <a name="frequently-asked-questions"></a>常見問題集

下列問題和解答適用於**進階**網路組態。

* 是否可以使用 Azure CLI 設定進階網路？

  編號 目前，當您在 Azure 入口網站中部署 AKS 叢集或使用 Resource Manager 範本來部署時，才能使用進階網路。

* 是否可以在叢集子網路中部署 VM？

  編號 不支援在 Kubernetes 叢集所使用的子網路中部署 VM。 VM 可部署在相同 VNet 中，但不能部署在不同的子網路。

* *是否可以針對個別 Pod 設定網路原則？

  編號 目前不支援針對個別 Pod 設定網路原則。

* 是否可以設定可部署到節點的 Pod 數目上限？

  根據預設，每個節點最多可以裝載 30 個 Pod。 您目前只能在使用 Resource Manager 範本部署叢集時，藉由修改 `maxPods` 屬性來變更上限值。

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
[portal]: https://portal.azure.com

<!-- LINKS - Internal -->
[aks-ssh]: aks-ssh.md
