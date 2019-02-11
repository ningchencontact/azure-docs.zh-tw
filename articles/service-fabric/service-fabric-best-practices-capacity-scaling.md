---
title: Azure Service Fabric 容量規劃和調整的最佳做法 | Microsoft Docs
description: Service Fabric 叢集和應用程式規劃和調整的最佳做法。
services: service-fabric
documentationcenter: .net
author: peterpogorski
manager: jeanpaul.connock
editor: ''
ms.assetid: 19ca51e8-69b9-4952-b4b5-4bf04cded217
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/23/2019
ms.author: pepogors
ms.openlocfilehash: d6f2ca53829642009adbc50061966c5a7e924f7e
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/30/2019
ms.locfileid: "55240398"
---
# <a name="capacity-planning-and-scaling"></a>容量規劃和調整

在建立任何 Azure Service Fabric 叢集或調整裝載叢集的計算資源之前，必須做好容量規劃。 如需規劃容量的詳細資訊，請參閱[規劃 Service Fabric 叢集容量](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity)。 除了考慮 Nodetype 和叢集的特性以外，還必須規劃在生產環境中需要一小時以上才能完成 (無論您新增多少 VM) 的調整作業。

## <a name="auto-scaling"></a>自動調整
調整作業應透過 Azure 資源範本部署來執行，因為最佳做法是[將資源組態視為程式碼]( https://docs.microsoft.com/azure/service-fabric/service-fabric-best-practices-infrastructure-as-code)，且使用虛擬機器擴展集自動調整將會導致已建立版本的 Resource Manager 範本不正確地定義您的虛擬機器擴展集執行個體計數，而增加未來的部署導致非預期調整作業的風險。一般而言，在下列情況下，您應使用自動調整：

* 部署已宣告適當容量的 Resource Manager 範本，無法為您的使用案例提供支援。
  * 除了手動調整以外，您也可以設定[在 Azure DevOps 服務中使用 Azure 資源群組部署專案的持續整合和傳遞管線]( https://docs.microsoft.com/azure/vs-azure-tools-resource-groups-ci-in-vsts)，這通常會由邏輯應用程式觸發，而該應用程式會使用從 [Azure 監視器 REST API](https://docs.microsoft.com/azure/azure-monitor/platform/rest-api-walkthrough) 查詢的虛擬機器效能計量；這將可根據您想要的任何計量有效地自動調整，並且讓 Azure Resource Manager 發揮最大的附加價值。
* 您每次只需要以水平方式調整 1 個虛擬機器擴展集節點。
  * 若要同時相應放大 3 個或更多節點，您應[透過新增虛擬機器擴展集將 Service Fabric 叢集相應放大](https://docs.microsoft.com/azure/service-fabric/virtual-machine-scale-set-scale-node-type-scale-out)，但最安全的方式是逐一以水平方式相應縮小和放大虛擬機器擴展集的 1 個節點。
* 您的 Service Fabric 叢集具有銀級或更高的可靠性，而您設定了自動調整規則的任何擴展集，則具有銀級或更高的持久性。
  * 自動調整規則的 capacity[minimum] 必須大於或等於 5 個虛擬機器執行個體，且必須大於或等於主要節點類型的最低可靠性層級。

> [!NOTE]
> Azure Service Fabric 具狀態 service fabric:/System/InfastructureService/<NODE_TYPE_NAME> 會在每個具有銀級或更高持久性的節點類型上執行，這是唯一可在 Azure 中執行於您任何叢集節點類型上的系統服務。 

## <a name="vertical-scaling-considerations"></a>垂直調整考量

要[垂直調整](https://docs.microsoft.com/azure/service-fabric/virtual-machine-scale-set-scale-node-type-scale-out#upgrade-the-size-and-operating-system-of-the-primary-node-type-vms) Azure Service Fabric 中的節點類型，需執行若干步驟並考量相關事項。 例如︰
* 在調整之前，叢集必須處於良好狀態。 否則，您只會讓叢集更加不穩定。
* 所有裝載具狀態服務的 Service Fabric 叢集 NodeType，都需要**銀級或更高的持久性層級**。

> [!NOTE]
> 您裝載具狀態 Service Fabric 系統服務的主要 NodeType，必須具備銀級或更高的持久性層級。 啟用銀級持久性之後，諸如升級、新增或移除節點等叢集作業的速度都會變慢，因為此時系統的最佳化是以資料安全為導向，而不是作業速度。

垂直調整虛擬機器擴展集是破壞性作業。 相對地，藉由新增使用所需 SKU 的新擴展集來水平調整叢集，並將服務移轉至您所需的 SKU，將可完成安全的垂直調整作業。 變更虛擬機器擴展集資源 SKU 是破壞性作業，因為它會為主機重新安裝映像，而移除所有保存於本機的狀態。

您的叢集會使用 Service Fabric [節點屬性和放置條件約束](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-resource-manager-cluster-description#node-properties-and-placement-constraints)來決定要在何處裝載應用程式服務。 垂直調整主要節點類型時，請為虛擬機器擴展集 Service Fabric 延伸模組中的 `"nodeTypeRef"` 宣告相同的屬性值。 Resource Manager 範本的下列程式碼片段顯示您將宣告的屬性，其值與您新佈建而要調整的擴展集相同，僅支援作為叢集的暫時具狀態屬性：

```json
"settings": {
   "nodeTypeRef": ["[parameters('primaryNodetypeName')]"]
}
```

> [!NOTE]
> 請不要讓您的叢集以使用多個相同 `nodeTypeRef` 屬性值的擴展集執行的時間超過完成成功垂直調整作業所需的時間。
> 請一律先在測試環境中驗證作業，再嘗試於生產環境變更。 根據預設，Service Fabric 叢集的系統服務會有僅以主要 nodetype 為目標的放置條件約束。

在宣告節點屬性和放置條件約束後，請逐一對各個 VM 執行個體執行下列步驟。 如此，在其他位置建立新複本時，系統服務 (以及您的具狀態服務) 將可在您要移除的 VM 執行個體上正常關閉。
1. 從 PowerShell 使用意圖 'RemoveNode' 執行 `Disable-ServiceFabricNode`，以停用您要移除的節點。 移除具有最高編號的節點類型。 例如，如果您有六個節點叢集，請移除 "MyNodeType_5" 虛擬機器執行個體。
2. 執行 `Get-ServiceFabricNode` 以確保節點已轉換為停用狀態。 如果沒有，請等到節點停用。 每個節點可能各需要數小時的時間。 請等到節點已轉換為停用狀態後，再繼續操作。
3. 將該節點類型的 VM 數目減少一個。 此時將會移除最高的 VM 執行個體。
4. 視需要重複步驟 1 到 3，但是請永遠不要將主要節點類型的執行個體數目相應減少到少於可靠性層級所需的數目。 如需建議的行個體清單，請參閱[規劃 Service Fabric 叢集容量](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity)。

## <a name="horizontal-scaling"></a>水平調整

Service Fabric 的水平相應縮小可[手動](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-scale-up-down)或是[以程式設計方式](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-programmatic-scaling)完成。

> [!NOTE]
> 調整具有銀級或金級持久性的節點類型時，調整速度將會變慢。

### <a name="scaling-out"></a>相應放大

藉由增加特定虛擬機器擴展集的執行個體計數，將 Service Fabric 叢集相應放大。 您可以使用 AzureClient 和所需擴展集的識別碼以程式設計方式進行相應放大，以增加容量。

```c#
var scaleSet = AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId);
var newCapacity = (int)Math.Min(MaximumNodeCount, scaleSet.Capacity + 1);
scaleSet.Update().WithCapacity(newCapacity).Apply(); 
```

若要手動相應放大，請在所需的[虛擬機器擴展集](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile)資源的 SKU 屬性中更新容量。
```json
"sku": {
    "name": "[parameters('vmNodeType0Size')]",
    "capacity": "[parameters('nt0InstanceCount')]",
    "tier": "Standard"
}
```

### <a name="scaling-in"></a>相應縮小

相應縮小所需考量的因素會比相應放大多一些。例如︰
* Service Fabric 系統服務會在叢集內的主要節點類型中執行。 切勿關閉該節點類型的執行個體，或將其數目相應減少到低於可靠性層級保證所需的執行個體數目。 
* 針對具狀態服務，您需要一些始終啟動的節點來維持可用性，以及維持服務的狀態。 您至少需要與分割區/服務的目標複本集計數相等的節點數目。

若要手動相應縮小，請遵循下列步驟︰

1. 從 PowerShell 使用意圖 'RemoveNode' 執行 `Disable-ServiceFabricNode`，以停用您要移除的節點。 移除具有最高編號的節點類型。 例如，如果您有六個節點叢集，請移除 "MyNodeType_5" 虛擬機器執行個體。
2. 執行 `Get-ServiceFabricNode` 以確保節點已轉換為停用狀態。 如果沒有，請等到節點停用。 每個節點可能各需要數小時的時間。 請等到節點已轉換為停用狀態後，再繼續操作。
3. 將該節點類型的 VM 數目減少一個。 此時將會移除最高的 VM 執行個體。
4. 視需要重複步驟 1 到 3，但是請永遠不要將主要節點類型的執行個體數目相應減少到少於可靠性層級所需的數目。 如需建議的行個體清單，請參閱[規劃 Service Fabric 叢集容量](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity)。

若要手動相應縮小，請在所需的[虛擬機器擴展集](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile)資源的 SKU 屬性中更新容量。

```json
"sku": {
    "name": "[parameters('vmNodeType0Size')]",
    "capacity": "[parameters('nt0InstanceCount')]",
    "tier": "Standard"
}
```

1. 重複步驟 1 到 3，直到佈建了您需要的容量為止。 切勿將主要節點類型的執行個體數目相應減少到低於可靠性層級保證所需的數目。 如需關於可靠性層級及其所需執行個體數目的詳細資訊，請參閱[規劃 Service Fabric 叢集容量](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity)。

您必須做好節點的關機準備，才能以程式設計方式進行相應縮小。 準備工作包括尋找要移除的節點 (最高的執行個體節點) 並加以停用。 例如︰

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

## <a name="reliability-levels"></a>可靠性層級

[可靠性層級](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity)是 Service Fabric 叢集資源的屬性之一，且對於個別的 nodeType，此屬性的設定必須相同。 它會控制叢集系統服務的複寫因子，是屬於叢集資源層級的設定。 可靠性層級將決定您的主要節點類型必須具備的節點數目下限。 可靠性層級可以採用以下的值：
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
> 執行 Bronze 持久性的節點類型「沒有權限」。 這表示不會停止或延遲對您的無狀態工作負載造成影響的基礎結構作業 (可能會影響工作負載)。 銅級持久性僅適用於執行無狀態工作負載的節點類型。 對於生產工作負載，請執行銀級或更高層級，以確保狀態的一致性。 請根據[容量規劃文件](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity)中的指導方針選擇正確的可靠性。

持久性層級必須設定於兩個資源中。 [虛擬機器擴展集資源](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile)的延伸模組設定檔：

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
