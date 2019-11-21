---
title: Azure Resource Manager support for Load Balancer
description: In this article, use Azure PowerShell and templates with Azure Load Balancer
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
# <a name="azure-resource-manager-support-with-azure-load-balancer"></a>Azure Resource Manager support with Azure Load Balancer



Azure Resource Manager 是 Azure 中慣用的服務管理架構。 Azure Load Balancer 可使用以 Azure Resource Manager 為基礎的 API 和工具進行管理。

## <a name="concepts"></a>概念

使用 Resource Manager 時，Azure Load Balancer 會包含下列子資源：

* Front-end IP configuration – a load balancer can include one or more frontend IP addresses, otherwise known as a virtual IPs (VIPs). 這些 IP 位址做為流量的輸入。
* Back-end address pool – This pool is a collection of IP addresses associated with the virtual machine Network Interface Card (NIC) to which load is distributed.
* Load-balancing rules – a rule property maps a given frontend IP and port combination to a set of back-end IP addresses and port combination. A single load balancer can have multiple load-balancing rules. Each rule is a combination of a frontend IP and port and back-end IP and port associated with VMs.
* 探查 - 探查可讓您追蹤 VM 執行個體的健全狀況。 如果健康情況探查失敗，則 VM 執行個體不會自動進入輪替。
* Inbound NAT rules – NAT rules defining the inbound traffic flowing through the frontend IP and distributed to the back-end IP.

![](./media/load-balancer-arm/load-balancer-arm.png)

## <a name="quickstart-templates"></a>快速入門範本

Azure Resource Manager 允許您使用宣告式範本提供應用程式。 在單一範本中，您可以部署多項服務及其相依性。 您可使用相同的範本，在應用程式生命週期的每一個階段中重複部署應用程式。

Templates may include definitions for:
* **虛擬機器**
* **虛擬網路**
* **可用性設定組**
* **Network interfaces (NICs)**
* **儲存體帳戶**
* **負載平衡器**
* **網路安全性群組**
* **Public IPs.** 

您可以使用範本來建立複雜應用程式所需的一切。 您可以將範本檔案簽入內容管理系統中，以進行版本控制和共同作業。

[深入了解範本](../azure-resource-manager/resource-manager-template-walkthrough.md)

[深入了解網路資源](../networking/networking-overview.md)

For Quickstart templates using Azure Load Balancer, see the [GitHub repository](https://github.com/Azure/azure-quickstart-templates) that hosts a set of community-generated templates.

範本的範例：

* [負載平衡器中的 2 部 VM 和負載平衡規則](https://go.microsoft.com/fwlink/?LinkId=544799)
* [2 VMs in a VNET with an Internal Load Balancer and load balancer rules](https://go.microsoft.com/fwlink/?LinkId=544800)
* [2 VMs in a load balancer and configure NAT rules on the LB](https://go.microsoft.com/fwlink/?LinkId=544801)

## <a name="setting-up-azure-load-balancer-with-a-powershell-or-cli"></a>使用 PowerShell 或 CLI 設定 Azure 負載平衡器

Get started with Azure Resource Manager cmdlets, command-line tools, and REST APIs

* [Azure 網路 Cmdlet](https://docs.microsoft.com/powershell/module/az.network#networking) 可用來建立負載平衡器。
* [如何使用 Azure 資源管理員建立負載平衡器](load-balancer-get-started-ilb-arm-ps.md)
* [搭配使用 Azure 資源管理與 Azure CLI](../xplat-cli-azure-resource-manager.md)
* [負載平衡器 REST API](https://msdn.microsoft.com/library/azure/mt163651.aspx)

## <a name="next-steps"></a>後續步驟

[Get started creating an Internet facing load balancer](load-balancer-get-started-internet-arm-ps.md) and configure the type of [distribution mode](load-balancer-distribution-mode.md) for specific network traffic behavior.

了解如何管理 [負載平衡器的閒置 TCP 逾時設定](load-balancer-tcp-idle-timeout.md)。 These settings are important when your application needs to keep connections alive for servers behind a load balancer.
