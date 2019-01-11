---
title: 如何在 Azure Service Fabric 中移除節點類型 | Microsoft Docs
description: 了解如何在 Azure Service Fabric 中移除節點類型
services: service-fabric
documentationcenter: .net
author: v-steg
manager: JeanPaul.Connick
editor: vturecek
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/26/2018
ms.author: v-steg
ms.openlocfilehash: 3704a356763b16a30285baee1aabffdd3aa3f8aa
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/02/2019
ms.locfileid: "53981462"
---
# <a name="remove-a-service-fabric-node-type"></a>移除 Service Fabric 節點類型

使用 [Remove-AzureRmServiceFabricNodeType](https://docs.microsoft.com/powershell/module/azurerm.servicefabric/remove-azurermservicefabricnodetype) 移除Service Fabric 節點類型。

呼叫 Remove-AzureRmServiceFabricNodeType 時，會發生兩個作業：
1.  刪除節點類型後的虛擬機器擴展集 (VMSS)。
2.  針對該節點類型內的所有節點，會從系統中移除該節點的整體狀態。 如果該節點上有服務，則服務會先移出至另一個節點。 如果叢集管理員找不到複本/服務的節點，則作業會延遲/封鎖。

> [!NOTE]
> 不建議經常使用 Remove-AzureRmServiceFabricNodeType 從生產環境叢集移除節點類型。 在此情況中，這是非常危險的命令，因為它基本上會刪除節點類型後的虛擬機器擴展集資源。 當您呼叫 Remove-AzureRmServiceFabricNodeType，系統無法知道您是否想安全地移除。 

## <a name="durability-characteristics"></a>持久性特性
使用 Remove-AzureRmServiceFabricNodeType 時，安全性會優於速度。 建議使用銀級或金級[持久性特性](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster)，因為：
- 銅級不提供關於儲存狀態資訊的任何保證。
- 銀級和金級持久性可以攔截對 VMSS 的任何變更。
- 金級也可提供您對 VMSS 下 Azure 更新的控制。

Service Fabric 會「協調」基礎結構變更和更新，如此資料就不會遺失。 不過，當您移除具有銅級持久性的節點時，您可能會遺失狀態資訊。 如果您要移除主要節點類型，且您的應用程式是無狀態的，則可接受銅級。 當您在生產環境中執行具狀態工作負載時，最低設定應該是銀級。 同樣地，針對生產環境案例，主要節點類型應該一律為銀級或金級。

### <a name="more-about-bronze-durability"></a>深入了解銅級持久性

當移除銅級的節點類型時，該節點類型中的所有節點會立即停機。 Service Fabric 不會攔截任何銅級節點 VMSS 更新，因此所有的 VM 會立即停機。 如果您有具狀態的任何項目在這些節點上，資料會遺失。 現在，即使您是無狀態的，Service Fabric 中的所有節點都會參與通道，因此整個鄰近的資料都會遺失，這可能會影響到叢集本身。

不同於移除單一節點，因為在理論上，您可以一次移除一個節點，等到複本和服務移動完成、等到系統穩定，然後才移除另一個節點，以此類推。  不過，如果您同時一次移除數個節點，您的叢集就可能停機 (因為使用銅級持久性的 Service Fabric 不會攔截任何 VMSS 更新)。

## <a name="recommended-node-type-removal-process"></a>建議的節點類型移除程序

移除節點類型最安全且快速的方式：
1.  如果您使用銅級持久性，或不想要系統移動包含狀態資訊 (其在節點類型刪除過程中可能會遺失) 的應用程式，請先從會受節點類型移除影響的節點上清空具狀態資料。
2.  在每個您要移除的節點上執行 [Remove-ServiceFabricNodeState](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricnodestate?view=azureservicefabricps)。
3.  針對將受節點類型移除影響的 VM 執行 [Remove-AzureRmVmss](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#remove-vms-from-a-scale-set)。
4. 執行 [Remove-AzureRmServiceFabricNodeType](https://docs.microsoft.com/powershell/module/azurerm.servicefabric/remove-azurermservicefabricnodetype) 移除節點類型。

## <a name="next-steps"></a>後續步驟
- 深入了解叢集[持久性特性](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster)。
- 深入了解[節點類型和虛擬機器擴展集](service-fabric-cluster-nodetypes.md)。
- 深入了解[調整 Service Fabric 叢集](service-fabric-cluster-scaling.md)。