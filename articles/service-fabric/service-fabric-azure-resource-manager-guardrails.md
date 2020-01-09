---
title: Service Fabric Azure Resource Manager 部署護欄
description: 本文概述透過 Azure Resource Manager 部署 Service Fabric 叢集時所發生的常見錯誤，以及如何避免這些問題。
services: service-fabric
documentationcenter: .net
author: peterpogorski
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: pepogors
ms.openlocfilehash: fe5ff2a5eeb4b2c73165d1577702eb6af7079b61
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/25/2019
ms.locfileid: "75426746"
---
# <a name="service-fabric-guardrails"></a>Service Fabric 護欄 
部署 Service Fabric 叢集時，會將護欄置中，這將會在叢集設定無效時，使 Azure Resource Manager 部署失敗。 下列各節提供常見叢集設定問題的總覽，以及減輕這些問題所需的步驟。 

## <a name="durability-mismatch"></a>持久性不符
### <a name="overview"></a>概觀
Service Fabric 節點類型的持久性值定義于 Azure Resource Manager 範本的兩個不同區段中。 虛擬機器擴展集資源的虛擬機器擴展集擴充功能區段，以及 Service Fabric 叢集資源的節點類型區段。 這些區段中的持久性值必須相符，否則資源部署將會失敗。

下一節包含虛擬機器擴展集擴充功能耐久性設定與 Service Fabric 節點類型耐久性設定之間的持久性不符範例：  

**虛擬機器擴展集耐久性設定**
```json 
{
  "extensions": [
    {
      "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
      "properties": {
        "type": "ServiceFabricNode",
        "publisher": "Microsoft.Azure.ServiceFabric",
        "settings": {
          "durabilityLevel": "Silver",
        }
      }
    }
  ]
}
```

**Service Fabric 節點類型耐久性設定** 
```json
{
  "nodeTypes": [
    {
      "name": "[variables('vmNodeType0Name')]",
      "durabilityLevel": "Bronze",
      "isPrimary": true,
      "vmInstanceCount": "[parameters('nt0InstanceCount')]"
    }
  ]
}
```

### <a name="error-messages"></a>錯誤訊息
* 虛擬機器擴展集耐久性不相符不符合目前的 Service Fabric 節點類型耐久性層級
* 虛擬機器擴展集耐久性與目標 Service Fabric 節點類型耐久性層級不相符
* 虛擬機器擴展集耐久性符合目前的 Service Fabric 耐久性層級或目標 Service Fabric 節點類型耐久性層級 


### <a name="mitigation"></a>降低
若要修正持久性不相符的問題，請使用上述任何錯誤訊息來表示：
1. 更新 [虛擬機器擴展集延伸模組] 或 [Azure Resource Manager 範本 Service Fabric 節點類型] 區段中的持久性層級，以確保這些值相符。
2. 使用更新的值重新部署 Azure Resource Manager 範本。

## <a name="next-steps"></a>後續步驟
* 在執行 Windows Server 的 VM 或電腦上建立叢集： [建立適用於 Windows Server 的 Service Fabric 叢集](service-fabric-cluster-creation-for-windows-server.md)
* 在 VM 或執行 Linux 的電腦上建立叢集：[建立 Linux 叢集](service-fabric-cluster-creation-via-portal.md)
* 疑難排解 Service Fabric：[疑難排解指南](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides)
