---
title: 高可用性和災害復原 Azure Kubernetes Service (AKS) 中
description: 了解叢集操作員的最佳作法，以達到您的應用程式，提供高可用性，並準備進行災害復原 Azure Kubernetes Service (AKS) 中的最大存留時間。
services: container-service
author: lastcoolnameleft
ms.service: container-service
ms.topic: conceptual
ms.date: 11/28/2018
ms.author: lastcoolnameleft
ms.openlocfilehash: 4afc1231e6c9fa49c04c7bf6dfe26ee5eb87cc31
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/04/2019
ms.locfileid: "66475159"
---
# <a name="best-practices-for-business-continuity-and-disaster-recovery-in-azure-kubernetes-service-aks"></a>因應 Azure Kubernetes Service (AKS) 中商務持續性和災害復原的最佳做法

當您在管理 Azure Kubernetes Service (AKS) 中的叢集時，應用程式的執行時間會變得很重要。 AKS 透過使用可用性設定組中的多個節點來提供高可用性。 但是，這些多個節點不保護系統不受區域失敗。 為了充分發揮您的執行時間，以維護業務持續性並準備進行災害復原預先規劃。

本文著重於如何規劃業務持續性和災害復原，AKS 中。 您會了解如何：

> [!div class="checklist"]
> * 規劃多個區域中的 AKS 叢集。
> * 跨多個叢集使用 Azure 流量管理員流量路由傳送。
> * 使用您的容器映像登錄的異地複寫。
> * 跨多個叢集中的應用程式狀態的計劃。
> * 跨多個區域中複寫儲存體。

## <a name="plan-for-multiregion-deployment"></a>多區域部署計劃

**最佳做法**：當您部署多個 AKS 叢集時，請選擇適用，AKS 的地區，並使用配對的區域。

AKS 叢集會部署到單一區域。 若要從區域失敗中保護您的系統，請跨不同區域中部署您的應用程式到多個 AKS 叢集。 當您規劃如何部署 AKS 叢集時，請考慮：

* [**AKS 區域可用性**](https://docs.microsoft.com/azure/aks/quotas-skus-regions#region-availability):請選擇靠近使用者的區域。 AKS 持續擴展至新的區域。
* [**Azure 配對區域**](https://docs.microsoft.com/azure/best-practices-availability-paired-regions):請針對您的地理區域選擇兩個彼此配對的區域。 配對的區域協調平台更新，並優先處理復原工作在需要時。
* **服務可用性**:決定經常性存取/經常性存取、 經常性存取/暖或熱/冷，是否應該是配對的區域。 您是否要在此同時，具有一個區域中執行這兩個區域*準備*啟動的流量提供服務？ 或者，您想要已準備服務流量的一個區域？

AKS 區域可用性與配對的區域是聯合的考量。 請將 AKS 叢集部署到設計用來一起管理區域災害復原的配對區域。 比方說，AKS 位在美國東部和美國西部。 這些區域配對。 當您建立的 AKS BC/DR 策略時，請選擇這兩個區域。

當您部署您的應用程式時，將另一個步驟新增到您的 CI/CD 管線，將部署到這些多個的 AKS 叢集。 如果您沒有更新您的部署管線，應用程式可能會部署到您的區域和 AKS 叢集中只有一個。 導向到次要地區的客戶流量不會收到最新的程式碼更新。

## <a name="use-azure-traffic-manager-to-route-traffic"></a>使用 Azure 流量管理員來路由傳送流量

**最佳做法**：Azure 流量管理員可以將客戶引導至其最接近 AKS 叢集和應用程式執行個體。 最佳的效能與備援能力，將所有的應用程式流量透過流量管理員導向之前會先移至您的 AKS 叢集。

如果您在不同區域中有多個 AKS 叢集，使用流量管理員來控制流量流動至每個叢集中執行的應用程式的方式。 [Azure 流量管理員](https://docs.microsoft.com/azure/traffic-manager/)是 DNS 型的流量負載平衡器，可以將網路流量分散到各個區域。 使用流量管理員將使用者路由傳送至叢集的回應時間為基礎，或依據地理位置。

![AKS 使用流量管理員](media/operator-best-practices-bc-dr/aks-azure-traffic-manager.png)

通常有一個單一的 AKS 叢集的客戶連線到指定的應用程式服務 IP 或 DNS 名稱。 在 multicluster 部署中，客戶需連線至每個 AKS 叢集上的服務會指向流量管理員 DNS 名稱。 使用流量管理員端點，以定義這些服務。 每個端點會*服務負載平衡器 IP*。 使用此設定將導向至不同的區域中的端點的網路流量從一個區域中的流量管理員端點。

![地理路由透過流量管理員](media/operator-best-practices-bc-dr/traffic-manager-geographic-routing.png)

流量管理員會執行 DNS 查閱，並傳回使用者的最適當的端點。 巢狀設定檔可以排定優先順序的主要位置。 例如，使用者通常應該連線到其最接近的地理區域。 如果該區域發生問題，流量管理員會將改為導向到次要地區的使用者。 這個方法可確保客戶可以連線到應用程式執行個體，即使其最接近的地理區域無法使用。

如需如何設定端點和路由的詳細資訊，請參閱[藉由使用流量管理員設定地理流量路由方法](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-configure-geographic-routing-method)。

### <a name="layer-7-application-routing-with-azure-front-door-service"></a>第 7 層路由與 Azure 前端服務的應用程式

流量管理員會使用 DNS （第 3 層），將圖形資料傳輸。 [Azure 的前端服務](https://docs.microsoft.com/azure/frontdoor/front-door-overview)提供 HTTP/HTTPS （第 7 層） 的路由選項。 Azure 前端服務的其他功能包括 SSL 終止、 自訂網域、 web 應用程式防火牆、 URL 重寫，和工作階段親和性。 請檢閱應用程式流量的需求，以了解哪一種解決方案最合適。

## <a name="enable-geo-replication-for-container-images"></a>為容器映像啟用異地複寫

**最佳做法**：將您的容器映像儲存在 Azure Container Registry 和異地複寫登錄，以每個 AKS 區域。

若要在 AKS 中部署及執行應用程式，您必須有辦法儲存和提取容器映像。 有了 AKS 的容器登錄庫整合，讓它可以安全地儲存您的容器映像或 Helm 圖表。 Container Registry 支援自動複寫到世界各地的 Azure 區域的 您的映像的多重主機異地複寫。 

若要改善效能和可用性，使用容器登錄異地複寫，其中有 AKS 叢集中的每個區域中建立登錄。 然後，每個 AKS 叢集會從相同的區域中的本機容器登錄提取容器映像：

![容器登錄異地複寫的容器映像](media/operator-best-practices-bc-dr/acr-geo-replication.png)

當您位於相同區域中使用提取映像的容器登錄異地複寫時，則結果為：

* **更快**:您在相同的 Azure 區域內提取映像的高速、 低延遲的網路連線。
* **更可靠**:如果一個區域無法使用時，您的 AKS 叢集會從可用的容器登錄提取映像。
* **成本較低**:資料中心之間沒有任何網路輸出費用。

異地複寫是一項功能*Premium* SKU 容器登錄庫。 如需如何設定異地複寫的詳細資訊，請參閱[Container Registry 的異地複寫](https://docs.microsoft.com/azure/container-registry/container-registry-geo-replication)。

## <a name="remove-service-state-from-inside-containers"></a>從容器內移除服務狀態

**最佳做法**：可能的話，則不會儲存在容器內的服務狀態。 請改用 Azure 的平台即服務 (PaaS)，支援多區域複寫。

*服務狀態*指服務正常運作所需的記憶體或磁碟上的資料。 狀態包括服務會讀取及寫入的資料結構和成員變數。 根據服務的架構方式檔案或儲存在磁碟的其他資源，也可能包含狀態。 例如，狀態可能會包含資料庫用來儲存資料和交易記錄的檔案。

狀態可以外部化或共置，以操作狀態的程式碼。 一般而言，您會使用資料庫或其他資料存放區，在透過網路執行不同的電腦上或在同一部電腦上執行跨處理序具體化狀態。

其中執行的處理程序不會保留狀態時，容器和微服務是最有彈性。 因為應用程式幾乎都包含某個狀態，用於 PaaS 的解決方案，例如 Azure Database MySQL、 Azure Database for PostgreSQL 或 Azure SQL Database。

若要建置可攜式應用程式，請參閱下列指導方針：

* [12 要素應用程式方法](https://12factor.net/)
* [在多個 Azure 區域中執行的 web 應用程式](https://docs.microsoft.com/azure/architecture/reference-architectures/app-service-web-app/multi-region)

## <a name="create-a-storage-migration-plan"></a>建立儲存體移轉計劃

**最佳做法**：如果您使用 Azure 儲存體，準備和測試如何將您的儲存體從主要區域移轉至備份區域。

您的應用程式可能會使用 Azure 儲存體，為其資料。 因為您的應用程式會分散到多個不同區域中的 AKS 叢集，您需要保留同步處理的儲存體。 以下是兩個常見的方式，將儲存體複寫：

* 以基礎結構為基礎的非同步複寫
* 以應用程式為基礎的非同步複寫

### <a name="infrastructure-based-asynchronous-replication"></a>以基礎結構為基礎的非同步複寫

即使在 pod 刪除之後，您的應用程式可能需要永續性儲存體。 在 Kubernetes 中，您可以使用永續性磁碟區來保存資料的儲存體。 永續性磁碟區會掛接到 VM 的節點，並再公開至的 pod。 永續性磁碟區會遵循 pod，即使 pod 會移到相同叢集內不同的節點。

您使用的複寫策略取決於您的儲存體解決方案。 常見的儲存體解決方案，例如[Gluster](https://docs.gluster.org/en/latest/Administrator%20Guide/Geo%20Replication/)， [Ceph](http://docs.ceph.com/docs/master/cephfs/disaster-recovery/)，[城堡](https://rook.io/docs/rook/master/disaster-recovery.html)，以及[Portworx](https://docs.portworx.com/scheduler/kubernetes/going-production-with-k8s.html#disaster-recovery-with-cloudsnaps)提供自己的災害復原指引和複寫。

一般的策略是要提供一個共同的儲存點，應用程式可以在其中寫入它們的資料。 然後，此資料會複寫到各個區域，再於本機存取。

![以基礎結構為基礎的非同步複寫](media/operator-best-practices-bc-dr/aks-infra-based-async-repl.png)

如果您使用 Azure 受控磁碟，您可以選擇複寫和 DR 解決方案，例如這些：

* [在 Azure 上的 Velero](https://github.com/heptio/velero/blob/master/site/docs/master/azure-config.md)
* [Azure Site Recovery](https://azure.microsoft.com/blog/asr-managed-disks-between-azure-regions/)

### <a name="application-based-asynchronous-replication"></a>以應用程式為基礎的非同步複寫

Kubernetes 目前提供應用程式為基礎的非同步複寫沒有原生實作。 因為容器與 Kubernetes 進行鬆散偶合，任何傳統的應用程式或語言方法應該會運作。 一般而言，應用程式本身會複寫儲存體要求，然後寫入每個叢集的基礎資料儲存區。

![以應用程式為基礎的非同步複寫](media/operator-best-practices-bc-dr/aks-app-based-async-repl.png)

## <a name="next-steps"></a>後續步驟

本文著重於商務持續性和災害復原考量 AKS 叢集。 如需有關在 AKS 叢集作業的詳細資訊，請參閱下列文章有關最佳作法：

* [多租用戶和叢集的隔離][aks-best-practices-cluster-isolation]
* [基本的 Kubernetes 排程器功能][aks-best-practices-scheduler]

<!-- INTERNAL LINKS -->
[aks-best-practices-scheduler]: operator-best-practices-scheduler.md
[aks-best-practices-cluster-isolation]: operator-best-practices-cluster-isolation.md
