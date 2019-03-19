---
title: Azure Service Fabric 事件 | Microsoft Docs
description: 了解協助您監視 Azure Service Fabric 叢集的現成 Service Fabric 事件。
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
ms.date: 11/21/2018
ms.author: srrengar
ms.openlocfilehash: c4ce8e01b1dc819453610f68d044cc268e27eed7
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/02/2019
ms.locfileid: "57242746"
---
# <a name="service-fabric-events"></a>Service Fabric 事件 

Service Fabric 平台會撰寫好幾種結構化事件，呈現叢集內發生的重要作業活動。 不論是叢集升級或複本放置決策等，都包括在內。 Service Fabric 公開的每個事件，都與叢集中的以下其中一個實體相對應：
* 叢集
* Application
* 服務
* 資料分割
* 複本 
* 容器

查看平台公開的完整事件清單 - [Service Fabric 事件清單](service-fabric-diagnostics-event-generation-operational.md)。

以下是幾個情節範例，您應該可以在叢集中看到這些情節的事件。 
* 節點生命週期事件：隨著節點上線、下線、相應縮小/放大、重新開機，以及啟動/停用，平台會藉由公開事件來指出發生什麼情況，同時協助您辨別機器本身是否發生任何問題，或是否曾透過 SF 呼叫 API 來修改節點狀態。
* 叢集升級：當叢集升級 (SF 版本或組態變更) 時，您可以看見升級起始、在每個升級網域間展開及完成 (或復原)。 
* 應用程式升級：與叢集升級相似，當升級展開時會產生一組詳盡的事件。 這些事件能用來得知升級的排程、升級目前的狀態，以及事件的整體順序。 它們能用來回顧哪些升級已成功推出，或是否觸發復原。
* 應用程式/服務的部署/刪除：每個應用程式、服務及容器建立或刪除時，都會產生相應的事件，且當相應縮小或相應放大時 (例如，增加複本的數目) 也很有用
* 分割區移動 (重新組態)：每當具狀態分割區經歷重新組態 (複本集發生變更) 時，平台會記錄事件。 如果您正在嘗試了解分割區複本集變更或容錯移轉的頻率，或是追蹤哪個節點在任何時間點執行主要複本，此功能將能派上用場。
* Chaos 事件：使用 Service Fabric 的 [Chaos](service-fabric-controlled-chaos.md) 服務時，每當服務啟動或停止，或是在系統中插入錯誤時，您都可以看到事件。
* 运行状况事件：每次创建了“警告”或“错误”运行状况报告时，或者当实体恢复为“正常”运行状况状态时，或者当运行状况报告过期时，Service Fabric 都会公开运行状况事件。 這些事件特別有助於追蹤實體的歷史健康情況統計資料。 

## <a name="how-to-access-events"></a>如何存取事件

存取 Service Fabric 事件的方法有好幾個：
* 事件會透過標準的管道，例如 ETW/Windows 事件記錄檔記錄，並可藉由支援這些等 Azure 監視記錄檔的任何監視工具。 根據預設，入口網站中建立的叢集已開啟的診斷和 Windows Azure 診斷代理程式將事件傳送至 Azure 資料表儲存體，但您仍然需要整合這與您的 log analytics 資源。 深入了解設定[Azure 診斷代理程式](service-fabric-diagnostics-event-aggregation-wad.md)若要修改您的叢集，以取得更多記錄或效能計數器的診斷組態和[Azure 監視器記錄整合](service-fabric-diagnostics-event-analysis-oms.md)
* EventStore 服務 Rest API，或透過 Service Fabric 用戶端程式庫可讓您直接查詢叢集。 請參閱[查詢 EventStore API 以查詢叢集事件](service-fabric-diagnostics-eventstore-query.md)。

## <a name="next-steps"></a>後續步驟
* 監視叢集的詳細資訊 - [監視叢集和平台](service-fabric-diagnostics-event-generation-infra.md)。
* 深入了解 EventStore 服務 - [EventStore 服務概觀](service-fabric-diagnostics-eventstore.md)
