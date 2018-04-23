---
title: 從遠端連線 Azure Service Fabric 叢集節點 | Microsoft Docs
description: 了解如何從遠端連線擴展集執行個體 (Service Fabric 叢集節點)。
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: timlt
editor: ''
ms.assetid: 5441e7e0-d842-4398-b060-8c9d34b07c48
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/23/2018
ms.author: aljo
ms.openlocfilehash: 68e3b8ae5bdaa3ad9f1c470294ef5c3bcf0c1893
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2018
---
# <a name="remote-connect-to-a-virtual-machine-scale-set-instance-or-a-cluster-node"></a>遠端連線到虛擬機器擴展集執行個體或叢集節點
在 Azure 中執行的 Service Fabric 叢集內，您定義的每個叢集節點類型都會[設定虛擬機器的個別擴展](service-fabric-cluster-nodetypes.md)。  您可以從遠端連線特定的擴展集執行個體 (或叢集節點)。  不同於單一執行個體的 VM，VM 擴展集的執行個體不會有自己的虛擬 IP 位址。 當您要尋找可用來遠端連線至特定執行個體的 IP 位址和連接埠時，可能有點困難。

若要找出可用來遠端連線至特定執行個體的 IP 位址和連接埠，請完成下列步驟。

1. 取得遠端桌面通訊協定 (RDP) 的輸入 NAT 規則，找出該節點類型的虛擬 IP 位址。

    首先，取得 `Microsoft.Network/loadBalancers` 的資源定義中定義的輸入 NAT 規則值。
    
    在 Azure 入口網站的負載平衡器頁面上，選取 [設定]  >  [輸入 NAT 規則]。 這會給您可用來遠端連線至第一個 擴展集執行個體的 IP 位址和連接埠。 
    
    ![負載平衡器][LBBlade]
    
    在下圖中，IP 位址和連接埠是 **104.42.106.156** 和 **3389**。
    
    ![NAT 規則][NATRules]

2. 找到可用來遠端連線至特定擴展集執行個體或節點的連接埠。

    擴展集執行個體對應至節點。 使用擴展集資訊判斷要使用哪一個通訊埠。
    
    連接埠是以和擴展集執行個體相符的遞增順序配置。 以之前的 FrontEnd 節點類型為例，下表列出這五個節點執行個體的連接埠。 對您的擴展集執行個體套用相同的對應。
    
    | **虛擬機器擴展集執行個體** | **連接埠** |
    | --- | --- |
    | FrontEnd_0 |3389 |
    | FrontEnd_1 |3390 |
    | FrontEnd_2 |3391 |
    | FrontEnd_3 |3392 |
    | FrontEnd_4 |3393 |
    | FrontEnd_5 |3394 |

3. 遠端連線到特定擴展集執行個體。

    下圖示範使用「遠端桌面連線」連線到 FrontEnd_1 擴展集執行個體：
    
    ![遠端桌面連線][RDP]


若要了解後續步驟，請閱讀下列文章：
* 請參閱[「到處部署」功能和與 Azure 受控叢集比較的概觀](service-fabric-deploy-anywhere.md)。
* 了解[叢集安全性](service-fabric-cluster-security.md)。
* 部署後在叢集 VM 上[更新 RDP 連接埠範圍值](./scripts/service-fabric-powershell-change-rdp-port-range.md)
* 變更叢集 VM 的[管理員使用者名稱和密碼](./scripts/service-fabric-powershell-change-rdp-user-and-pw.md)

<!--Image references-->
[LBBlade]: ./media/service-fabric-cluster-remote-connect-to-azure-cluster-node/LBBlade.png
[NATRules]: ./media/service-fabric-cluster-remote-connect-to-azure-cluster-node/NATRules.png
[RDP]: ./media/service-fabric-cluster-remote-connect-to-azure-cluster-node/RDP.png
