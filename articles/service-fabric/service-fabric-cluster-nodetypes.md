---
title: Azure Service Fabric 節點類型與虛擬機器擴展集 | Microsoft Docs
description: 了解 Azure Service Fabric 節點類型與 VM 擴展集的關係，以及如何遠端連線到擴展集執行個體或叢集節點。
services: service-fabric
documentationcenter: .net
author: ChackDan
manager: chackdan
editor: ''
ms.assetid: 5441e7e0-d842-4398-b060-8c9d34b07c48
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/23/2018
ms.author: chackdan
ms.openlocfilehash: a5f8735df2b230de2b0ddcdcccff09430bada9e3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "64684681"
---
# <a name="azure-service-fabric-node-types-and-virtual-machine-scale-sets"></a>Azure Service Fabric 節點類型與虛擬機器擴展集
[虛擬機器擴展集](/azure/virtual-machine-scale-sets)是 Azure 計算資源。 您可以使用擴展集來將虛擬機器集合以一組的方式加以部署和管理。 在 Azure Service Fabric 叢集中定義的每個節點類型會設定不同的擴展集。  擴展集中的 Microsoft.Azure.ServiceFabric 虛擬機器擴充功能的每部虛擬機器上安裝的 Service Fabric 執行階段。 您可以分開相應增加或減少每個節點類型、變更每個叢集節點上執行的 OS SKU、開啟不同組的連接埠，並使用不同的容量計量。

下圖顯示具有 FrontEnd 和 BackEnd 兩個節點類型的叢集。 每個節點類型各有五個節點。

![有兩個節點類型的叢集][NodeTypes]

## <a name="map-virtual-machine-scale-set-instances-to-nodes"></a>將虛擬機器擴展集執行個體對應至節點
如上圖所示，擴展集執行個體是由執行個體 0 開始編號，然後再逐個增加 1。 編號會反映在節點名稱中。 例如，BackEnd_0 節點是 BackEnd 擴展集的執行個體 0。 這個特定的擴展集有五個執行個體，名稱分別是 BackEnd_0、BackEnd_1、BackEnd_2、BackEnd_3、BackEnd_4。

當您相應增加擴展集，系統就會建立一個新的執行個體。 新擴展集執行個體的名稱通常是擴展集名稱 + 下一個執行個體編號。 在我們的範例中是 BackEnd_5。

## <a name="map-scale-set-load-balancers-to-node-types-and-scale-sets"></a>將擴展集負載平衡器對應至節點類型和擴展集
如果您已 Azure 入口網站中部署叢集，或已使用範例 Azure Resource Manager 範本部署叢集，系統會列出資源群組下的所有資源都。 可以看到每個擴展集或節點類型的負載平衡器。 負載平衡器名稱會使用下列格式：**LB-&lt;節點類型名稱&gt;** 。 例如下圖中顯示的 LB-sfcluster4doc-0：

![資源][Resources]

## <a name="service-fabric-virtual-machine-extension"></a>Service Fabric 虛擬機器擴充功能
Service Fabric 虛擬機器延伸模組用來啟動 Service Fabric Azure 虛擬機器，並設定節點安全性。

以下是 Service Fabric 虛擬機器擴充功能的程式碼片段：

```json
"extensions": [
  {
    "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
    "properties": {
      "type": "ServiceFabricLinuxNode",
      "autoUpgradeMinorVersion": true,
      "protectedSettings": {
        "StorageAccountKey1": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('supportLogStorageAccountName')),'2015-05-01-preview').key1]",
       },
       "publisher": "Microsoft.Azure.ServiceFabric",
       "settings": {
         "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
         "nodeTypeRef": "[variables('vmNodeType0Name')]",
         "durabilityLevel": "Silver",
         "enableParallelJobs": true,
         "nicPrefixOverride": "[variables('subnet0Prefix')]",
         "certificate": {
           "commonNames": [
             "[parameters('certificateCommonName')]"
           ],
           "x509StoreName": "[parameters('certificateStoreValue')]"
         }
       },
       "typeHandlerVersion": "1.1"
     }
   },
```

屬性描述如下：

| **名稱** | **允許的值** | ** --- ** | **指引或簡短描述** |
| --- | --- | --- | --- |
| name | 字串 | --- | 延伸模組的唯一名稱 |
| type | 「 ServiceFabricLinuxNode"或者"ServiceFabricWindowsNode | --- | 識別作業系統的 Service Fabric 會在啟動載入至 |
| autoUpgradeMinorVersion | True 或 False | --- | 啟用自動升級的 SF 執行階段次要版本 |
| publisher | Microsoft.Azure.ServiceFabric | --- | Service Fabric 延伸模組發行者的名稱 |
| clusterEndpont | 字串 | --- | 管理端點的 URI:PORT |
| nodeTypeRef | 字串 | --- | nodeType 的名稱 |
| durabilityLevel | 銅級、 銀級、 金級、 白金級 | --- | 若要暫停不可變的 Azure 基礎結構允許的時間 |
| enableParallelJobs | True 或 False | --- | 啟用計算 ParallelJobs，例如移除 VM 和相同的擴展集以平行方式重新啟動 VM |
| nicPrefixOverride | 字串 | --- | 子網路首碼，如 「 10.0.0.0/24" |
| commonNames | string[] | --- | 已安裝的叢集憑證通用名稱 |
| x509StoreName | 字串 | --- | 已安裝的叢集憑證所在的存放區的名稱 |
| typeHandlerVersion | 1.1 | --- | 延伸模組的版本。 1.0 的傳統版本的延伸模組會建議您升級至 1.1 |

## <a name="next-steps"></a>後續步驟
* 請參閱[「到處部署」功能和與 Azure 受控叢集比較的概觀](service-fabric-deploy-anywhere.md)。
* 了解[叢集安全性](service-fabric-cluster-security.md)。
* [遠端連線](service-fabric-cluster-remote-connect-to-azure-cluster-node.md)到特定擴展集執行個體
* 部署後在叢集 VM 上[更新 RDP 連接埠範圍值](./scripts/service-fabric-powershell-change-rdp-port-range.md)
* 變更叢集 VM 的[管理員使用者名稱和密碼](./scripts/service-fabric-powershell-change-rdp-user-and-pw.md)

<!--Image references-->
[NodeTypes]: ./media/service-fabric-cluster-nodetypes/NodeTypes.png
[Resources]: ./media/service-fabric-cluster-nodetypes/Resources.png
[InboundNatPools]: ./media/service-fabric-cluster-nodetypes/InboundNatPools.png
