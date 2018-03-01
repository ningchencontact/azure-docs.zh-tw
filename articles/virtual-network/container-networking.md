---
title: "容器的 Azure 虛擬網路 | Microsoft Docs"
description: "了解 Kubernetes 叢集的 CNI 外掛程式，此外掛程式可讓容器得以與虛擬網路中的其他容器以及其他資源進行通訊。"
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: 
ms.assetid: 
ms.service: virtual-network
ms.devlang: NA
ms.topic: 
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/18/2017
ms.author: jdial
ms.openlocfilehash: f70afa8ff69b6c79363313c0f2df3b6785da8d81
ms.sourcegitcommit: c87e036fe898318487ea8df31b13b328985ce0e1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/19/2017
---
# <a name="container-networking"></a>容器網路服務

Azure 提供[容器網路介面 (CNI) 外掛程式](https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md)來讓您部署和管理具有原生 Azure 網路功能的自有 Kubernetes 叢集。 在使用 [Azure Container Service 引擎](https://github.com/Azure/acs-engine) (或 ACS 引擎) 部署 Kubernetes 叢集時，系統依預設會啟用此外掛程式。

## <a name="networking-capabilities"></a>網路功能

容器可以利用虛擬網路所提供的一組豐富功能，例如：
-   您可以為叢集建立個別的虛擬網路，或將叢集部署到現有虛擬網路中。 
-   叢集中的每個 Pod 都會從虛擬網路內收到 IP 位址，並可直接與叢集中的其他 Pod 以及虛擬網路中的任何虛擬機器通訊。 
-   Pod 可以透過 ExpressRoute 和站對站 VPN 連線，來連線到對等互連虛擬網路中的其他 Pod 和虛擬機器以及連線到內部部署網路。 內部部署資源可以和 Pod 通訊。 
-   您可以透過 Azure Load Balancer 對網際網路公開 Kubernetes 服務。  
-   位於已啟用服務端點之子網路的 Pod 可以安全地連線到 Azure 服務 (例如，儲存體和 SQL Database)。
-   您可以使用使用者定義的路由，將流量從 Pod 路由到網路虛擬設備。 
-   Pod 可以存取網際網路上的公用資源。
-   您可以對 Pod 指派可與 DNS 名稱相關聯的公用 IP 位址。
 
## <a name="limits"></a>限制
使用外掛程式時，Kubernetes 叢集中最多可以部署 4,000 個節點，每個節點最多可以有 250 個 Pod，每個叢集總共只能有 16,000 個 Pod。

## <a name="constraints"></a>條件約束
- 在使用 [Azure Container Service (AKS)](../aks/intro-kubernetes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 部署 Kubernetes 叢集或使用 Kubernetes 部署 [ACS](../container-service/kubernetes/container-service-intro-kubernetes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 叢集時，系統不會啟用此外掛程式。
- 未原生支援 Kubernetes 網路原則，包括 DNS 或存取原則。
- 此外掛程式不支援以個別 Pod 為基礎的網路原則。

## <a name="pricing"></a>價格
使用 CNI 外掛程式完全免費。

## <a name="next-steps"></a>後續步驟

了解如何在您[自己的虛擬網路](https://github.com/Azure/acs-engine/blob/master/docs/kubernetes/features.md#using-azure-integrated-networking-cni)中[部署 Kubernetes 叢集](https://github.com/Azure/acs-engine/blob/master/docs/kubernetes/deploy.md)。
