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
ms.date: 04/25/2019
ms.author: magoedte
ms.openlocfilehash: d6321564672097fbf901d1d33afac9f606fcb63a
ms.sourcegitcommit: bb85a238f7dbe1ef2b1acf1b6d368d2abdc89f10
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/10/2019
ms.locfileid: "65521827"
---
# <a name="azure-monitor-for-containers-overview"></a>適用於容器的 Azure 監視器概觀

適用於容器的 Azure 監視器是一項功能，其設計訴求是要監視部署至 Azure Container Instances 或 Azure Kubernetes Service (AKS) 上所裝載受控 Kubernetes 叢集的容器工作負載效能。 監視容器很重要，尤其在您使用多個應用程式大規模執行生產環境叢集時。

適用於容器的 Azure 監視器可藉由透過計量 API 從 Kubernetes 中取得的控制器、節點與容器來收集記憶體與處理器計量，為您提供效能可見度。 容器記錄也會一併收集。  啟用監視 Kubernetes 叢集之後，度量和記錄檔會自動收集您透過 Log Analytics 代理程式，適用於 Linux 的容器化版本。 計量會寫入計量存放區和記錄檔資料會寫入記錄檔相關聯的儲存與您[Log Analytics](../log-query/log-query-overview.md)工作區。 

![Azure 監視器容器架構](./media/container-insights-overview/azmon-containers-architecture-01.png)
 
## <a name="what-does-azure-monitor-for-containers-provide"></a>適用於容器的 Azure 監視器提供哪些功能？

適用於容器的 azure 監視器提供完整的監視體驗使用不同的 Azure 監視器的功能，讓您了解 Kubernetes 叢集和容器工作負載的健康情況與效能。 使用適用於容器的 Azure 監視器中，您可以：

* 識別正在節點上執行的 AKS 容器，以及其平均的處理器與記憶體使用率。 此知識可協助您識別資源瓶頸。
* 識別容器群組及其裝載於 Azure Container Instances 之容器的處理器和記憶體使用率。  
* 識別容器在控制器或 Pod 中的所在位置。 此知識可協助您檢視控制器或 Pod 的整體效能。
* 檢閱在和支援 Pod 的標準程序無關之主機上執行的工作負載的資源使用率。
* 了解叢集在平均負載和最高負載之下的行為。 此知識可協助您識別所需的容量，並判斷叢集可承受的負載上限。 
* 設定警示，以主動通知您，或當節點或容器上的 CPU 和記憶體使用率超過您的臨界值時，請記錄下來。  

## <a name="how-do-i-access-this-feature"></a>如何存取此功能？
您有兩種方式可用來存取適用於容器的 Azure 監視器：從 Azure 監視器或直接從所選取的 AKS 叢集。 從 Azure 監視器中，您有全域之檢視方塊的所有容器部署，這會監視並不是，讓您跨訂用帳戶和資源群組，請進行搜尋和篩選，然後向下切入至 「 Azure 監視器的容器選取的容器。  否則，您可以直接從選取的 AKS 容器從 AKS 頁面存取的功能。  

![存取適用於容器的 Azure 監視器方法概觀](./media/container-insights-overview/azmon-containers-experience.png)

如果您想要監視和管理 Docker 與 Windows 容器主機以檢視設定、稽核以及資源使用率，請參閱[容器監視解決方案](../../azure-monitor/insights/containers.md)。

## <a name="next-steps"></a>後續步驟
若要開始監視您的 AKS 叢集，請檢閱[如何啟用適用於容器的 Azure 監視器](container-insights-onboard.md)了解需求和可用的方法，若要啟用監視。  
