---
title: 在 Azure Service Fabric 中指定計量和放置設定 | Microsoft Docs
description: 藉由指定計量、放置條件約束及其他放置原則，了解如何描述 Service Fabric 服務。
services: service-fabric
documentationcenter: .net
author: masnider
manager: chackdan
editor: ''
ms.assetid: 16e135c1-a00a-4c6f-9302-6651a090571a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 21fcac62c9335652d0c682a6ac889be82e649464
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/29/2019
ms.locfileid: "58661542"
---
# <a name="configuring-cluster-resource-manager-settings-for-service-fabric-services"></a>設定 Service Fabric 服務的叢集資源管理員設定
Service Fabric 群集资源管理器允许精细地控制用于管控每个独立命名服务的规则。 每個具名服務都可以指定規則來規定它在叢集中的配置方式。 每個具名服務還可以定義一組它想要報告的計量，包括它們對該服務的重要程度。 設定服務可細分成三個不同的工作︰

1. 設定放置條件約束
2. 設定計量
3. 設定進階放置原則及其他規則 (較不常見)

## <a name="placement-constraints"></a>放置约束
放置约束可用来控制服务实际可在群集中的哪些节点上运行。 通常特定的具名服務執行個體或某個指定類型的所有服務會受到限制，只能在特定類型的節點上執行。 放置條件約束是可擴充的。 您可以依據節點類型定義任何屬性集，然後在建立服務時為它們選取條件約束。 您也可以變更執行中的服務放置條件約束。 這可讓您回應叢集中的變更或服務的需求。 指定節點的屬性也可以在叢集中動態更新。 可以在 [這篇文章](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints)

## <a name="metrics"></a>度量
計量是指定的具名服務所需的一組資源。 服務的計量組態包括該服務的每個具狀態複本或無狀態執行個體對該資源的預設耗用量。 計量也包含一個權數，用來指出平衡該計量對服務來說有多重要 (以因應需要取捨的情況)。

## <a name="advanced-placement-rules"></a>進階放置規則
在較少見的情況中，有其他類型的配置規則可使用。 部分範例如下：
- 對於依地理位置的分散式叢集有助益的條件約束
- 特定應用程式架構

其他放置規則是透過「相互關聯」或「原則」來設定。

## <a name="next-steps"></a>後續步驟
- 度量是 Service Fabric 叢集資源管理員管理叢集中的耗用量和容量的方式。 若要深入了解計量及其設定方式，請查看[這篇文章](service-fabric-cluster-resource-manager-metrics.md)
- 親和性是您可以針對服務設定的一種模式。 它並不常見，但如果您需要，可以參閱 [這裡](service-fabric-cluster-resource-manager-advanced-placement-rules-affinity.md)
- 有許多不同的放置規則可以在您的服務上設定以處理其他案例。 您可以在 [這裡](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md)
- 从头开始并[获取 Service Fabric 群集 Resource Manager 简介](service-fabric-cluster-resource-manager-introduction.md)
- 若要了解群集 Resource Manager 如何管理和均衡群集中的负载，请查看有关[均衡负载](service-fabric-cluster-resource-manager-balancing.md)的文章
- 叢集資源管理員有許多描述叢集的選項。 若要详细了解这些选项，请查看这篇[描述 Service Fabric 群集](service-fabric-cluster-resource-manager-cluster-description.md)的文章
