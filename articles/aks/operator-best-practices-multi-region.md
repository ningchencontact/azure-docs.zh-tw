---
title: 操作員最佳做法 - Azure Kubernetes Service (AKS) 中的高可用性和災害復原
description: 了解叢集操作員最佳做法，讓應用程式獲得最大執行時間以提供高可用性，並針對 Azure Kubernetes Service (AKS) 中的災害復原狀況做好準備
services: container-service
author: lastcoolnameleft
ms.service: container-service
ms.topic: conceptual
ms.date: 11/28/2018
ms.author: lastcoolnameleft
ms.openlocfilehash: 622cdd36a1ecf582c4cdb883b12753ee2a75d50e
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/04/2018
ms.locfileid: "52853978"
---
# <a name="best-practices-for-business-continuity-and-disaster-recovery-in-azure-kubernetes-service-aks"></a>因應 Azure Kubernetes Service (AKS) 中商務持續性和災害復原的最佳做法

當您在管理 Azure Kubernetes Service (AKS) 中的叢集時，應用程式的執行時間會變得很重要。 AKS 透過使用可用性設定組中的多個節點來提供高可用性。 但這些節點無法讓您避免區域失敗。 若要獲得最大的執行時間，請實作某些商務持續性和災害復原功能。

本最佳做法文章所著重的考量重點，在於協助您規劃 AKS 中的商務持續性和災害復原。 您會了解如何：

> [!div class="checklist"]
* [規劃多個區域中的 AKS 叢集](#region-planning)
* [使用 Azure 流量管理員跨多個叢集路由傳送流量](#ingress-traffic)
* [針對容器映像登錄使用異地複寫](#container-registry)
* [規劃跨多個叢集的應用程式狀態](#managing-application-state)
* [跨多個區域複寫儲存體](#storage)

## <a name="plan-for-multi-region-deployment"></a>規劃多重區域部署

**最佳做法指引** - 當您部署多個 AKS 叢集時，請選擇 AKS 的可用區域，並使用配對區域。

AKS 叢集會部署到單一區域。 若要避免區域失敗，請將應用程式部署到跨不同區域的多個 AKS 叢集中。 當您規劃要在哪些區域部署 AKS 叢集時，會適用下列考量：

* [AKS 區域可用性](https://docs.microsoft.com/azure/aks/container-service-quotas#region-availability)
  * 請選擇靠近使用者的區域。 AKS 會持續延伸到新的區域。
* [Azure 配對區域](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)
  * 請針對您的地理區域選擇兩個彼此配對的區域。 這些區域會協同進行平台更新，並優先處理需要進行的復原工作。
* 服務可用性層級 (熱門/熱門、熱門/有點熱門、熱門/冷門)
  * 是否要同時執行這兩個區域，讓其中一個區域「準備好」開始處理流量，或是讓其中一個區域需要時間才能準備好處理流量。

AKS 區域可用性和配對區域要放在一起考慮。 請將 AKS 叢集部署到設計用來一起管理區域災害復原的配對區域。 例如，AKS 有在「美國東部」和「美國西部」提供。 這兩個區域也有配對。 在建立 AKS BC/DR 策略時，就會建議使用這兩個區域。

在部署應用程式時，您還必須將另一個步驟新增至 CI/CD 管線，以便部署到這些 AKS 叢集。 如果您未更新部署管線，則應用程式部署只能部署到其中一個區域和 AKS 叢集。 導向至次要區域的客戶流量不會收到最新的程式碼更新。

## <a name="use-azure-traffic-manager-to-route-traffic"></a>使用 Azure 流量管理員來路由傳送流量

**最佳做法指引** - Azure 流量管理員可以將客戶導向與其最接近的 AKS 叢集和應用程式執行個體。 若要獲得最佳的效能和備援能力，請先透過流量管理員導引所有應用程式流量再進入 AKS 叢集。

使用位於不同區域的多個 AKS 叢集時，您必須控制您要如何將流量導向在每個叢集中執行的應用程式。 [Azure 流量管理員](https://docs.microsoft.com/azure/traffic-manager/)是 DNS 型的流量負載平衡器，可以將網路流量分散到各個區域。 您可以根據叢集回應時間或根據地理位置來路由傳送使用者。

![AKS 與 Azure 流量管理員](media/operator-best-practices-bc-dr/aks-azure-traffic-manager.png)

使用單一 AKS 叢集時，客戶通常會連線至指定應用程式的「服務 IP」或 DNS 名稱。 在多個叢集的部署中，客戶應該連線至流量管理員 DNS 名稱，而這個名稱又指向每個 AKS 叢集上的服務。 這些服務會使用流量管理員端點來進行定義。 每個端點都是「服務的負載平衡器 IP」。 此組態可讓您將網路流量從某個區域中的流量管理員端點，導向不同區域中的端點。

![地理路由與 Azure 流量管理員](media/operator-best-practices-bc-dr/traffic-manager-geographic-routing.png)

流量管理員可用來執行 DNS 查閱，並為使用者傳回最適當的端點。 可以使用巢狀設定檔，並為主要位置指定優先權。 例如，使用者主要會連線至與其最接近的地理區域。 如果該區域發生問題，流量管理員便會將其改為導向至次要區域。 這種方法可確保客戶一律會連線至應用程式執行個體，即使與其最接近的地理區域無法使用也是如此。

如需如何設定這些端點和路由的步驟，請參閱[使用流量管理員來設定地理流量路由方法](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-configure-geographic-routing-method)。

### <a name="layer-7-application-routing-with-azure-front-door"></a>第 7 層應用程式路由與 Azure Front Door

Azure 流量管理員使用 DNS (第 3 層) 來形塑流量。 [Azure Front Door (預覽)](https://docs.microsoft.com/azure/frontdoor/front-door-overview) 會提供 HTTP/HTTPS (第 7 層) 路由選項。 Front Door 的其他功能包括 SSL 終止、自訂網域、Web 應用程式防火牆、URL 重寫和工作階段親和性。

請檢閱應用程式流量的需求，以了解哪一種解決方案最合適。

## <a name="enable-geo-replication-for-container-images"></a>為容器映像啟用異地複寫

**最佳做法指引** - 將容器映像儲存在 Azure Container Registry，並將登錄異地複寫至每個 AKS 區域。

若要在 AKS 中部署及執行應用程式，您必須有辦法儲存和提取容器映像。 Azure Container Registry (ACR) 可與 AKS 整合，以便安全地儲存容器映像或 Helm 圖表。 ACR 可支援讓多重主機異地複寫自動將映像複寫到世界各地的 Azure 區域。 若要改善效能和可用性，請使用 ACR 異地複寫，在您具有 AKS 叢集的每個區域中建立登錄。 然後，每個 AKS 叢集便可從相同區域的本機 ACR 登錄提取容器映像：

![容器映像的 Azure Container Registry 異地複寫](media/operator-best-practices-bc-dr/acr-geo-replication.png)

使用 ACR 異地複寫的好處如下：

* **可以更快地從相同區域提取映像。** 您可從相同 Azure 區域內的高速、低延遲網路連線提取映像。
* **可以更可靠地從相同區域提取映像。** 如果某個區域無法使用，AKS 叢集可從仍可使用的不同 ACR 登錄提取映像。
* **可以用更低成本的方式從相同區域提取映像。** 資料中心之間沒有任何網路輸出費用。

異地複寫是「進階」SKU ACR 登錄的一項功能。 如需如何設定複寫的步驟，請參閱 [Azure Container Registry 異地複寫](https://docs.microsoft.com/azure/container-registry/container-registry-geo-replication)

## <a name="remove-service-state-from-inside-containers"></a>從容器內移除服務狀態

**最佳做法指引** - 盡可能不要將服務狀態儲存在容器內。 請改用支援多重區域複寫的 Azure PaaS 服務。

服務狀態係指服務正常運作所需的記憶體或磁碟資料。 狀態包括服務會讀取及寫入的資料結構和成員變數。 根據服務的架構方式而定，狀態可能還包括儲存在磁碟上的檔案或其他資源。 例如，資料庫要用於儲存資料和交易記錄的檔案。

狀態可進行外部化，也可並存在運作該狀態的程式碼中。 狀態外部化的做法通常是使用資料庫，或是透過網路在不同電腦上或在相同電腦上的流程以外執行的其他資料存放區。

在容器和微服務內執行的程序不保留狀態時，容器和微服務會有最好的復原性。 因為您的應用程式幾乎一律包含某些狀態，所以請使用平台即服務解決方案，例如適用於 MySQL/Postgres 或 Azure SQL 的 Azure 資料庫。

若要了解如何建置可攜性更好的應用程式，請參閱下列指導方針：

* [十二要素應用程式方法](https://12factor.net/)。
* [在多個 Azure 區域中執行 Web 應用程式](https://docs.microsoft.com/azure/architecture/reference-architectures/app-service-web-app/multi-region)

## <a name="create-a-storage-migration-plan"></a>建立儲存體移轉計劃

**最佳做法指引** - 如果您使用 Azure 儲存體，請針對將儲存體從主要區域遷移至備份區域的方法進行準備和測試。

您的應用程式可以為其資料使用 Azure 儲存體。 當應用程式分散到多個位於不同區域的 AKS 叢集時，您必須讓儲存體保持同步。 用來複寫儲存體的兩個常見方式包括下列方法：

* 以應用程式為基礎的非同步複寫
* 以基礎結構為基礎的非同步複寫

### <a name="infrastructure-based-asynchronous-replication"></a>以基礎結構為基礎的非同步複寫

即使在 Pod 刪除之後，您的應用程式仍可能需要永續性儲存體。 在 Kubernetes 中，您可以使用永續性磁碟區來保存資料儲存體。 這些永續性磁碟區會掛接到節點 VM，再對 Pod 公開。 永續性磁碟區會跟隨 Pod，即使 Pod 移到相同叢集內的不同節點也是如此。

根據解決方案所使用的儲存體，複寫策略可能會不同。 常見的儲存體解決方案 (例如 [Gluster](https://docs.gluster.org/en/latest/Administrator%20Guide/Geo%20Replication/)、[CEPH](http://docs.ceph.com/docs/master/cephfs/disaster-recovery/)、[Rook](https://rook.io/docs/rook/master/disaster-recovery.html) 和 [Portworx](https://docs.portworx.com/scheduler/kubernetes/going-production-with-k8s.html#disaster-recovery-with-cloudsnaps)) 都有自己的指引。

主要方法是準備共同的儲存點供應用程式寫入其資料。 然後，此資料會複寫到各個區域，再於本機存取。

![以基礎結構為基礎的非同步複寫](media/operator-best-practices-bc-dr/aks-infra-based-async-repl.png)

如果您使用 Azure 受控磁碟，可用的複寫和 DR 解決方案包含使用下列其中一個方法：

* [Azure 上的 Ark](https://github.com/heptio/ark/blob/master/docs/azure-config.md)
* [Azure Site Recovery](https://azure.microsoft.com/blog/asr-managed-disks-between-azure-regions/)

### <a name="application-based-asynchronous-replication"></a>以應用程式為基礎的非同步複寫

以應用程式為基礎的非同步複寫目前沒有 Kubernetes 原生實作。 由於容器和 Kubernetes 有鬆散結合的本質，所有傳統的應用程式或語言方法應該都會有作用。 主要方法是讓應用程式本身複寫儲存要求，此要求接著會再寫入至每個叢集的基礎資料儲存體。

![以應用程式為基礎的非同步複寫](media/operator-best-practices-bc-dr/aks-app-based-async-repl.png)

## <a name="next-steps"></a>後續步驟

本文著重在 AKS 叢集中的商務持續性和災害復原考量。 如需 AKS 中叢集作業的相關詳細資訊，請參閱下列最佳作法：

* [多租用戶和叢集隔離][aks-best-practices-cluster-isolation]
* [基本的 Kubernetes 排程器功能][aks-best-practices-scheduler]

<!-- INTERNAL LINKS -->
[aks-best-practices-scheduler]: operator-best-practices-scheduler.md
[aks-best-practices-cluster-isolation]: operator-best-practices-cluster-isolation.md
