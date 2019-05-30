---
title: Azure Service Fabric 容量規劃和調整的最佳做法 | Microsoft Docs
description: Service Fabric 叢集和應用程式規劃和調整的最佳做法。
services: service-fabric
documentationcenter: .net
author: peterpogorski
manager: chackdan
editor: ''
ms.assetid: 19ca51e8-69b9-4952-b4b5-4bf04cded217
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/25/2019
ms.author: pepogors
ms.openlocfilehash: 9bddb6552b11dd506ee3e2c1c416c15da11048b7
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/28/2019
ms.locfileid: "66258745"
---
# <a name="capacity-planning-and-scaling"></a>容量規劃和調整

在建立任何 Azure Service Fabric 叢集或調整裝載叢集的計算資源之前，必須做好容量規劃。 如需規劃容量的詳細資訊，請參閱[規劃 Service Fabric 叢集容量](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity)。 進一步的最佳作法指南取得叢集的延展性，請參閱[Service Fabric 延展性考量](https://docs.microsoft.com/azure/architecture/reference-architectures/microservices/service-fabric#scalability-considerations)

除了考慮節點類型和叢集的特性，您應該規劃調整作業花費的時間超過一小時才能完成在生產環境，不論您要新增的 Vm 數目。

## <a name="auto-scaling"></a>自動調整
調整作業應該執行透過 Azure 資源範本部署，因為它是將最佳做法[資源組態即程式碼]( https://docs.microsoft.com/azure/service-fabric/service-fabric-best-practices-infrastructure-as-code)，並使用虛擬機器擴展集自動調整將會導致您建立版本的 Resource Manager 範本不正確定義您的虛擬機器擴展集執行個體計數;增加的風險，未來的部署造成非預期的調整作業，然後在 一般您應該使用自動調整規模，如果：

* 部署已宣告適當容量的 Resource Manager 範本，無法為您的使用案例提供支援。
  * 除了手動調整，您可以設定[持續整合和傳遞管線中使用 Azure 資源群組部署專案的 Azure DevOps 服務](https://docs.microsoft.com/azure/vs-azure-tools-resource-groups-ci-in-vsts)，這通常會觸發邏輯應用程式，運用從查詢的虛擬機器的效能度量[Azure 監視器 REST API](https://docs.microsoft.com/azure/azure-monitor/platform/rest-api-walkthrough)有效的自動調整，根據您想，並可最佳化 Azure Resource Manager 值新增任何計量。
* 您每次只需要以水平方式調整 1 個虛擬機器擴展集節點。
  * 若要一次相應放大 3 個或多個節點，您應該[相應放大 Service Fabric 叢集中新增虛擬機器擴展集](https://docs.microsoft.com/azure service-fabric/virtual-machine-scale-set-scale-node-type-scale-out)，它是最安全的方式相應縮小和放大虛擬機器擴展集以水平方式 1 個節點一次。
* 您的 Service Fabric 叢集具有銀級或更高的可靠性，而您設定了自動調整規則的任何擴展集，則具有銀級或更高的持久性。
  * 自動調整規則的容量 （最小值） 必須等於或大於 5 的虛擬機器執行個體，且必須等於或大於您可靠性層級最小值，為您的主要節點類型。

> [!NOTE]
> Azure Service Fabric 具狀態的 service fabric: / System/InfastructureService/< NODE_TYPE_NAME >，在每個節點型別具有銀級或更高的持久性，這是唯一支援在 Azure 中執行任何您叢集的節點類型上的系統服務上執行.

## <a name="vertical-scaling-considerations"></a>垂直調整考量

[垂直調整](https://docs.microsoft.com/azure/service-fabric/virtual-machine-scale-set-scale-node-type-scale-out)Azure Service Fabric 中的節點類型需要的步驟和考量的數字。 例如: 

* 在調整之前，叢集必須處於良好狀態。 否則您只會進一步在叢集中造成不穩定。
* **銀級耐久性層級或更高**需要裝載具狀態服務的所有 Service Fabric 叢集節點類型。

> [!NOTE]
> 您裝載具狀態的 Service Fabric 系統服務的主要節點類型必須是銀級耐久性層級或更高。 啟用銀級持久性之後，諸如升級、新增或移除節點等叢集作業的速度都會變慢，因為此時系統的最佳化是以資料安全為導向，而不是作業速度。

垂直縮放比例的虛擬機器擴展集是破壞性作業。 相對地，藉由新增使用所需 SKU 的新擴展集來水平調整叢集，並將服務移轉至您所需的 SKU，將可完成安全的垂直調整作業。 變更虛擬機器擴展集資源 SKU 是破壞性作業，因為重新映像您的主機移除所有在本機保存的狀態。

您的叢集會使用 Service Fabric [節點屬性和放置條件約束](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-resource-manager-cluster-description#node-properties-and-placement-constraints)來決定要在何處裝載應用程式服務。 垂直調整您的主要節點類型，當宣告相同的屬性值的`"nodeTypeRef"`，其位在虛擬機器擴展集的 Service Fabric 延伸模組。 Resource Manager 範本的下列程式碼片段顯示您將宣告的屬性，其值與您新佈建而要調整的擴展集相同，僅支援作為叢集的暫時具狀態屬性：

```json
"settings": {
   "nodeTypeRef": ["[parameters('primaryNodetypeName')]"]
}
```

> [!NOTE]
> 請不要讓您的叢集以使用多個相同 `nodeTypeRef` 屬性值的擴展集執行的時間超過完成成功垂直調整作業所需的時間。
> 請一律先在測試環境中驗證作業，再嘗試於生產環境變更。 根據預設，Service Fabric 叢集的系統服務會有的放置條件約束，以目標主要節點型別。

在宣告節點屬性和放置條件約束後，請逐一對各個 VM 執行個體執行下列步驟。 如此，在其他位置建立新複本時，系統服務 (以及您的具狀態服務) 將可在您要移除的 VM 執行個體上正常關閉。

1. 從 PowerShell 使用意圖 'RemoveNode' 執行 `Disable-ServiceFabricNode`，以停用您要移除的節點。 移除具有最高編號的節點類型。 例如，如果您有六個節點叢集，請移除 "MyNodeType_5" 虛擬機器執行個體。
2. 執行 `Get-ServiceFabricNode` 以確保節點已轉換為停用狀態。 如果沒有，請等到節點停用。 每個節點可能各需要數小時的時間。 請等到節點已轉換為停用狀態後，再繼續操作。
3. 將該節點類型的 VM 數目減少一個。 此時將會移除最高的 VM 執行個體。
4. 視需要重複步驟 1 到 3，但是請永遠不要將主要節點類型的執行個體數目相應減少到少於可靠性層級所需的數目。 如需建議的行個體清單，請參閱[規劃 Service Fabric 叢集容量](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity)。

> [!NOTE]
> 支援的案例，來執行垂直調整作業的時機為：我可以移轉我的 Service Fabric 叢集和應用程式從非受控磁碟為受控磁碟而不需要應用程式停機。 藉由佈建新的虛擬機器擴展集使用受控磁碟，並執行含有放置約束限制應用程式升級為目標佈建容量，Service Fabric 叢集可以排程您的升級網域的推出而不需要應用程式停機的佈建的叢集節點容量的工作負載。 [Azure 負載平衡器基本 SKU](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview#skus)後端集區端點也可以在一個可用性設定組或虛擬機器擴展集虛擬機器。 這表示您無法使用基本 SKU 負載平衡器，如果您在擴展集之間移動您的 Service Fabric 系統應用程式，而不會造成暫時無法存取您的 Service fabric 叢集管理端點，即使叢集和其應用程式仍在執行;通常使用者佈建的標準 SKU 負載平衡器中，執行基本 SKU LB 與標準 SKU LB 資源之間的虛擬 IP 位址 (VIP) 交換時，以減少任何未來大約 30 秒會以無法存取所需的 VIP 交換。

## <a name="horizontal-scaling"></a>水平調整

Service Fabric 的水平相應縮小可[手動](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-scale-up-down)或是[以程式設計方式](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-programmatic-scaling)完成。

> [!NOTE]
> 調整具有銀級或金級持久性的節點類型時，調整速度將會變慢。

### <a name="scaling-out"></a>相應放大

藉由增加特定虛擬機器擴展集的執行個體計數，以相應放大 Service Fabric 叢集。 您可以使用 AzureClient 和所需擴展集的識別碼以程式設計方式進行相應放大，以增加容量。

```c#
var scaleSet = AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId);
var newCapacity = (int)Math.Min(MaximumNodeCount, scaleSet.Capacity + 1);
scaleSet.Update().WithCapacity(newCapacity).Apply(); 
```

若要手動向外延展，更新 所需的 SKU 屬性中的容量[虛擬機器擴展集](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile)資源。
```json
"sku": {
    "name": "[parameters('vmNodeType0Size')]",
    "capacity": "[parameters('nt0InstanceCount')]",
    "tier": "Standard"
}
```

### <a name="scaling-in"></a>相應縮小

相應縮小所需考量的因素會比相應放大多一些。例如: 

* Service Fabric 系統服務會在叢集內的主要節點類型中執行。 切勿關閉該節點類型的執行個體，或將其數目相應減少到低於可靠性層級保證所需的執行個體數目。 
* 針對具狀態服務，您需要一些始終啟動的節點來維持可用性，以及維持服務的狀態。 您至少需要與分割區/服務的目標複本集計數相等的節點數目。

若要手動相應縮小，請遵循下列步驟︰

1. 從 PowerShell 使用意圖 'RemoveNode' 執行 `Disable-ServiceFabricNode`，以停用您要移除的節點。 移除具有最高編號的節點類型。 例如，如果您有六個節點叢集，請移除 "MyNodeType_5" 虛擬機器執行個體。
2. 執行 `Get-ServiceFabricNode` 以確保節點已轉換為停用狀態。 如果沒有，請等到節點停用。 每個節點可能各需要數小時的時間。 請等到節點已轉換為停用狀態後，再繼續操作。
3. 將該節點類型的 VM 數目減少一個。 此時將會移除最高的 VM 執行個體。
4. 視需要重複步驟 1 到 3，但是請永遠不要將主要節點類型的執行個體數目相應減少到少於可靠性層級所需的數目。 如需建議的行個體清單，請參閱[規劃 Service Fabric 叢集容量](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity)。

若要手動調整，更新所需的 SKU 屬性中的容量[虛擬機器擴展集](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile)資源。

```json
"sku": {
    "name": "[parameters('vmNodeType0Size')]",
    "capacity": "[parameters('nt0InstanceCount')]",
    "tier": "Standard"
}
```

1. 重複步驟 1 到 3，直到佈建了您需要的容量為止。 切勿將主要節點類型的執行個體數目相應減少到低於可靠性層級保證所需的數目。 如需關於可靠性層級及其所需執行個體數目的詳細資訊，請參閱[規劃 Service Fabric 叢集容量](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity)。

您必須做好節點的關機準備，才能以程式設計方式進行相應縮小。 準備工作包括尋找要移除的節點 (最高的執行個體節點) 並加以停用。 例如: 

```c#
using (var client = new FabricClient())
{
    var mostRecentLiveNode = (await client.QueryManager.GetNodeListAsync())
        .Where(n => n.NodeType.Equals(NodeTypeToScale, StringComparison.OrdinalIgnoreCase))
        .Where(n => n.NodeStatus == System.Fabric.Query.NodeStatus.Up)
        .OrderByDescending(n =>
        {
            var instanceIdIndex = n.NodeName.LastIndexOf("_");
            var instanceIdString = n.NodeName.Substring(instanceIdIndex + 1);
            return int.Parse(instanceIdString);
        })
        .FirstOrDefault();
```

識別要移除的節點後，請以您在上述程式碼中使用的相同 `FabricClient` 執行個體 (在此案例中為 `client`) 和節點執行個體名稱 (在此案例中為 `instanceIdString`) 將節點停用和移除：

```c#
var scaleSet = AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId);

// Remove the node from the Service Fabric cluster
ServiceEventSource.Current.ServiceMessage(Context, $"Disabling node {mostRecentLiveNode.NodeName}");
await client.ClusterManager.DeactivateNodeAsync(mostRecentLiveNode.NodeName, NodeDeactivationIntent.RemoveNode);

// Wait (up to a timeout) for the node to gracefully shutdown
var timeout = TimeSpan.FromMinutes(5);
var waitStart = DateTime.Now;
while ((mostRecentLiveNode.NodeStatus == System.Fabric.Query.NodeStatus.Up || mostRecentLiveNode.NodeStatus == System.Fabric.Query.NodeStatus.Disabling) &&
        DateTime.Now - waitStart < timeout)
{
    mostRecentLiveNode = (await client.QueryManager.GetNodeListAsync()).FirstOrDefault(n => n.NodeName == mostRecentLiveNode.NodeName);
    await Task.Delay(10 * 1000);
}

// Decrement VMSS capacity
var newCapacity = (int)Math.Max(MinimumNodeCount, scaleSet.Capacity - 1); // Check min count 

scaleSet.Update().WithCapacity(newCapacity).Apply();
```

> [!NOTE]
> 當您相應減少叢集就會看到顯示處於狀況不良的狀態，Service Fabric Explorer 中已移除的節點 /VM 執行個體。 如需此行為的說明，請參閱 < [Service Fabric Explorer 中，您可看到的行為](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-scale-up-down#behaviors-you-may-observe-in-service-fabric-explorer)。 您可以：
> * 呼叫[Remove-servicefabricnodestate cmd](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricnodestate?view=azureservicefabricps)以適當的節點名稱。
> * 部署[service fabric 自動調整規模的協助程式應用程式](https://github.com/Azure/service-fabric-autoscale-helper/)以確保節點下的 調整叢集上會清除從 Service Fabric Explorer。

## <a name="reliability-levels"></a>可靠性層級

[可靠性層級](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity)是您的 Service Fabric 叢集資源的屬性，因此無法設定個別的節點類型的方式。 它會控制叢集系統服務的複寫因子，是屬於叢集資源層級的設定。 可靠性層級將決定您的主要節點類型必須具備的節點數目下限。 可靠性層級可以採用以下的值：

* 白金級 - 執行包含七個目標複本集和九個種子節點的系統服務。
* 金級 - 執行包含七個目標複本集和七個種子節點的系統服務。
* 銀級 - 執行包含五個目標複本集和五個種子節點的系統服務。
* 銅級 - 執行包含三個目標複本集和三個種子節點的系統服務。

建議的最低可靠性層級為銀級。

可靠性層級設定於 [Microsoft.ServiceFabric/clusters resource](https://docs.microsoft.com/azure/templates/microsoft.servicefabric/2018-02-01/clusters) 的屬性區段中，如下所示：

```json
"properties":{
    "reliabilityLevel": "Silver"
}
```

## <a name="durability-levels"></a>持久性層級

> [!WARNING]
> 執行 Bronze 持久性的節點類型「沒有權限」  。 這表示不會停止或延遲對您的無狀態工作負載造成影響的基礎結構作業 (可能會影響工作負載)。 銅級持久性僅適用於執行無狀態工作負載的節點類型。 對於生產工作負載，請執行銀級或更高層級，以確保狀態的一致性。 請根據[容量規劃文件](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity)中的指導方針選擇正確的可靠性。

持久性層級必須設定於兩個資源中。 擴充功能設定檔[虛擬機器擴展集資源](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile):

```json
"extensionProfile": {
    "extensions":          {
        "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
        "properties": {
            "settings": {
                "durabilityLevel": "Bronze"
            }
        }
    }
}
```

以及 [Microsoft.ServiceFabric/clusters 資源](https://docs.microsoft.com/azure/templates/microsoft.servicefabric/2018-02-01/clusters)中的 `nodeTypes` 下 

```json
"nodeTypes": [
    {
        "name": "[variables('vmNodeType0Name')]",
        "durabilityLevel": "Bronze"
    }
]
```

## <a name="next-steps"></a>後續步驟

* 在 VM 或執行 Windows Server 的電腦上建立叢集：[適用於 Windows Server 的 Service Fabric 叢集建立](service-fabric-cluster-creation-for-windows-server.md)
* 在 VM 或執行 Linux 的電腦上建立叢集：[建立 Linux 叢集](service-fabric-cluster-creation-via-portal.md)
* 了解 [Service Fabric 支援選項](service-fabric-support.md)

[Image1]: ./media/service-fabric-best-practices/generate-common-name-cert-portal.png
