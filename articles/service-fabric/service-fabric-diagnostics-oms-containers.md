---
title: 監視 Azure Service Fabric 上使用 Azure 監視器記錄的容器 |Microsoft Docs
description: 使用 Azure 監視器記錄來監視 Azure Service Fabric 叢集上執行的容器。
services: service-fabric
documentationcenter: .net
author: srrengar
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/25/2019
ms.author: srrengar
ms.openlocfilehash: d5840db718191c9b67a8b28a2efccd55146ae510
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/02/2019
ms.locfileid: "57246928"
---
# <a name="monitor-containers-with-azure-monitor-logs"></a>使用 Azure 監視器記錄檔監視容器
 
本文章涵蓋設定 Azure 監視器的記錄檔容器監視解決方案，以便查看容器事件所需的步驟。 若要設定您的叢集來收集容器事件，請參閱此[逐步教學課程](service-fabric-tutorial-monitoring-wincontainers.md)。 

[!INCLUDE [log-analytics-agent-note.md](../../includes/log-analytics-agent-note.md)]

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="set-up-the-container-monitoring-solution"></a>設定容器監視解決方案

> [!NOTE]
> 您要讓 「 Azure 監視器記錄檔設定為您的叢集，以及在節點上部署 Log Analytics 代理程式。 如果您沒有這麼做，請依照下列中的步驟[設定 Azure 監視器記錄檔](service-fabric-diagnostics-oms-setup.md)並[新增到叢集的 Log Analytics 代理程式](service-fabric-diagnostics-oms-agent.md)第一次。

1. 一旦您的叢集設定 Log Analytics 代理程式與 Azure 監視器記錄檔，部署您的容器。 移至下一步之前，請等待容器部署完成。

2. 在 Azure Marketplace 中，搜尋 [容器監視解決方案]，然後按一下顯示在 [監視 + 管理] 類別下的 [容器監視解決方案] 資源。

    ![新增容器解決方案](./media/service-fabric-diagnostics-event-analysis-oms/containers-solution.png)

3. 將解決方案建立在已經為叢集建立的相同工作區內。 此變更會自動觸發代理程式，以開始收集容器上的 Docker 資料。 大約 15 分鐘左右，您應該會看到帶有傳入記錄和統計資料的解決方案亮起，如以下圖像所示。

    ![基本 Log Analytics 儀表板](./media/service-fabric-diagnostics-event-analysis-oms/oms-containers-dashboard.png)

代理程式可讓數個容器特定記錄檔，可以在 Azure 監視器記錄檔中查詢，或用來視覺化效能指標的集合。 所收集的記錄類型有：

* ContainerInventory︰顯示容器位置、名稱和映像的相關資訊
* ContainerImageInventory︰已部署映像相關資訊，包括 ID 或大小
* ContainerLog︰特定的錯誤記錄檔、 docker 記錄檔 (stdout 等) 及其他項目
* ContainerServiceLog：已執行的 docker 精露命令
* 效能︰包括容器 CPU、記憶體、網路流量、磁碟 I/O，以及主機電腦的自訂計量



## <a name="next-steps"></a>後續步驟
* 深入了解[Azure 監視器記錄的容器解決方案](../azure-monitor/insights/containers.md)。
* 深入了解 Service Fabric 上的容器協調流程 - [Service Fabric 與容器](service-fabric-containers-overview.md)
* 熟悉[記錄搜尋和查詢](../log-analytics/log-analytics-log-searches.md)作為 Azure 監視器記錄的一部分提供的功能
* 設定 Azure 監視器記錄檔來設定[自動化警示](../log-analytics/log-analytics-alerts.md)規則，以協助偵測與診斷