---
title: 使用 Log Analytics 監視 Azure Service Fabric 上的容器 | Microsoft Docs
description: 使用 Log Analytics 以監視在 Azure Service Fabric 叢集上執行的容器。
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/1/2017
ms.author: dekapur
ms.openlocfilehash: 7a775b6d23c144c81650bb3608ee6a117475a9ba
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/06/2018
---
# <a name="monitor-containers-with-log-analytics"></a>使用 Log Analytics 監視容器
 
本文章涵蓋為您的叢集設定容器監視所需的步驟。 如需相關的詳細資訊，請參閱[監視 Service Fabric 中的容器](service-fabric-diagnostics-event-analysis-oms.md#monitoring-containers)。 若要查看相關的逐步教學課程，可按照[監視 Service Fabric 上的 Windows 容器](service-fabric-tutorial-monitoring-wincontainers.md)的步驟進行。

## <a name="set-up-the-container-monitoring-solution"></a>設定容器監視解決方案

> [!NOTE]
> 您需要為您的叢集設定 Log Analytics，並在您的節點上部署 OMS 代理程式。 如果尚未完成，請先按照[設定 Log Analytics](service-fabric-diagnostics-oms-setup.md) 以及[將 OMS 代理程式新增至叢集](service-fabric-diagnostics-oms-agent.md)中的步驟進行。

1. 使用 Log Analytics 與 OMS 代理程式設定叢集之後，請部署您的容器。 移至下一步之前，請等待容器部署完成。

2. 在 Azure Marketplace 中，搜尋 [容器監視解決方案]，然後按一下顯示在 [監視 + 管理] 類別下的 [容器監視解決方案] 資源。

    ![新增容器解決方案](./media/service-fabric-diagnostics-event-analysis-oms/containers-solution.png)

3. 將解決方案建立在已經為叢集建立的相同工作區內。 此變更會自動觸發代理程式，以開始收集容器上的 Docker 資料。 大約 15 分鐘左右，應會看到帶有傳入記錄和統計資料的解決方案亮起。

## <a name="next-steps"></a>後續步驟
* 深入了解 Service Fabric 上的容器協調流程 - [Service Fabric 與容器](service-fabric-containers-overview.md)
* 熟悉 Log Analytics 的[記錄搜尋和查詢](../log-analytics/log-analytics-log-searches.md)功能
* 設定 Log Analytics，以設定[自動化警示](../log-analytics/log-analytics-alerts.md)規則來協助偵測與診斷