---
title: Azure Service Fabric 網路功能最佳做法 | Microsoft Docs
description: 管理 Service Fabric 網路功能的最佳做法。
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
ms.date: 01/23/2019
ms.author: pepogors
ms.openlocfilehash: 317977af9d41163013545a6e5f60bee887da596c
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2019
ms.locfileid: "71262253"
---
# <a name="networking"></a>網路功能

當您建立和管理 Azure Service Fabric 叢集時，您會為節點和應用程式提供網路連線。 這些網路資源包括 IP 位址範圍、虛擬網路、負載平衡器和網路安全性群組。 在本文中，您將了解這些資源的最佳做法。

請檢閱 Azure [Service Fabric 網路功能模式](https://docs.microsoft.com/azure/service-fabric/service-fabric-patterns-networking)，以了解如何建立使用下列功能的叢集：現有的虛擬網路或子網路、靜態公用 IP 位址、僅限內部的負載平衡器，或內部和外部負載平衡器。

## <a name="infrastructure-networking"></a>基礎結構網路功能
藉由在 Resource Manager 範本中宣告 enableAcceleratedNetworking 屬性，使用加速網路將虛擬機器的效能最大化。下列程式碼片段用於啟用加速網路的虛擬機器擴展集 NetworkInterfaceConfigurations：

```json
"networkInterfaceConfigurations": [
  {
    "name": "[concat(variables('nicName'), '-0')]",
    "properties": {
      "enableAcceleratedNetworking": true,
      "ipConfigurations": [
        {
        <snip>
        }
      ],
      "primary": true
    }
  }
]
```
Service Fabric 叢集可以佈建在[使用加速網路的 Linux](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli) 上，以及[使用加速網路的 Windows](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell) 上。

以下是可支援加速網路的 Azure 虛擬機器系列 SKU：D/DSv2、D/DSv3、E/ESv3、F/FS、FSv2 和 Ms/Mms。 加速網路已於 1/23/2019 使用 Standard_DS8_v3 SKU 針對 Service Fabric Windows 叢集成功進行測試，並於 01/29/2019 使用 Standard_DS12_v2 針對 Service Fabric Linux 叢集成功進行測試。

若要在現有的 Service Fabric 叢集上啟用加速網路，您必須先[透過新增虛擬機器擴展集將 Service Fabric 叢集相應放大](https://docs.microsoft.com/azure/service-fabric/virtual-machine-scale-set-scale-node-type-scale-out)，以執行下列動作：
1. 佈建已啟用加速網路的 NodeType
2. 將您的服務及其狀態移轉至已佈建並啟用加速網路的 NodeType

要將基礎結構相應放大，必須在現有的叢集上啟用加速網路，因為要就地啟用加速網路，必須將可用性設定組中的所有虛擬機器全部[停止和解除配置，才能在任何現有的 NIC 上啟用加速網路](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli#enable-accelerated-networking-on-existing-vms)，因此會導致停機時間。

## <a name="cluster-networking"></a>叢集網路功能

* Service Fabric 叢集可依照 [Service Fabric 網路功能模式](https://docs.microsoft.com/azure/service-fabric/service-fabric-patterns-networking)中說明的步驟部署到現有的虛擬網路中。

* 對於限制叢集的輸入和輸出流量的節點類型，建議使用網路安全性群組 (NSG)。 請確實在 NSG 中開啟必要的連接埠。 例如: ![Service Fabric NSG 規則][NSGSetup]

* 主要節點類型包含 Service Fabric 系統服務，因此不需要透過外部負載平衡器來公開，而可由[內部負載平衡器](https://docs.microsoft.com/azure/service-fabric/service-fabric-patterns-networking#internal-only-load-balancer)來公開

* 為您的叢集建立[靜態公用 IP 位址](https://docs.microsoft.com/azure/service-fabric/service-fabric-patterns-networking#static-public-ip-address-1)。

## <a name="application-networking"></a>應用程式網路功能

* 若要執行 Windows 容器工作負載，請使用[開放網路模式](https://docs.microsoft.com/azure/service-fabric/service-fabric-networking-modes#set-up-open-networking-mode)，以便進行服務對服務的通訊。

* 使用反向 Proxy (例如 [Traefik](https://docs.traefik.io/v1.6/configuration/backends/servicefabric/) 或 [Service Fabric 反向 Proxy](https://docs.microsoft.com/azure/service-fabric/service-fabric-reverseproxy)) 公開常用的應用程式連接埠，例如 80 或 443。

* 針對在無法從 Azure 雲端儲存體提取基本層的空中電腦上裝載的 Windows 容器，請使用 Docker daemon 中的[--nondistributable-構件](https://docs.microsoft.com/virtualization/windowscontainers/about/faq#how-do-i-make-my-container-images-available-on-air-gapped-machines)旗標來覆寫外部層行為。

## <a name="next-steps"></a>後續步驟

* 在 VM 或執行 Windows Server 的電腦上建立叢集：[適用於 Windows Server 的 Service Fabric 叢集建立](service-fabric-cluster-creation-for-windows-server.md)
* 在 VM 或執行 Linux 的電腦上建立叢集：[建立 Linux 叢集](service-fabric-cluster-creation-via-portal.md)
* 了解 [Service Fabric 支援選項](service-fabric-support.md)

[NSGSetup]: ./media/service-fabric-best-practices/service-fabric-nsg-rules.png
