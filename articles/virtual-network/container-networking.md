---
title: "容器的 azure 虛擬網路 |Microsoft 文件"
description: "深入了解 CNI 外掛程式 Kubernetes 叢集，這可讓容器，以及虛擬網路中的其他資源與通訊。"
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
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/19/2017
---
# <a name="container-networking"></a>容器的網路功能

Azure 提供[容器網路介面 (CNI) 外掛程式](https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md)，可讓您部署和管理自己的 Kubernetes 叢集與 Azure 的原生網路功能。 此外掛程式時，會啟用，根據預設，與部署 Kubernetes 叢集[Azure 容器服務引擎](https://github.com/Azure/acs-engine)（或 ACS 引擎）。

## <a name="networking-capabilities"></a>網路功能

容器可以利用一組豐富的功能，例如虛擬網路提供：
-   您可以為您的叢集，建立個別的虛擬網路，或您叢集部署在現有的虛擬網路。 
-   叢集中的每個 pod 接收從虛擬網路內的 IP 位址，即可直接通訊與叢集中其他 pod 和虛擬網路中的任何虛擬機器。 
-   Pod 可透過 ExpressRoute 和站對站 VPN 連線來連接其他 pod，所以虛擬網路中的虛擬機器，並在內部部署網路。 在內部部署資源可以傳達給 pod。 
-   您可以公開 Kubernetes 服務透過 Azure 負載平衡器。  
-   已啟用的服務端點的子網路的 pod 可以安全地連接 Azure 服務 （儲存體和 SQL 資料庫，例如）。
-   您可以使用使用者定義的路由 pod 的流量路由傳送至網路的虛擬設備。 
-   Pod 可以存取公用網際網路上的資源。
-   您可以指派為 pod 可以與 DNS 名稱相關聯的公用 IP 位址。
 
## <a name="limits"></a>限制
使用外掛程式時，您可以部署在 Kubernetes 叢集中，並最多 250 pod 每個節點，每個叢集 16,000 pod 整體限制最多 4,000 個節點。

## <a name="constraints"></a>條件約束
- 部署與 Kubernetes 叢集時，不會啟用此外掛程式[Azure 容器服務 (AKS)](../aks/intro-kubernetes.md?toc=%2fazure%2fvirtual-network%2ftoc.json)或[ACS](../container-service/kubernetes/container-service-intro-kubernetes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) Kubernetes 的叢集。
- 沒有原生支援 Kubernetes 網路原則，包括 DNS 或存取原則。
- 外掛程式不支援 pod 每個網路原則。

## <a name="pricing"></a>價格
沒有使用 CNI 外掛程式需付費。

## <a name="next-steps"></a>後續步驟

深入了解如何[部署 Kubernetes 叢集](https://github.com/Azure/acs-engine/blob/master/docs/kubernetes/deploy.md)中您[自己的虛擬網路](https://github.com/Azure/acs-engine/blob/master/docs/kubernetes/features.md#using-azure-integrated-networking-cni)。
