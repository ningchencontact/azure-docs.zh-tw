---
title: "設定負載平衡器使用 SQL Server Always On | Microsoft Docs"
description: "設定負載平衡器使用 SQL Always On，以及了解如何使用 PowerShell 來建立 SQL Server 實作的負載平衡器"
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
ms.assetid: d7bc3790-47d3-4e95-887c-c533011e4afd
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: 5e890f8314c8f191dbfa6c6818d810b91d0e829d
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/11/2017
---
# <a name="configure-a-load-balancer-for-sql-server-always-on"></a>設定負載平衡器使用 SQL Server Always On

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

SQL Server Always On 可用性群組現在可以使用內部負載平衡器執行。 可用性群組是 SQL Server 針對高可用性和災難復原的旗艦解決方案。 不論組態中的複本數目為何，可用性群組接聽程式可讓用戶端應用程式順暢地連接到主要複本。

接聽程式 (DNS) 名稱會與負載平衡 IP 位址對應。 Azure Load Balancer 只會將連入流量導向複本集中的主要伺服器。

您可以對 SQL Server Always On (接聽程式) 端點使用負載平衡器支援。 您現在可以控制接聽程式的可存取性。 您可以從虛擬網路中特定的子網路選擇負載平衡 IP 位址。

在接聽程式上使用內部負載平衡器之後，只有下列項目可以存取 SQL Server 端點 (例如，Server=tcp:ListenerName,1433;Database=DatabaseName)：

* 相同虛擬網路中的服務與 VM。
* 來自已連線內部部署網路的服務與 VM。
* 來自互連式虛擬網域的服務與 VM。

![內部負載平衡器 SQL Server Always On](./media/load-balancer-configure-sqlao/sqlao1.png)

## <a name="add-an-internal-load-balancer-to-the-service"></a>將內部負載平衡器新增至服務

1. 在下列範例中，您會設定一個包含名為 'Subnet-1' 之子網路的虛擬網路：

    ```powershell
    Add-AzureInternalLoadBalancer -InternalLoadBalancerName ILB_SQL_AO -SubnetName Subnet-1 -ServiceName SqlSvc
    ```
2. 在每個 VM 上新增內部負載平衡器的負載平衡端點。

    ```powershell
    Get-AzureVM -ServiceName SqlSvc -Name sqlsvc1 | Add-AzureEndpoint -Name "LisEUep" -LBSetName "ILBSet1" -Protocol tcp -LocalPort 1433 -PublicPort 1433 -ProbePort 59999 -ProbeProtocol tcp -ProbeIntervalInSeconds 10 -
    DirectServerReturn $true -InternalLoadBalancerName ILB_SQL_AO | Update-AzureVM

    Get-AzureVM -ServiceName SqlSvc -Name sqlsvc2 | Add-AzureEndpoint -Name "LisEUep" -LBSetName "ILBSet1" -Protocol tcp -LocalPort 1433 -PublicPort 1433 -ProbePort 59999 -ProbeProtocol tcp -ProbeIntervalInSeconds 10 -DirectServerReturn $true -InternalLoadBalancerName ILB_SQL_AO | Update-AzureVM
    ```

    在上述範例中，您有在雲端服務 "Sqlsvc" 中執行的兩個 VM，稱為 "sqlsvc1" 和 "sqlsvc2"。 使用 `DirectServerReturn` 參數建立內部負載平衡器後，您可以將負載平衡的端點新增至內部負載平衡器。 負載平衡的端點允許 SQL Server 設定用於可用性群組的接聽程式。

如需有關 SQL Server Always On 的詳細資訊，請參閱[針對 Azure 中的 Always On 可用性群組設定內部負載平衡器](../virtual-machines/windows/sql/virtual-machines-windows-portal-sql-alwayson-int-listener.md)。

## <a name="see-also"></a>另請參閱
* [開始設定公用負載平衡器](load-balancer-get-started-internet-arm-ps.md)
* [開始設定內部負載平衡器](load-balancer-get-started-ilb-arm-ps.md)
* [設定負載平衡器分配模式](load-balancer-distribution-mode.md)
* [設定負載平衡器的閒置 TCP 逾時設定](load-balancer-tcp-idle-timeout.md)
