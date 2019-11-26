---
title: Load Balancer 的 Azure Resource Manager 支援
description: 在本文中，使用 Azure PowerShell 和範本搭配 Azure Load Balancer
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/19/2019
ms.author: allensu
ms.openlocfilehash: 57a60a65dee995695224aa1b451e848ea8768ab1
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/20/2019
ms.locfileid: "74215395"
---
# <a name="azure-resource-manager-support-with-azure-load-balancer"></a>Azure Load Balancer 的 Azure Resource Manager 支援



Azure Resource Manager 是 Azure 中慣用的服務管理架構。 Azure Load Balancer 可使用以 Azure Resource Manager 為基礎的 API 和工具進行管理。

## <a name="concepts"></a>概念

使用 Resource Manager 時，Azure Load Balancer 會包含下列子資源：

* 前端 IP 設定-負載平衡器可以包含一或多個前端 IP 位址，又稱為虛擬 Ip （Vip）。 這些 IP 位址做為流量的輸入。
* 後端位址集區–此集區是與虛擬機器網路介面卡（NIC）相關聯的 IP 位址集合，負載會分散到此。
* 負載平衡規則-規則屬性會將指定的前端 IP 和埠組合對應到一組後端 IP 位址和埠組合。 單一負載平衡器可以有多個負載平衡規則。 每個規則都是與 Vm 相關聯的前端 IP 和埠以及後端 IP 和埠的組合。
* 探查 - 探查可讓您追蹤 VM 執行個體的健全狀況。 如果健康情況探查失敗，則 VM 執行個體不會自動進入輪替。
* 輸入 NAT 規則-定義流經前端 IP 並散發到後端 IP 之輸入流量的 NAT 規則。

![](./media/load-balancer-arm/load-balancer-arm.png)

## <a name="quickstart-templates"></a>快速入門範本

Azure 資源管理員可讓您使用宣告式範本佈建應用程式。 在單一的範本中，您可以部署多個服務及其相依性。 您可以使用相同的範本，在應用程式生命週期的每個階段重複部署應用程式。

範本可能包含下列定義：
* **虛擬機器**
* **虛擬網路**
* **可用性設定組**
* **網路介面（Nic）**
* **儲存體帳戶**
* **負載平衡器**
* **網路安全性群組**
* **公用 Ip。** 

您可以使用範本來建立複雜應用程式所需的一切。 您可以將範本檔案簽入內容管理系統中，以進行版本控制和共同作業。

[深入了解範本](../azure-resource-manager/resource-manager-template-walkthrough.md)

[深入了解網路資源](../networking/networking-overview.md)

如需使用 Azure Load Balancer 的快速入門範本，請參閱裝載一組由社區產生之範本的[GitHub 存放庫](https://github.com/Azure/azure-quickstart-templates)。

範本的範例：

* [負載平衡器中的 2 部 VM 和負載平衡規則](https://go.microsoft.com/fwlink/?LinkId=544799)
* [具有內部 Load Balancer 和負載平衡器規則的 VNET 中的2部 Vm](https://go.microsoft.com/fwlink/?LinkId=544800)
* [負載平衡器中的2部 Vm，並在 LB 上設定 NAT 規則](https://go.microsoft.com/fwlink/?LinkId=544801)

## <a name="setting-up-azure-load-balancer-with-a-powershell-or-cli"></a>使用 PowerShell 或 CLI 設定 Azure 負載平衡器

開始使用 Azure Resource Manager Cmdlet、命令列工具和 REST Api

* [Azure 網路 Cmdlet](https://docs.microsoft.com/powershell/module/az.network#networking) 可用來建立負載平衡器。
* [如何使用 Azure 資源管理員建立負載平衡器](load-balancer-get-started-ilb-arm-ps.md)
* [搭配使用 Azure 資源管理與 Azure CLI](../xplat-cli-azure-resource-manager.md)
* [負載平衡器 REST API](https://msdn.microsoft.com/library/azure/mt163651.aspx)

## <a name="next-steps"></a>後續步驟

[開始建立網際網路面向的負載平衡器](load-balancer-get-started-internet-arm-ps.md)，並針對特定的網路流量行為設定[分配模式](load-balancer-distribution-mode.md)的類型。

了解如何管理 [負載平衡器的閒置 TCP 逾時設定](load-balancer-tcp-idle-timeout.md)。 當您的應用程式需要讓負載平衡器後方的伺服器保持連線時，這些設定就很重要。
