---
title: 容量規劃和調整適用於 Azure Service Fabric |Microsoft Docs
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
ms.openlocfilehash: 58c50eac60f1a8a47aac9a88125bc3e0132ec3db
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67059153"
---
# <a name="capacity-planning-and-scaling-for-azure-service-fabric"></a>容量規劃和調整適用於 Azure Service Fabric

建立任何 Azure Service Fabric 叢集，或調整計算資源，可裝載您的叢集之前，請務必規劃容量。 如需規劃容量的詳細資訊，請參閱[規劃 Service Fabric 叢集容量](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity)。 叢集延展性的進一步最佳作法指引，請參閱[Service Fabric 延展性考量](https://docs.microsoft.com/azure/architecture/reference-architectures/microservices/service-fabric#scalability-considerations)。

除了考慮節點類型和叢集的特性，您應該預期花費的時間超過一小時才能完成在生產環境的調整作業。 這項考量是不論您要新增的 Vm 數目，則為 true。

## <a name="autoscaling"></a>自動調整
您應該執行調整作業，透過 Azure Resource Manager 範本，因為它是最佳作法，將[資源組態即程式碼]( https://docs.microsoft.com/azure/service-fabric/service-fabric-best-practices-infrastructure-as-code)。 

使用透過虛擬機器擴展集的自動調整可讓您建立版本的 Resource Manager 範本，不正確定義您的虛擬機器擴展集的執行個體計數。 不正確的定義會增加，未來的部署將會導致非預期的調整作業的風險。 一般情況下，您應該使用自動調整，如果：

* 部署已宣告適當容量的 Resource Manager 範本，無法為您的使用案例提供支援。
     
   除了手動調整，您可以設定[持續整合和傳遞管線 Azure DevOps 服務中的使用 Azure 資源群組部署專案](https://docs.microsoft.com/azure/vs-azure-tools-resource-groups-ci-in-vsts)。 此管線通常會觸發邏輯應用程式會從查詢的虛擬機器的效能度量[Azure 監視器 REST API](https://docs.microsoft.com/azure/azure-monitor/platform/rest-api-walkthrough)。 管線自動調整的基礎有效地在任何計量上您想，Resource Manager 範本進行最佳化。
* 您需要水平調整只有一個虛擬機器擴展集節點，一次。
   
   若要一次相應放大三個或多個節點，您應該[相應放大 Service Fabric 叢集中新增虛擬機器擴展集](virtual-machine-scale-set-scale-node-type-scale-out.md)。 它是最安全的方式相應縮小和相應放大虛擬機器擴展設定水平的方式，一次一個節點。
* 您擁有銀級的可靠性更高版本為 Service Fabric 叢集和銀級耐久性或更高版本，您可以設定自動調整規則的任何標尺上。
  
   自動調整規則的最小容量必須等於或大於 5 的虛擬機器執行個體。 它也必須等於或大於主要節點類型的程式可靠性層級最小值。

> [!NOTE]
> Service Fabric 具狀態的 service fabric: / System/InfastructureService/< NODE_TYPE_NAME > durability 銀級或更高版本的每個節點類型上執行。 它是唯一的系統服務，支援在 Azure 中執行任何您叢集的節點類型。

## <a name="vertical-scaling-considerations"></a>垂直調整考量

[垂直調整](https://docs.microsoft.com/azure/service-fabric/virtual-machine-scale-set-scale-node-type-scale-out)Azure Service Fabric 中的節點類型需要的步驟和考量的數字。 例如:

* 在調整之前，叢集必須處於良好狀態。 否則，您會造成不穩定進一步的叢集。
* 所有 Service Fabric 叢集的節點類型裝載具狀態服務需要銀級耐久性層級或更高。

> [!NOTE]
> 您裝載具狀態的 Service Fabric 系統服務的主要節點類型必須是銀級耐久性層級或更高。 啟用 Silver 的持久性，叢集作業，例如升級之後新增或移除的節點，並依此類推會慢因為系統於作業的速度最佳化的資料安全。

垂直縮放比例的虛擬機器擴展集是破壞性作業。 相反地，水平調整叢集新增新的擴展集使用所需的 SKU。 然後，將服務移轉給您所需的 SKU，以完成安全的垂直調整作業。 變更虛擬機器擴展集資源 SKU 是破壞性作業，因為它在您的主機，哪些移除所有在本機保存的狀態重新安裝映像。

您的叢集使用 Service Fabric[節點屬性和放置條件約束](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-resource-manager-cluster-description#node-properties-and-placement-constraints)決定要裝載您的應用程式服務。 當您要以垂直方式調整主要節點類型時，宣告相同的屬性值的`"nodeTypeRef"`。 您可以在虛擬機器擴展集的 Service Fabric 延伸模組中找到這些值。 

Resource Manager 範本中的下列程式碼片段會顯示您要宣告的屬性。 它具有您要調整，而且只能作為暫時的具狀態服務，為您的叢集支援的新佈建的擴展集相同的值。

```json
"settings": {
   "nodeTypeRef": ["[parameters('primaryNodetypeName')]"]
}
```

> [!NOTE]
> 不要離開您的叢集使用多個使用相同的擴展集執行`nodeTypeRef`完成成功的垂直調整作業所需的長度超過的屬性值。
>
> 一律驗證在測試環境中的作業，才能嘗試進行到生產環境的變更。 根據預設，Service Fabric 叢集的系統服務會有的放置條件約束，以只在目標主要節點類型。

在宣告節點屬性和放置條件約束後，請逐一對各個 VM 執行個體執行下列步驟。 這可讓系統服務 （及具狀態服務） 依正常程序關閉新的複本建立其他位置時，您要移除的 VM 執行個體。

1. 從 PowerShell，執行`Disable-ServiceFabricNode`意圖`RemoveNode`停用您要移除的節點。 移除具有最高編號的節點類型。 例如，如果您有六個節點叢集，移除 「 MyNodeType_5"虛擬機器執行個體。
2. 執行 `Get-ServiceFabricNode` 以確保節點已轉換為停用狀態。 如果沒有，請等到節點停用。 這可能需要幾個小時，每個節點。 請等到節點已轉換為停用狀態後，再繼續操作。
3. 減少該節點類型的一個 Vm 數目。 此時將會移除最高的 VM 執行個體。
4. 視需要重複步驟 1 到 3，但是請永遠不要將主要節點類型的執行個體數目相應減少到少於可靠性層級所需的數目。 如需建議的行個體清單，請參閱[規劃 Service Fabric 叢集容量](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity)。

### <a name="example-scenario"></a>範例案例
何時執行垂直調整作業的支援案例： 您想要將您的 Service Fabric 叢集和應用程式從非受控磁碟移轉至受控磁碟，而不需要應用程式停機。 

您可以佈建的新的虛擬機器擴展集使用受控磁碟，並執行應用程式升級，以佈建的容量為目標的放置條件約束。 Service Fabric 叢集，可以接著排程您的應用程式不必停機的升級網域所推出的佈建的叢集節點容量的工作負載。 

後端集區端點[Azure Load Balancer Basic SKU](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview#skus)可以在一個可用性設定組或虛擬機器擴展集的虛擬機器。 這表示您不能使用基本 SKU 負載平衡器，如果您在擴展集，而不會造成暫時無法存取您的 Service Fabric 叢集管理端點之間移動您的 Service Fabric 系統應用程式。 即使叢集和其應用程式仍在執行，也是如此。

執行基本 SKU load balancer 與標準 SKU 負載平衡器資源之間的虛擬 IP 位址 (VIP) 交換時，使用者通常佈建的標準 SKU 負載平衡器。 這項技術會限制為大約 30 秒 VIP 交換所需的任何未來無法存取。

## <a name="horizontal-scaling"></a>水平調整

您可以執行其中的水平縮放比例[手動](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-scale-up-down)或是[以程式設計方式](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-programmatic-scaling)。

> [!NOTE]
> 如果您要調整具有 Silver 或 Gold 持久性的節點類型，縮放作業將會變慢。

### <a name="scaling-out"></a>相應放大

藉由增加特定虛擬機器擴展集的執行個體計數，以相應放大 Service Fabric 叢集。 您可以透過相應放大以程式設計方式使用`AzureClient`和所需的擴展集以增加容量的識別碼。

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

* Service Fabric 系統服務在叢集中的主要節點類型中執行。 切勿關閉該節點類型的執行個體，或將其數目相應減少到低於可靠性層級保證所需的執行個體數目。 
* 對於具狀態服務，您必須永遠是以維護可用性並維持服務的狀態最新的節點數目。 最少，您需要節點的數目等於資料分割或服務的目標複本集數量。

若要手動相應縮小，請遵循下列步驟︰

1. 從 PowerShell，執行`Disable-ServiceFabricNode`意圖`RemoveNode`停用您要移除的節點。 移除具有最高編號的節點類型。 例如，如果您有六個節點叢集，移除 「 MyNodeType_5"虛擬機器執行個體。
2. 執行 `Get-ServiceFabricNode` 以確保節點已轉換為停用狀態。 如果沒有，請等到節點停用。 這可能需要幾個小時，每個節點。 請等到節點已轉換為停用狀態後，再繼續操作。
3. 減少該節點類型的一個 Vm 數目。 此時將會移除最高的 VM 執行個體。
4. 重複步驟 1 到 3，視需要等到佈建您需要的容量。 切勿將主要節點類型的執行個體數目相應減少到低於可靠性層級保證所需的數目。 如需建議的行個體清單，請參閱[規劃 Service Fabric 叢集容量](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity)。

若要手動調整，更新所需的 SKU 屬性中的容量[虛擬機器擴展集](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile)資源。

```json
"sku": {
    "name": "[parameters('vmNodeType0Size')]",
    "capacity": "[parameters('nt0InstanceCount')]",
    "tier": "Standard"
}
```

您必須準備關閉，若要以程式設計方式調整中的節點。 尋找要節點移除 （最高執行個體節點）。 例如:

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

停用和移除節點使用相同的`FabricClient`執行個體 (`client`在此情況下) 和節點執行個體 (`instanceIdString`在此情況下)，您在先前的程式碼中使用：

```c#
var scaleSet = AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId);

// Remove the node from the Service Fabric cluster
ServiceEventSource.Current.ServiceMessage(Context, $"Disabling node {mostRecentLiveNode.NodeName}");
await client.ClusterManager.DeactivateNodeAsync(mostRecentLiveNode.NodeName, NodeDeactivationIntent.RemoveNode);

// Wait (up to a timeout) for the node to gracefully shut down
var timeout = TimeSpan.FromMinutes(5);
var waitStart = DateTime.Now;
while ((mostRecentLiveNode.NodeStatus == System.Fabric.Query.NodeStatus.Up || mostRecentLiveNode.NodeStatus == System.Fabric.Query.NodeStatus.Disabling) &&
        DateTime.Now - waitStart < timeout)
{
    mostRecentLiveNode = (await client.QueryManager.GetNodeListAsync()).FirstOrDefault(n => n.NodeName == mostRecentLiveNode.NodeName);
    await Task.Delay(10 * 1000);
}

// Decrement virtual machine scale set capacity
var newCapacity = (int)Math.Max(MinimumNodeCount, scaleSet.Capacity - 1); // Check min count 

scaleSet.Update().WithCapacity(newCapacity).Apply();
```

> [!NOTE]
> 當您相應減少叢集時，您會看到顯示處於狀況不良的狀態，Service Fabric Explorer 中已移除的節點 /VM 執行個體。 如需此行為的說明，請參閱 < [Service Fabric Explorer 中，您可看到的行為](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-scale-up-down#behaviors-you-may-observe-in-service-fabric-explorer)。 您可以：
> * 呼叫[Remove-servicefabricnodestate 命令](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricnodestate?view=azureservicefabricps)以適當的節點名稱。
> * 部署[Service Fabric 自動調整輔助應用程式](https://github.com/Azure/service-fabric-autoscale-helper/)在叢集上。 此應用程式可確保相應減少節點會清除從 Service Fabric Explorer。

## <a name="reliability-levels"></a>可靠性層級

[可靠性層級](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity)是 Service Fabric 叢集資源的屬性。 不能設定不同的個別節點類型。 它會控制叢集系統服務的複寫因子，是屬於叢集資源層級的設定。 

可靠性層級將決定您的主要節點類型必須具備的節點數目下限。 可靠性層級可以採用以下的值：

* 白金級：執行系統服務的七個和 9 個種子節點的目標複本集計數。
* 金級：執行系統服務的七個和七個種子節點的目標複本集計數。
* 銀級：系統服務執行的五個和五個種子節點的目標複本集計數。
* 銅級：系統服務執行的三個和三個種子節點的目標複本集計數。

建議的最低可靠性層級為銀級。

可靠性層級設定於 [Microsoft.ServiceFabric/clusters resource](https://docs.microsoft.com/azure/templates/microsoft.servicefabric/2018-02-01/clusters) 的屬性區段中，如下所示：

```json
"properties":{
    "reliabilityLevel": "Silver"
}
```

## <a name="durability-levels"></a>持久性層級

> [!WARNING]
> 執行 Bronze 持久性的節點類型「沒有權限」  。 會影響您的無狀態工作負載的基礎結構工作將不會停止或延遲，可能會影響您的工作負載。 
>
> 銅級持久性僅適用於執行無狀態工作負載的節點類型。 對於生產工作負載，執行銀級或更高版本以確保一致性狀態。 請根據[容量規劃文件](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity)中的指導方針選擇正確的可靠性。

持久性層級必須設定於兩個資源中。 有的擴充功能設定檔[虛擬機器擴展集資源](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile):

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

其他資源受到`nodeTypes`中[microsoft.servicefabric/clusters 資源](https://docs.microsoft.com/azure/templates/microsoft.servicefabric/2018-02-01/clusters): 

```json
"nodeTypes": [
    {
        "name": "[variables('vmNodeType0Name')]",
        "durabilityLevel": "Bronze"
    }
]
```

## <a name="next-steps"></a>後續步驟

* 在 VM 或執行 Windows Server 的電腦上建立叢集：[適用於 Windows Server 的 Service Fabric 叢集建立](service-fabric-cluster-creation-for-windows-server.md)。
* 在 VM 或執行 Linux 的電腦上建立叢集：[建立 Linux 叢集](service-fabric-cluster-creation-via-portal.md)。
* 了解 [Service Fabric 支援選項](service-fabric-support.md)。

[Image1]: ./media/service-fabric-best-practices/generate-common-name-cert-portal.png
