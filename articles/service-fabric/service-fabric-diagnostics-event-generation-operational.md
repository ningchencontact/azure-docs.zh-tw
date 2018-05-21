---
title: Azure Service Fabric 事件清單 | Microsoft Docs
description: Azure Service Fabric 所提供的事件完整清單可協助監控叢集。
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: reference
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/25/2018
ms.author: dekapur
ms.openlocfilehash: 529df0147d2563c62c4a9578e47184bd98b01761
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/16/2018
---
# <a name="list-of-service-fabric-events"></a>Service Fabric 事件清單 

Service Fabric 會公開一組主要的叢集事件，以 [Service Fabric 事件](service-fabric-diagnostics-events.md)的方式向您通知叢集的狀態。 這些是以 Service Fabric 在您的節點和叢集上執行的動作，或以叢集擁有者/操作者所做的管理決定為基礎。 您可以透過查詢叢集中的 [EventStore](service-fabric-diagnostics-eventstore.md)，或透過操作通道存取這些事件。 在 Windows 電腦中，操作通道也會與 EventLog 連結，因此您可以在事件檢視器中看到 Service Fabric 事件。 

>[!NOTE]
>如需低於 6.2 版叢集的 Service Fabric 事件清單，請參閱下一節。 

以下是平台中所有可用事件的清單，依它們所對應的實體排序。

## <a name="cluster-events"></a>叢集事件

**叢集升級事件**

| EventId | Name | 來源 (工作) | Level | 版本 |
| --- | --- | --- | --- | --- |
| 29627 | ClusterUpgradeStartOperational | CM | 資訊 | 1 |
| 29628 | ClusterUpgradeCompleteOperational | CM | 資訊 | 1 |
| 29629 | ClusterUpgradeRollbackStartOperational | CM | 資訊 | 1 |
| 29630 | ClusterUpgradeRollbackCompleteOperational | CM | 資訊 | 1 |
| 29631 | ClusterUpgradeDomainCompleteOperational | CM | 資訊 | 1 |

**叢集健康情況報表事件**

| EventId | Name | 來源 (工作) | Level | 版本 |
| --- | --- | --- | --- | --- |
| 54428 | ProcessClusterReportOperational | HM | 資訊 | 1 |
| 54437 | ExpiredClusterEventOperational | HM | 資訊 | 1 |

**混沌服務事件**

| EventId | Name | 來源 (工作) | Level | 版本 |
| --- | --- | --- | --- | --- |
| 50021 | ChaosStartedEvent | 可測試性 | 資訊 | 1 |
| 50023 | ChaosStoppedEvent | 可測試性 | 資訊 | 1 |

## <a name="node-events"></a>節點事件

**節點生命週期事件** 

| EventId | Name | 來源 (工作) | Level | 版本 |
| --- | --- | --- | --- | --- |
| 18602 | DeactivateNodeCompletedOperational | FM | 資訊 | 1 |
| 18603 | NodeUpOperational | FM | 資訊 | 1 |
| 18604 | NodeDownOperational | FM | 資訊 | 1 |
| 18605 | NodeAddedOperational | FM | 資訊 | 1 |
| 18606 | NodeRemovedOperational | FM | 資訊 | 1 |
| 18607 | DeactivateNodeStartOperational | FM | 資訊 | 1 |
| 25620 | NodeOpening | FabricNode | 資訊 | 1 |
| 25621 | NodeOpenedSuccess | FabricNode | 資訊 | 1 |
| 25622 | NodeOpenedFailed | FabricNode | 資訊 | 1 |
| 25623 | NodeClosing | FabricNode | 資訊 | 1 |
| 25624 | NodeClosed | FabricNode | 資訊 | 1 |
| 25625 | NodeAborting | FabricNode | 資訊 | 1 |
| 25626 | NodeAborted | FabricNode | 資訊 | 1 |

**節點健康情況報表事件**

| EventId | Name | 來源 (工作) | Level | 版本 |
| --- | --- | --- | --- | --- |
| 54423 | ProcessNodeReportOperational | HM | 資訊 | 1 |
| 54432 | ExpiredNodeEventOperational | HM | 資訊 | 1 |

**混沌節點事件**

| EventId | Name | 來源 (工作) | Level | 版本 |
| --- | --- | --- | --- | --- |
| 50033 | ChaosRestartNodeFaultScheduledEvent | 可測試性 | 資訊 | 1 |
| 50087 | ChaosRestartNodeFaultCompletedEvent | 可測試性 | 資訊 | 1 |

## <a name="application-events"></a>應用程式事件

**應用程式生命週期事件**

| EventId | Name | 來源 (工作) | Level | 版本 |
| --- | --- | --- | --- | --- |
| 29620 | ApplicationCreatedOperational | CM | 資訊 | 1 |
| 29625 | ApplicationDeletedOperational | CM | 資訊 | 1 |
| 23083 | ProcessExitedOperational | 裝載 | 資訊 | 1 |

**應用程式升級事件**

| EventId | Name | 來源 (工作) | Level | 版本 |
| --- | --- | --- | --- | --- |
| 29621 | ApplicationUpgradeStartOperational | CM | 資訊 | 1 |
| 29622 | ApplicationUpgradeCompleteOperational | CM | 資訊 | 1 |
| 29623 | ApplicationUpgradeRollbackStartOperational | CM | 資訊 | 1 |
| 29624 | ApplicationUpgradeRollbackCompleteOperational | CM | 資訊 | 1 |
| 29626 | ApplicationUpgradeDomainCompleteOperational | CM | 資訊 | 1 |

**應用程式健康情況報表事件**

| EventId | Name | 來源 (工作) | Level | 版本 |
| --- | --- | --- | --- | --- |
| 54425 | ProcessApplicationReportOperational | HM | 資訊 | 1 |
| 54426 | ProcessDeployedApplicationReportOperational | HM | 資訊 | 1 |
| 54427 | ProcessDeployedServicePackageReportOperational | HM | 資訊 | 1 |
| 54434 | ExpiredApplicationEventOperational | HM | 資訊 | 1 |
| 54435 | ExpiredDeployedApplicationEventOperational | HM | 資訊 | 1 |
| 54436 | ExpiredDeployedServicePackageEventOperational | HM | 資訊 | 1 |

**混沌應用程式事件**

| EventId | Name | 來源 (工作) | Level | 版本 |
| --- | --- | --- | --- | --- |
| 50053 | ChaosRestartCodePackageFaultScheduledEvent | 可測試性 | 資訊 | 1 |
| 50101 | ChaosRestartCodePackageFaultCompletedEvent | 可測試性 | 資訊 | 1 |

## <a name="service-events"></a>服務事件

**服務生命週期事件**

| EventId | Name | 來源 (工作) | Level | 版本 |
| --- | --- | --- | --- | --- |
| 18602 | ServiceCreatedOperational | FM | 資訊 | 1 |
| 18658 | ServiceDeletedOperational | FM | 資訊 | 1 |

**服務健康情況報表事件**

| EventId | Name | 來源 (工作) | Level | 版本 |
| --- | --- | --- | --- | --- |
| 54424 | ProcessServiceReportOperational | HM | 資訊 | 1 |
| 54433 | ExpiredServiceEventOperational | HM | 資訊 | 1 |

## <a name="partition-events"></a>分割事件

**分割移動事件**

| EventId | Name | 來源 (工作) | Level | 版本 |
| --- | --- | --- | --- | --- |
| 18940 | ReconfigurationCompleted | RA | 資訊 | 1 |

**分割健康情況報表事件**

| EventId | Name | 來源 (工作) | Level | 版本 |
| --- | --- | --- | --- | --- |
| 54422 | ProcessPartitionReportOperational | HM | 資訊 | 1 |
| 54431 | ExpiredPartitionEventOperational | HM | 資訊 | 1 |

**混沌分割事件**

| EventId | Name | 來源 (工作) | Level | 版本 |
| --- | --- | --- | --- | --- |
| 50069 | ChaosMovePrimaryFaultScheduledEvent | 可測試性 | 資訊 | 1 |
| 50077 | ChaosMoveSecondaryFaultScheduledEvent | 可測試性 | 資訊 | 1 |

**分割分析事件**

| EventId | Name | 來源 (工作) | Level | 版本 |
| --- | --- | --- | --- | --- |
| 65003 | PrimaryMoveAnalysisEvent | 可測試性 | 資訊 | 1 |

## <a name="replica-events"></a>複本事件

**複本健康情況報表事件**

| EventId | Name | 來源 (工作) | Level | 版本 |
| --- | --- | --- | --- | --- |
| 54429 | ProcessStatefulReplicaReportOperational | HM | 資訊 | 1 |
| 54430 | ProcessStatelessInstanceReportOperational | HM | 資訊 | 1 |
| 54438 | ExpiredStatefulReplicaEventOperational | HM | 資訊 | 1 |
| 54439 | ExpiredStatelessInstanceEventOperational | HM | 資訊 | 1 |

**混沌複本事件**

| EventId | Name | 來源 (工作) | Level | 版本 |
| --- | --- | --- | --- | --- |
| 50047 | ChaosRestartReplicaFaultScheduledEvent | 可測試性 | 資訊 | 1 |
| 50051 | ChaosRemoveReplicaFaultScheduledEvent | 可測試性 | 資訊 | 1 |
| 50093 | ChaosRemoveReplicaFaultCompletedEvent | 可測試性 | 資訊 | 1 |

## <a name="container-events"></a>容器事件

**容器生命週期事件** 

| EventId | Name | 來源 (工作) | Level | 版本 |
| --- | --- | --- | --- | --- |
| 23074 | ContainerActivatedOperational | 裝載 | 資訊 | 1 |
| 23075 | ContainerDeactivatedOperational | 裝載 | 資訊 | 1 |
| 23082 | ContainerExitedOperational | 裝載 | 資訊 | 1 |

## <a name="other-events"></a>其他事件

**相互關連事件**

| EventId | Name | 來源 (工作) | Level | 版本 |
| --- | --- | --- | --- | --- |
| 65011 | CorrelationOperational | 可測試性 | 資訊 | 1 |

## <a name="events-prior-to-version-62"></a>6.2 版之前的事件

以下是 Service Fabric 6.2 版之前所提供事件的完整清單。

| EventId | Name | 來源 (工作) | 等級 |
| --- | --- | --- | --- |
| 25620 | NodeOpening | FabricNode | 資訊 |
| 25621 | NodeOpenedSuccess | FabricNode | 資訊 |
| 25622 | NodeOpenedFailed | FabricNode | 資訊 |
| 25623 | NodeClosing | FabricNode | 資訊 |
| 25624 | NodeClosed | FabricNode | 資訊 |
| 25625 | NodeAborting | FabricNode | 資訊 |
| 25626 | NodeAborted | FabricNode | 資訊 |
| 29627 | ClusterUpgradeStart | CM | 資訊 |
| 29628 | ClusterUpgradeComplete | CM | 資訊 |
| 29629 | ClusterUpgradeRollback | CM | 資訊 |
| 29630 | ClusterUpgradeRollbackComplete | CM | 資訊 |
| 29631 | ClusterUpgradeDomainComplete | CM | 資訊 |
| 23074 | ContainerActivated | 裝載 | 資訊 |
| 23075 | ContainerDeactivated | 裝載 | 資訊 |
| 29620 | ApplicationCreated | CM | 資訊 |
| 29621 | ApplicationUpgradeStart | CM | 資訊 |
| 29622 | ApplicationUpgradeComplete | CM | 資訊 |
| 29623 | ApplicationUpgradeRollback | CM | 資訊 |
| 29624 | ApplicationUpgradeRollbackComplete | CM | 資訊 |
| 29625 | ApplicationDeleted | CM | 資訊 |
| 29626 | ApplicationUpgradeDomainComplete | CM | 資訊 |
| 18566 | ServiceCreated | FM | 資訊 |
| 18567 | ServiceDeleted | FM | 資訊 |

## <a name="next-steps"></a>後續步驟

* 深入了解 Service Fabric 中整個[平台層級的事件產生](service-fabric-diagnostics-event-generation-infra.md)
* 修改您的 [Azure 診斷](service-fabric-diagnostics-event-aggregation-wad.md)設定以收集更多記錄
* [設定 Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md) 以查看您的作業通道記錄
