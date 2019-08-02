---
title: 在 Azure Service Fabric 中移除節點類型 | Microsoft Docs
description: 學習如何從在 Azure 中執行的 Service Fabric 叢集移除節點類型。
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chakdan
editor: vturecek
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/14/2019
ms.author: atsenthi
ms.openlocfilehash: 44f25adf4168f4339a31e9270c2b23a8466a8889
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/29/2019
ms.locfileid: "68599484"
---
# <a name="remove-a-service-fabric-node-type"></a>移除 Service Fabric 節點類型
此文章說明如何透過將現有的節點類型從叢集移除，來調整 Azure Service Fabric 叢集的規模。 Service Fabric 叢集是一組由網路連接的虛擬或實體機器，可用來將您的微服務部署到其中並進行管理。 屬於叢集一部分的機器或 VM 都稱為節點。 虛擬機器擴展集是一個 Azure 計算資源，可以用來將一組虛擬機器當做一個集合加以部署和管理。 在 Azure 叢集中定義的每個節點類型，會[設定為不同的擴展集](service-fabric-cluster-nodetypes.md)。 隨後，您即可個別管理每個節點類型。 建立 Service Fabric 叢集之後，您可以透過移除節點類型 (虛擬機器擴展集) 與其所有節點，來水平調整叢集規模。  您可以隨時調整叢集，即使正在叢集上執行工作負載，也是如此。  在叢集進行調整時，您的應用程式也會自動調整。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

請使用[remove-azservicefabricnodetype](https://docs.microsoft.com/powershell/module/az.servicefabric/remove-azservicefabricnodetype)移除 Service Fabric 節點類型。

呼叫 Remove-azservicefabricnodetype 時所發生的三項作業如下:
1.  刪除節點類型後的虛擬機器擴展集。
2.  從叢集移除節點類型。
3.  針對該節點類型內的所有節點，從系統移除該節點的整體狀態。 如果該節點上有服務，則服務會先移出至另一個節點。 如果叢集管理員找不到複本/服務的節點，則作業會延遲/封鎖。

> [!WARNING]
> 不建議經常使用 Remove-AzServiceFabricNodeType 從生產環境叢集移除節點類型。 它是非常危險的命令，因為它會刪除節點類型後的虛擬機器擴展集資源。 

## <a name="durability-characteristics"></a>持久性特性
使用 Remove-azservicefabricnodetype 時, 安全性的優先順序高於速度。 節點類型必須是銀級或金級[持久性層級](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster)，因為：
- 銅級不提供關於儲存狀態資訊的任何保證。
- 銀級和金級持久性可以攔截對擴展集的任何變更。
- 金級也可提供您對擴展集下 Azure 更新的控制。

Service Fabric 會「協調」基礎結構變更和更新，如此資料就不會遺失。 不過，當您移除具有銅級持久性的節點時，您可能會遺失狀態資訊。 如果您要移除主要節點類型，且您的應用程式是無狀態的，則可接受銅級。 當您在生產環境中執行具狀態工作負載時，最低設定應該是銀級。 同樣地，針對生產環境案例，主要節點類型應該一律為銀級或金級。

### <a name="more-about-bronze-durability"></a>深入了解銅級持久性

當移除銅級的節點類型時，該節點類型中的所有節點會立即停機。 Service Fabric 不會攔截任何銅級節點擴展集更新，因此所有的 VM 會立即停機。 如果您有具狀態的任何項目在這些節點上，資料會遺失。 現在，即使您是無狀態的，Service Fabric 中的所有節點都會參與通道，因此整個鄰近的資料都會遺失，這可能會造成叢集本身不穩定。

## <a name="recommended-node-type-removal-process"></a>建議的節點類型移除程序

若要移除節點類型，請執行 [Remove-AzServiceFabricNodeType](/powershell/module/az.servicefabric/remove-azservicefabricnodetype) Cmdlet。  這個 Cmdlet 需要一些時間才能完成。  一旦所有 Vm 都消失 (以「向下」表示), fabric:/System/InfrastructureService/[nodetype name] 就會顯示錯誤狀態。

```powershell
$groupname = "mynodetype"
$nodetype = "nt2vm"
$clustername = "mytestcluster"

Remove-AzServiceFabricNodeType -Name $clustername  -NodeType $nodetype -ResourceGroupName $groupname

Connect-ServiceFabricCluster -ConnectionEndpoint mytestcluster.eastus.cloudapp.azure.com:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint <thumbprint> `
          -FindType FindByThumbprint -FindValue <thumbprint> `
          -StoreLocation CurrentUser -StoreName My
```

然後, 您可以更新叢集資源來移除節點類型。 您可以使用 ARM 範本部署, 或透過[Azure resource manager](https://resources.azure.com)來編輯叢集資源。 這會啟動叢集升級, 這會移除處於錯誤狀態的 fabric:/System/InfrastructureService/[nodetype name] 服務。

您仍然會在 Service Fabric Explorer 中看到節點處於「關閉」狀態。 在每個您要移除的節點上執行 [Remove-ServiceFabricNodeState](/powershell/module/servicefabric/remove-servicefabricnodestate?view=azureservicefabricps)。


```powershell
$nodes = Get-ServiceFabricNode | Where-Object {$_.NodeType -eq $nodetype} | Sort-Object { $_.NodeName.Substring($_.NodeName.LastIndexOf('_') + 1) } -Descending

Foreach($node in $nodes)
{
    Remove-ServiceFabricNodeState -NodeName $node.NodeName -TimeoutSec 300 -Force 
}
```

## <a name="next-steps"></a>後續步驟
- 深入了解叢集[持久性特性](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster)。
- 深入了解[節點類型和虛擬機器擴展集](service-fabric-cluster-nodetypes.md)。
- 深入了解[調整 Service Fabric 叢集](service-fabric-cluster-scaling.md)。
