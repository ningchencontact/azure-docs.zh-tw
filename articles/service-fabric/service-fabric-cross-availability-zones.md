---
title: 部署 Azure Service Fabric 叢集跨越多個可用性區域 |Microsoft Docs
description: 了解如何建立 Azure Service Fabric 叢集跨越多個可用性區域。
services: service-fabric
documentationcenter: .net
author: peterpogorski
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/25/2019
ms.author: pepogors
ms.openlocfilehash: b664c3d655ab45c89a65a0aea31622f57ddc8d9e
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/27/2019
ms.locfileid: "65077451"
---
# <a name="deploy-an-azure-service-fabric-cluster-across-availability-zones"></a>部署 Azure Service Fabric 叢集跨越多個可用性區域
在 Azure 中的可用性區域 」 是高可用性供應項目來保護您的應用程式和資料從資料中心故障影響。 可用性區域會是唯一的實體位置，配備獨立電力、 冷卻和網路的 Azure 區域內。

Service Fabric 支援跨越可用性區域，藉由部署釘選的節點型別與特定區域的叢集。 這可確保應用程式的高可用性。 在選取的區域中，才可以使用 azure 可用性區域。 如需詳細資訊，請參閱 < [Azure 可用性區域概觀](https://docs.microsoft.com/azure/availability-zones/az-overview)。

可使用範例範本：[Service Fabric 跨可用性區域的範本](https://github.com/Azure-Samples/service-fabric-cluster-templates)

## <a name="recommended-topology-for-primary-node-type-of-azure-service-fabric-clusters-spanning-across-availability-zones"></a>建議的 Azure Service Fabric 叢集跨越可用性區域間的主要節點類型的拓樸
Service Fabric 叢集中分散於可用性區域可確保高可用性叢集狀態。 Service Fabric 叢集跨越區域中，您必須建立區域所支援的每個可用性區域中的主要節點類型。 這會將種子節點平均分散到每個主要節點類型。

主要節點類型的建議的拓撲需要如下所述的資源：

* 叢集的可靠性層級設定為白金級。
* 三個節點類型標示為主要。
    * 每個節點類型應該對應至其本身位於不同區域中的虛擬機器擴展集。
    * 每個虛擬機器擴展集應該有至少 5 個節點 （Silver 持久性）。
* 使用標準 SKU 單一公用 IP 資源。
* 使用標準 SKU 單一負載平衡器資源。
* NSG，您可以在其中部署您的虛擬機器擴展集的子網路所參考。

>[!NOTE]
> 單一放置群組屬性必須設定為 true，因為 Service Fabric 不支援跨區域的單一虛擬機器擴展集虛擬機器擴展集。

 ![Azure Service Fabric 的可用性區域的架構][sf-architecture]

## <a name="networking-requirements"></a>網路需求
### <a name="public-ip-and-load-balancer-resource"></a>公用 IP 與負載平衡器資源
若要啟用屬性上的虛擬機器擴展集資源、 負載平衡器和參考該虛擬機器擴展集 IP 資源的區域都必須使用*標準*SKU。 建立負載平衡器或 IP 資源，而且沒有 SKU 屬性，會建立基本的 SKU，不支援可用性區域。 標準 SKU 負載平衡器會封鎖來自外部的所有流量，根據預設，若要允許外部流量，必須部署 NSG 與子網路。

```json
{
    "apiVersion": "2018-11-01",
    "type": "Microsoft.Network/publicIPAddresses",
    "name": "[concat('LB','-', parameters('clusterName')]",
    "location": "[parameters('computeLocation')]",
    "sku": {
        "name": "Standard"
    }
}
```

```json
{
    "apiVersion": "2018-11-01",
    "type": "Microsoft.Network/loadBalancers",
    "name": "[concat('LB','-', parameters('clusterName')]", 
    "location": "[parameters('computeLocation')]",
    "dependsOn": [
        "[concat('Microsoft.Network/networkSecurityGroups/', concat('nsg', parameters('subnet0Name')))]"
    ],
    "properties": {
        "addressSpace": {
            "addressPrefixes": [
                "[parameters('addressPrefix')]"
            ]
        },
        "subnets": [
        {
            "name": "[parameters('subnet0Name')]",
            "properties": {
                "addressPrefix": "[parameters('subnet0Prefix')]",
                "networkSecurityGroup": {
                "id": "[resourceId('Microsoft.Network/networkSecurityGroups', concat('nsg', parameters('subnet0Name')))]"
              }
            }
          }
        ]
    },
    "sku": {
        "name": "Standard"
    }
}
```

>[!NOTE]
> 您不可能進行就地變更 SKU 的公用 IP 與負載平衡器資源上。 如果您要從現有的資源具有基本 SKU，請參閱這篇文章的 [移轉] 區段。

### <a name="virtual-machine-scale-set-nat-rules"></a>虛擬機器擴展集的 NAT 規則
負載平衡器輸入的 NAT 規則應該符合從虛擬機器擴展集的 NAT 集區。 每個虛擬機器擴展集必須具有唯一的輸入的 NAT 集區。

```json
{
"inboundNatPools": [
    {
        "name": "LoadBalancerBEAddressNatPool0",
        "properties": {
            "backendPort": "3389",
            "frontendIPConfiguration": {
                "id": "[variables('lbIPConfig0')]"
            },
            "frontendPortRangeEnd": "50999",
            "frontendPortRangeStart": "50000",
            "protocol": "tcp"
        }
    },
    {
        "name": "LoadBalancerBEAddressNatPool1",
        "properties": {
            "backendPort": "3389",
            "frontendIPConfiguration": {
                "id": "[variables('lbIPConfig0')]"
            },
            "frontendPortRangeEnd": "51999",
            "frontendPortRangeStart": "51000",
            "protocol": "tcp"
        }
    },
    {
        "name": "LoadBalancerBEAddressNatPool2",
        "properties": {
            "backendPort": "3389",
            "frontendIPConfiguration": {
                "id": "[variables('lbIPConfig0')]"
            },
            "frontendPortRangeEnd": "52999",
            "frontendPortRangeStart": "52000",
            "protocol": "tcp"
        }
    }
    ]
}
```

### <a name="standard-sku-load-balancer-outbound-rules"></a>標準 SKU 負載平衡器的輸出規則
標準 Load Balancer 和標準公用 IP 將引進新功能以及不同的行為相較於使用基本 Sku 的輸出連線。 如果您想要在使用標準 SKU 時輸出連線，您必須使用標準公用 IP 位址或標準公用 Load Balancer 明確定義該連線。 如需詳細資訊，請參閱 <<c0> [ 輸出連線](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#snatexhaust)並[Azure 標準 Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview)。

>[!NOTE]
> 標準的範本會參考其中預設會允許所有輸出流量的 NSG。 傳送輸入的流量會限制為所需的 Service Fabric 管理作業的連接埠。 可以修改 NSG 規則，以符合您的需求。

### <a name="enabling-zones-on-a-virtual-machine-scale-set"></a>啟用區域上的虛擬機器擴展集
若要啟用區域，請在 虛擬機器擴展集必須包含下列三個值中的虛擬機器擴展集資源。

* 第一個值**區域**屬性，指定虛擬機器擴展集將會部署到哪一個可用性區域。
* 第二個值是"singlePlacementGroup"屬性，必須設定為 true。
* 第三個值是"faultDomainOverride"屬性在 Service Fabric 虛擬機器擴展集延伸模組。 此屬性的值應該包含的地區和區域將會在其中放置此虛擬機器擴展集。 範例: 「 faultDomainOverride":"eastus/az1 「 所有的虛擬機器擴展集資源必須放置在相同的區域，因為 Azure Service Fabric 叢集不需要跨區域支援。

```json
{
    "apiVersion": "2018-10-01",
    "type": "Microsoft.Compute/virtualMachineScaleSets",
    "name": "[parameters('vmNodeType1Name')]",
    "location": "[parameters('computeLocation')]",
    "zones": ["1"],
    "properties": {
        "singlePlacementGroup": "true",
    },
    "virtualMachineProfile": {
    "extensionProfile": {
    "extensions": [
    {
    "name": "[concat(parameters('vmNodeType1Name'),'_ServiceFabricNode')]",
    "properties": {
        "type": "ServiceFabricNode",
        "autoUpgradeMinorVersion": false,
        "publisher": "Microsoft.Azure.ServiceFabric.Test",
        "settings": {
            "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
            "nodeTypeRef": "[parameters('vmNodeType1Name')]",
            "dataPath": "D:\\\\SvcFab",
            "durabilityLevel": "Silver",
            "certificate": {
                "thumbprint": "[parameters('certificateThumbprint')]",
                "x509StoreName": "[parameters('certificateStoreValue')]"
            },
            "systemLogUploadSettings": {
                "Enabled": true
            },
            "faultDomainOverride": "eastus/az1"
        },
        "typeHandlerVersion": "1.0"
    }
}
```

### <a name="enabling-multiple-primary-node-types-in-the-service-fabric-cluster-resource"></a>啟用多個主要節點類型，在 Service Fabric 叢集資源
若要將一或多個節點類型設為主要在叢集資源，「 isPrimary"屬性設定為"true"。 在部署 Service Fabric 叢集跨越多個可用性區域時，您應該在不同區域中有三個節點類型。

```json
{
    "reliabilityLevel": "Platinum",
    "nodeTypes": [
    {
        "name": "[parameters('vmNodeType0Name')]",
        "applicationPorts": {
            "endPort": "[parameters('nt0applicationEndPort')]",
            "startPort": "[parameters('nt0applicationStartPort')]"
        },
        "clientConnectionEndpointPort": "[parameters('nt0fabricTcpGatewayPort')]",
        "durabilityLevel": "Silver",
        "ephemeralPorts": {
            "endPort": "[parameters('nt0ephemeralEndPort')]",
            "startPort": "[parameters('nt0ephemeralStartPort')]"
        },
        "httpGatewayEndpointPort": "[parameters('nt0fabricHttpGatewayPort')]",
        "isPrimary": true,
        "vmInstanceCount": "[parameters('nt0InstanceCount')]"
    },
    {
        "name": "[parameters('vmNodeType1Name')]",
        "applicationPorts": {
            "endPort": "[parameters('nt1applicationEndPort')]",
            "startPort": "[parameters('nt1applicationStartPort')]"
        },
        "clientConnectionEndpointPort": "[parameters('nt1fabricTcpGatewayPort')]",
        "durabilityLevel": "Silver",
        "ephemeralPorts": {
            "endPort": "[parameters('nt1ephemeralEndPort')]",
            "startPort": "[parameters('nt1ephemeralStartPort')]"
        },
        "httpGatewayEndpointPort": "[parameters('nt1fabricHttpGatewayPort')]",
        "isPrimary": true,
        "vmInstanceCount": "[parameters('nt1InstanceCount')]"
    },
    {
        "name": "[parameters('vmNodeType2Name')]",
        "applicationPorts": {
            "endPort": "[parameters('nt2applicationEndPort')]",
            "startPort": "[parameters('nt2applicationStartPort')]"
        },
        "clientConnectionEndpointPort": "[parameters('nt2fabricTcpGatewayPort')]",
        "durabilityLevel": "Silver",
        "ephemeralPorts": {
            "endPort": "[parameters('nt2ephemeralEndPort')]",
            "startPort": "[parameters('nt2ephemeralStartPort')]"
        },
        "httpGatewayEndpointPort": "[parameters('nt2fabricHttpGatewayPort')]",
        "isPrimary": true,
        "vmInstanceCount": "[parameters('nt2InstanceCount')]"
    }
    ],
}
```

## <a name="migrate-to-using-availability-zones-from-a-cluster-using-a-basic-sku-load-balancer-and-a-basic-sku-ip"></a>若要使用可用性區域使用基本 SKU Load Balancer 與基本 SKU IP 從叢集移轉
若要移轉的叢集所使用的負載平衡器和 IP 與基本 SKU，您必須先建立使用標準 SKU 的整個新負載平衡器和 IP 資源。 您不可以更新這些資源進行就地。

新的 LB 和 IP 應該在新跨可用性區域節點類型中，您想要使用參考。 在上例中，三個新的虛擬機器擴展集資源已加入在區域 1、 2 和 3。 這些虛擬機器擴展集參考新建立的 LB 和 IP 和會標示為主要節點類型，在 Service Fabric 叢集資源。

若要開始，您必須將新的資源新增至您現有的 Resource Manager 範本。 這些資源包括：
* 使用標準 SKU 公用 IP 資源。
* 使用標準 SKU 負載平衡器資源。
* NSG，您可以在其中部署您的虛擬機器擴展集的子網路所參考。
* 三個節點類型標示為主要。
    * 每個節點類型應該對應至其本身位於不同區域中的虛擬機器擴展集。
    * 每個虛擬機器擴展集應該有至少 5 個節點 （Silver 持久性）。

可以在中找到這些資源的範例[範例範本](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/10-VM-Ubuntu-2-NodeType-Secure)。

```powershell
New-AzureRmResourceGroupDeployment `
    -ResourceGroupName $ResourceGroupName `
    -TemplateFile $Template `
    -TemplateParameterFile $Parameters
```

一旦資源部署完成，就可以停用的原始叢集的主要節點類型中的節點。 節點會停用，因為系統服務會移轉至新部署過上述步驟中的主要節點類型。

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint $ClusterName `
    -KeepAliveIntervalInSec 10 `
    -X509Credential `
    -ServerCertThumbprint $thumb  `
    -FindType FindByThumbprint `
    -FindValue $thumb `
    -StoreLocation CurrentUser `
    -StoreName My 

Write-Host "Connected to cluster"

$nodeNames = @("_nt0_0", "_nt0_1", "_nt0_2", "_nt0_3", "_nt0_4")

Write-Host "Disabling nodes..."
foreach($name in $nodeNames) {
    Disable-ServiceFabricNode -NodeName $name -Intent RemoveNode -Force
}
```

節點所有停用之後，系統服務將會執行主要節點型別，則會分散到區域中。 然後，您就可以從叢集移除已停用的節點。 一旦移除的節點，您可以移除原始的 IP、 負載平衡器，以及虛擬機器擴展集資源。

```powershell
foreach($name in $nodeNames){
    # Remove the node from the cluster
    Remove-ServiceFabricNodeState -NodeName $name -TimeoutSec 300 -Force
    Write-Host "Removed node state for node $name"
}

$scaleSetName="nt0"
Remove-AzureRmVmss -ResourceGroupName $groupname -VMScaleSetName $scaleSetName -Force

$lbname="LB-cluster-nt0"
$oldPublicIpName="LBIP-cluster-0"
$newPublicIpName="LBIP-cluster-1"

Remove-AzureRmLoadBalancer -Name $lbname -ResourceGroupName $groupname -Force
Remove-AzureRmPublicIpAddress -Name $oldPublicIpName -ResourceGroupName $groupname -Force
```

然後，您應該移除這些資源的參考，從已部署的 Resource Manager 範本。

最後一個步驟會需要更新的 DNS 名稱和公用 IP。

```powershell
$oldprimaryPublicIP = Get-AzureRmPublicIpAddress -Name $oldPublicIpName  -ResourceGroupName $groupname
$primaryDNSName = $oldprimaryPublicIP.DnsSettings.DomainNameLabel
$primaryDNSFqdn = $oldprimaryPublicIP.DnsSettings.Fqdn

Remove-AzureRmLoadBalancer -Name $lbname -ResourceGroupName $groupname -Force
Remove-AzureRmPublicIpAddress -Name $oldPublicIpName -ResourceGroupName $groupname -Force

$PublicIP = Get-AzureRmPublicIpAddress -Name $newPublicIpName  -ResourceGroupName $groupname
$PublicIP.DnsSettings.DomainNameLabel = $primaryDNSName
$PublicIP.DnsSettings.Fqdn = $primaryDNSFqdn
Set-AzureRmPublicIpAddress -PublicIpAddress $PublicIP

```

[sf-architecture]: ./media/service-fabric-cross-availability-zones/sf-cross-az-topology.png
