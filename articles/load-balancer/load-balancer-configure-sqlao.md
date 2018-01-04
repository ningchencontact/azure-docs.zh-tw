---
title: "設定負載平衡器適用於 SQL Server Alwayson |Microsoft 文件"
description: "負載平衡器設定為使用 SQL Server Always On，並了解如何使用 PowerShell 建立的 SQL Server 實作負載平衡器"
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
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/11/2017
---
# <a name="configure-a-load-balancer-for-sql-server-always-on"></a>SQL Server Alwayson 設定負載平衡器

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

SQL Server Always On 可用性群組現在可以執行內部負載平衡器。 可用性群組是 SQL Server 的高可用性和災害復原的旗艦級解決方案。 可用性群組接聽程式可讓用戶端應用程式順暢地連線到主要複本，不論是在組態中的複本數目。

接聽程式 (DNS) 名稱對應到負載平衡 IP 位址。 Azure 負載平衡器會將只在主要伺服器連入流量導向複本組中。

您可以使用內部負載平衡器支援 SQL Server Always On （接聽程式） 端點。 您現在可以控制接聽程式的存取範圍。 您可以選擇從特定的子網路的負載平衡 IP 位址，虛擬網路中。

使用內部負載平衡器上的接聽程式，SQL Server 端點 (例如，伺服器 = tcp:ListenerName，1433; Database = DatabaseName) 只能藉由存取：

* 服務和 Vm 相同的虛擬網路中。
* 服務和 Vm 從連線到的內部網路。
* 服務和 Vm 從互連的虛擬網路。

![SQL Server Alwayson 的內部負載平衡器](./media/load-balancer-configure-sqlao/sqlao1.png)

## <a name="add-an-internal-load-balancer-to-the-service"></a>內部負載平衡器新增至服務

1. 在下列範例中，您可以設定虛擬網路，包含名稱為 ' 的子網路 1' 的子網路：

    ```powershell
    Add-AzureInternalLoadBalancer -InternalLoadBalancerName ILB_SQL_AO -SubnetName Subnet-1 -ServiceName SqlSvc
    ```
2. 每個 VM 上加入內部負載平衡器負載平衡的端點。

    ```powershell
    Get-AzureVM -ServiceName SqlSvc -Name sqlsvc1 | Add-AzureEndpoint -Name "LisEUep" -LBSetName "ILBSet1" -Protocol tcp -LocalPort 1433 -PublicPort 1433 -ProbePort 59999 -ProbeProtocol tcp -ProbeIntervalInSeconds 10 -
    DirectServerReturn $true -InternalLoadBalancerName ILB_SQL_AO | Update-AzureVM

    Get-AzureVM -ServiceName SqlSvc -Name sqlsvc2 | Add-AzureEndpoint -Name "LisEUep" -LBSetName "ILBSet1" -Protocol tcp -LocalPort 1433 -PublicPort 1433 -ProbePort 59999 -ProbeProtocol tcp -ProbeIntervalInSeconds 10 -DirectServerReturn $true -InternalLoadBalancerName ILB_SQL_AO | Update-AzureVM
    ```

    在上述範例中，您有兩個 Vm 稱為 「 sqlsvc1"和"sqlsvc2 「 執行中雲端服務 」 Sqlsvc"。 建立具有內部負載平衡器後`DirectServerReturn`切換時，您將負載平衡的端點加入至內部負載平衡器。 負載平衡的端點可讓 SQL Server 來設定可用性群組接聽程式。

如需 SQL Server Alwayson 的詳細資訊，請參閱[在 Azure 中設定 Alwayson 可用性群組的內部負載平衡器](../virtual-machines/windows/sql/virtual-machines-windows-portal-sql-alwayson-int-listener.md)。

## <a name="see-also"></a>請參閱
* [開始設定公用負載平衡器](load-balancer-get-started-internet-arm-ps.md)
* [開始設定內部負載平衡器](load-balancer-get-started-ilb-arm-ps.md)
* [設定負載平衡器分配模式](load-balancer-distribution-mode.md)
* [設定負載平衡器的閒置 TCP 逾時設定](load-balancer-tcp-idle-timeout.md)
