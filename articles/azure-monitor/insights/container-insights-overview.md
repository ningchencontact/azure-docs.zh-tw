---
title: 適用於容器的 Azure 監視器概觀 | Microsoft Docs
description: 本文說明適用於容器的 Azure 監視器，其會監視 AKS 容器深入解析解決方案，以及它藉由監視您 AKS 叢集和 Azure 中容器執行個體的健康情況來提供的值。
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/12/2019
ms.author: magoedte
ms.openlocfilehash: 7d8fb7bc0e837ba78d86a39524d869c9bb01895d
ms.sourcegitcommit: 10251d2a134c37c00f0ec10e0da4a3dffa436fb3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/13/2019
ms.locfileid: "67867572"
---
# <a name="azure-monitor-for-containers-overview"></a>適用於容器的 Azure 監視器概觀

適用於容器的 Azure 監視器是一項功能，其設計訴求是要監視部署至 Azure Container Instances 或 Azure Kubernetes Service (AKS) 上所裝載受控 Kubernetes 叢集的容器工作負載效能。 監視容器很重要，尤其在您使用多個應用程式大規模執行生產環境叢集時。

適用於容器的 Azure 監視器可藉由透過計量 API 從 Kubernetes 中取得的控制器、節點與容器來收集記憶體與處理器計量，為您提供效能可見度。 容器記錄也會一併收集。  當您從 Kubernetes 叢集啟用監視之後, 系統會透過適用于 Linux 的 Log Analytics 代理程式容器化版本自動收集計量和記錄。 計量會寫入計量存放區, 並將記錄資料寫入與您的[Log Analytics](../log-query/log-query-overview.md)工作區相關聯的記錄存放區。 

![容器的 Azure 監視器架構](./media/container-insights-overview/azmon-containers-architecture-01.png)
 
## <a name="what-does-azure-monitor-for-containers-provide"></a>適用於容器的 Azure 監視器提供哪些功能？

適用于容器的 Azure 監視器可使用 Azure 監視器的不同功能提供完整的監視體驗, 讓您瞭解 Kubernetes 叢集和容器工作負載的效能和健康情況。 使用適用于容器的 Azure 監視器, 您可以:

* 識別正在節點上執行的 AKS 容器，以及其平均的處理器與記憶體使用率。 此知識可協助您識別資源瓶頸。
* 識別容器群組及其裝載於 Azure Container Instances 之容器的處理器和記憶體使用率。  
* 識別容器在控制器或 Pod 中的所在位置。 此知識可協助您檢視控制器或 Pod 的整體效能。
* 檢閱在和支援 Pod 的標準程序無關之主機上執行的工作負載的資源使用率。
* 了解叢集在平均負載和最高負載之下的行為。 此知識可協助您識別所需的容量，並判斷叢集可承受的負載上限。 
* 設定警示, 以主動通知您, 或在節點或容器上的 CPU 和記憶體使用率超過閾值時加以記錄。
* 與[Prometheus](https://prometheus.io/docs/introduction/overview/)整合, 以查看它從節點和 Kubernetes 收集的應用程式和工作負載計量使用[查詢](container-insights-log-search.md)來建立自訂警示、儀表板, 以及詳細的執行詳細分析。

>[!NOTE]
>目前的 Prometheus 支援是公開預覽中的一項功能。
>

## <a name="how-do-i-access-this-feature"></a>如何存取此功能？
您有兩種方式可用來存取適用於容器的 Azure 監視器：從 Azure 監視器或直接從所選取的 AKS 叢集。 從 Azure 監視器中, 您會看到所有已部署的容器的全域觀點, 這些都是受監視的, 而不是, 可讓您在訂用帳戶和資源群組之間進行搜尋和篩選, 然後向下切入至容器的 Azure 監視器選取的容器。  否則, 您可以直接從 [AKS] 頁面中選取的 AKS 容器存取該功能。  

![存取適用於容器的 Azure 監視器方法概觀](./media/container-insights-overview/azmon-containers-experience.png)

如果您想要監視和管理 Docker 與 Windows 容器主機以檢視設定、稽核以及資源使用率，請參閱[容器監視解決方案](../../azure-monitor/insights/containers.md)。

## <a name="next-steps"></a>後續步驟
若要開始監視您的 AKS 叢集, 請參閱[如何啟用容器的 Azure 監視器](container-insights-onboard.md), 以瞭解啟用監視的需求和可用方法。  
