---
title: 使用 Azure PowerShell 設定負載平衡和輸出規則
titlesuffix: Azure Load Balancer
description: 本文說明如何使用 Azure PowerShell 在 Standard Load Balancer 中設定負載平衡和輸出規則。
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: article
ms.date: 09/24/2019
ms.author: allensu
ms.openlocfilehash: 6e85d31e40e35b9d796fc66394a6eb446c7302ad
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2019
ms.locfileid: "71262618"
---
# <a name="configure-load-balancing-and-outbound-rules-in-standard-load-balancer-using-azure-powershell"></a>使用 Azure PowerShell 在標準負載平衡器中設定負載平衡和輸出規則

本文說明如何使用 Azure PowerShell 設定標準負載平衡器中的輸出規則。  

當您完成時，負載平衡器資源會包含兩個前端和其相關聯的規則：一個用於輸入，另一個用於輸出。  每個前端都有公用 IP 位址的參考，本案例會對輸入與輸出使用不同的公用 IP 位址。   負載平衡規則只會提供輸入負載平衡，輸出規則則可控制提供給 VM 的輸出 NAT。  本文使用兩個不同的後端集區，一個用於輸入，另一個用於輸出，以說明功能並允許在此案例中提供彈性。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="connect-to-azure-account"></a>連接到 Azure 帳戶
使用[disconnect-azaccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0)命令登入您的 Azure 訂用帳戶，並遵循畫面上的指示：
    
```azurepowershell-interactive
Connect-AzAccount
```
## <a name="create-resource-group"></a>建立資源群組

使用 [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0) 來建立資源群組。 Azure 資源群組是在其中部署與管理 Azure 資源的邏輯容器。

下列範例會在 eastus2 位置建立名為 myresourcegroupoutbound 的資源群組：

```azurepowershell-interactive
New-AzResourceGroup -Name myresourcegroupoutbound -Location eastus
```
## <a name="create-virtual-network"></a>建立虛擬網路
使用[new-azvirtualnetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork?view=azps-2.6.0)和[new-new-azvirtualnetworksubnetconfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig?view=azps-2.6.0)，在*myresourcegroupoutbound*中建立名為*myvnetoutbound*的虛擬網路，並將該子網命名為*myresourcegroupoutbound* ：

```azurepowershell-interactive
$subnet = New-AzVirtualNetworkSubnetConfig -Name mysubnetoutbound -AddressPrefix "192.168.0.0/24"

New-AzVirtualNetwork -Name myvnetoutbound -ResourceGroupName myresourcegroupoutbound -Location eastus -AddressPrefix "192.168.0.0/16" -Subnet $subnet
```

## <a name="create-inbound-public-ip-address"></a>建立輸入公用 IP 位址 

若要在網際網路上存取您的 Web 應用程式，您需要負載平衡器的公用 IP 位址。 標準負載平衡器只支援標準公用 IP 位址。 使用[get-azpublicipaddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress?view=azps-2.6.0)在*myresourcegroupoutbound*中建立名為*mypublicipinbound*的標準公用 IP 位址。

```azurepowershell-interactive
$pubIPin = New-AzPublicIpAddress -ResourceGroupName myresourcegroupoutbound -Name mypublicipinbound -AllocationMethod Static -Sku Standard -Location eastus
```

## <a name="create-outbound-public-ip-address"></a>建立輸出公用 IP 位址 

使用[get-azpublicipaddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress?view=azps-2.6.0)建立負載平衡器前端輸出設定的標準 IP 位址。

```azurepowershell-interactive
$pubIPout = New-AzPublicIpAddress -ResourceGroupName myresourcegroupoutbound -Name mypublicipoutbound -AllocationMethod Static -Sku Standard -Location eastus
```

## <a name="create-azure-load-balancer"></a>建立 Azure Load Balancer

本節將詳細說明如何建立及設定下列負載平衡器元件：
  - 前端 IP，可接收負載平衡器上的連入網路流量。
  - 後端集區, 前端 IP 會在其中傳送負載平衡的網路流量。
  - 輸出連線能力的後端集區。 
  - 健康狀態探查，可判斷後端 VM 執行個體的健康狀態。
  - 負載平衡器輸入規則，可定義如何將流量分散至 VM。
  - 負載平衡器輸出規則，可定義如何從 VM 分散流量。

### <a name="create-inbound-frontend-ip"></a>建立輸入前端 IP
使用[new-azloadbalancerfrontendipconfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerfrontendipconfig?view=azps-2.6.0)建立負載平衡器的輸出前端 ip 設定，其中包含名為*myfrontendinbound*且與公用 IP 位址*相關聯的輸入前端 ip 設定mypublicipinbound*

```azurepowershell-interactive
$frontendIPin = New-AzLoadBalancerFrontendIPConfig -Name "myfrontendinbound" -PublicIpAddress $pubIPin
```
### <a name="create-outbound-frontend-ip"></a>建立輸出前端 IP
使用[new-azloadbalancerfrontendipconfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerfrontendipconfig?view=azps-2.6.0)建立負載平衡器的輸出前端 ip 設定，其中包含名為*myfrontendoutbound*且與公用 IP 位址*相關聯的輸出前端 ip 設定mypublicipoutbound*：

```azurepowershell-interactive
$frontendIPout = New-AzLoadBalancerFrontendIPConfig -Name "myfrontendoutbound" -PublicIpAddress $pubIPout
```
### <a name="create-inbound-backend-pool"></a>建立輸入後端集區
使用名為*bepoolinbound*的[新 new-azloadbalancerbackendaddresspoolconfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig?view=azps-2.6.0) ，為負載平衡器建立後端輸入集區：

```azurepowershell-interactive
$bepoolin = New-AzLoadBalancerBackendAddressPoolConfig -Name bepoolinbound
``` 

### <a name="create-outbound-backend-pool"></a>建立輸出後端集區
建立額外的後端位址集區，以定義名為*bepooloutbound*之[new-azloadbalancerbackendaddresspoolconfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig?view=azps-2.6.0) vm 集區的輸出連線能力。 建立個別的輸出集區可提供最大的彈性, 但您也可以省略此步驟, 也只能使用輸入*bepoolinbound* 。  :

```azurepowershell-interactive
$bepoolout = New-AzLoadBalancerBackendAddressPoolConfig -Name bepooloutbound
``` 

### <a name="create-health-probe"></a>建立健全狀況探查

健全狀況探查會檢查所有虛擬機器執行個體，確認它們可以傳送網路流量。 探查檢查失敗的虛擬機器執行個體會從負載平衡器上移除，直到其恢復正常運作且探查判斷其健全狀況良好為止。 使用[new-azloadbalancerprobeconfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerprobeconfig?view=azps-2.6.0)建立健康狀態探查，以監視虛擬機器的健康情況。 

```azurepowershell-interactive
$probe = New-AzLoadBalancerProbeConfig -Name http -Protocol "http" -Port 80 -IntervalInSeconds 15 -ProbeCount 2 -RequestPath /
```
### <a name="create-load-balancing-rule"></a>建立負載平衡規則

負載平衡器規則可定義連入流量的前端 IP 組態及接收流量的後端集區，以及所需的來源和目的地連接埠。 建立具有[add-azloadbalancerruleconfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerruleconfig?view=azps-2.6.0)的負載平衡器規則*myinboundlbrule* ，以接聽前端集區中的埠 80 *myfrontendinbound* ，並將負載平衡的網路流量傳送到後端位址集區*bepoolinbound*也使用埠80。 

>[!NOTE]
>此負載平衡規則會透過 **-DisableOutboundSNAT**參數，停用自動輸出（S） NAT 作為此規則的結果。 只有輸出規則會提供輸出 NAT。

```azurepowershell-interactive
$inboundRule = New-AzLoadBalancerRuleConfig -Name inboundlbrule -FrontendIPConfiguration $frontendIPin -BackendAddressPool $bepoolin -Probe $probe -Protocol "Tcp" -FrontendPort 80 -BackendPort 80 -IdleTimeoutInMinutes 15 -EnableFloatingIP -LoadDistribution SourceIP -DisableOutboundSNAT
```

### <a name="create-outbound-rule"></a>建立輸出規則

輸出規則會定義前端公用 IP (由前端 myfrontendoutbound 來表示)，以供用於所有輸出 NAT 流量以及要套用此規則的後端集區。  建立輸出規則 myoutboundrule 以便轉譯 bepool 後端集區中所有虛擬機器 的輸出網路 (NIC IP 組態)。  下列命令也會將輸出閒置逾時從 4 分鐘變更為 15 分鐘，並配置 10000 個 SNAT 連接埠，而不是 1024 個。  如需詳細資訊，請參閱[AzLoadBalancerOutboundRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalanceroutboundruleconfig?view=azps-2.7.0) 。

```azurepowershell-interactive
 $outboundRule = New-AzLoadBalancerOutBoundRuleConfig -Name outboundrule -FrontendIPConfiguration $frontendIPout -BackendAddressPool $bepoolout -Protocol All -IdleTimeoutInMinutes 15 -AllocatedOutboundPort 10000
```
如果您不想要使用個別的輸出集區，您可以變更上述命令中的位址集區引數，改為指定 *$bepoolin* 。  我們建議使用不同的集區，以提供所產生設定的彈性和可讀性。

### <a name="create-load-balancer"></a>建立負載平衡器

使用名為*lb*的[remove-azloadbalancer](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancer?view=azps-2.6.0)建立具有輸入 ip 位址的負載平衡器，其中包含與公用 IP 位址 *相關聯的輸入前端 IP 設定和後端集區 bepoolinbound*您在上一個步驟中建立的 mypublicipinbound。

```azurepowershell-interactive
New-AzLoadBalancer -Name lb -Sku Standard -ResourceGroupName myresourcegroupoutbound -Location eastus -FrontendIpConfiguration $frontendIPin,$frontendIPout -BackendAddressPool $bepoolin,$bepoolout -Probe $probe -LoadBalancingRule $inboundrule -OutboundRule $outboundrule 
```

此時，您可以使用[Add-AzNetworkInterfaceIpConfig](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest)來更新個別 NIC 資源的 IP 設定，以繼續將 vm 新增至後端集區*bepoolinbound* __和__ *bepooloutbound* 。

## <a name="clean-up-resources"></a>清除資源

若不再需要，您可以使用[remove-azresourcegroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.7.0)命令來移除資源群組、負載平衡器和所有相關資源。

```azurepowershell-interactive 
  Remove-AzResourceGroup -Name myresourcegroupoutbound
```

## <a name="next-steps"></a>後續步驟
在本文中，您已建立標準負載平衡器，為後端集區中的 Vm 設定了輸入負載平衡器流量規則、設定和健康情況探查。 若要深入瞭解 Azure Load Balancer，請繼續進行 Azure 負載平衡器的教學課程。

> [!div class="nextstepaction"]
> [Azure Load Balancer 教學課程](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
