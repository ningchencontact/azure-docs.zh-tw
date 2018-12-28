---
title: 設定 Global Reach - ExpressRoute：Azure | Microsoft Docs
description: 本文可協助您將 ExpressRoute 線路連結在一起，在內部部署網路之間產生私人網路，並使觸角擴及全球。
services: expressroute
author: mialdrid
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/13/2018
ms.author: mialdrid
ms.custom: seodec18
ms.openlocfilehash: 3df107f8854469b50c5e8483515388b5c93fb244
ms.sourcegitcommit: 85d94b423518ee7ec7f071f4f256f84c64039a9d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/14/2018
ms.locfileid: "53383267"
---
# <a name="configure-expressroute-global-reach-preview"></a>設定 ExpressRoute Global Reach (預覽)
本文可協助您使用 PowerShell 設定 ExpressRoute Global Reach。 如需詳細資訊，請參閱 [ExpressRouteRoute Global Reach](expressroute-global-reach.md)。
 
## <a name="before-you-begin"></a>開始之前
> [!IMPORTANT]
> 此公開預覽版是在沒有服務等級協定的情況下提供，不應用於生產環境工作負載。 特定功能可能不受支援、功能可能受限，或者可能無法在所有 Azure 位置提供使用。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。
> 


在開始進行設定之前，請先確認下列情況︰

* 您已安裝最新版 Azure PowerShell。 如需詳細資訊，請參閱[安裝和設定 Azure PowerShell](/powershell/azure/install-azurerm-ps)。
* 您了解 ExpressRoute 線路佈建[工作流程](expressroute-workflows.md)。
* 您的 ExpressRoute 線路處於已佈建狀態。
* 您的 ExpressRoute 線路上已設定 Azure 私用對等互連。  

### <a name="sign-in-to-your-azure-account"></a>登入您的 Azure 帳戶
若要開始進行設定，請登入您的 Azure 帳戶。 

以提高的權限開啟 PowerShell 主控台，然後連線至您的帳戶。 此命令會提示您輸入 Azure 帳戶的登入認證。  

```powershell
Connect-AzureRmAccount
```

如果您有多個 Azure 訂用帳戶，請檢查帳戶的訂用帳戶。

```powershell
Get-AzureRmSubscription
```

指定您要使用的訂用帳戶。

```powershell
Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"
```

### <a name="identify-your-expressroute-circuits-for-configuration"></a>識別要設定的 ExpressRoute 線路
您可以在任兩個 ExpressRoute 線路之間啟用 ExpressRoute Global Reach，只要它們位於支援的國家/地區且建立在不同的對等互連位置即可。 如果兩個線路皆為您的訂用帳戶所擁有，則您可以選擇使用任一線路來執行下列各節中的設定。 

如果兩個線路在不同的 Azure 訂用帳戶中，您便需要從一個 Azure 訂用帳戶取得授權。 接著，您需在於另一個 Azure 訂用帳戶中執行設定命令時，傳入該授權金鑰。

## <a name="enable-connectivity-between-your-on-premises-networks"></a>在內部部署網路之間啟用連線

使用下列命令來取得線路 1 和線路 2。 這兩個線路位於相同的訂用帳戶中。

```powershell
$ckt_1 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
$ckt_2 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_2_name" -ResourceGroupName "Your_resource_group"
```

針對線路 1 執行下列命令，並傳入線路 2 的私用對等互連的識別碼。 執行命令時，請注意下列事項：

* 私用對等互連識別碼會類似以下範例： 

  ```
  /subscriptions/{your_subscription_id}/resourceGroups/{your_resource_group}/providers/Microsoft.Network/expressRouteCircuits/{your_circuit_name}/peerings/AzurePrivatePeering
  ```
* *-AddressPrefix* 必須是 /29 IPv4 子網路，例如 "10.0.0.0/29"。 我們會使用此子網路中的 IP 位址，在兩個 ExpressRoute 線路之間建立連線。 您不應該在 Azure 虛擬網路或內部部署網路中，使用此子網路中的位址。

```powershell
Add-AzureRmExpressRouteCircuitConnectionConfig -Name 'Your_connection_name' -ExpressRouteCircuit $ckt_1 -PeerExpressRouteCircuitPeering $ckt_2.Peerings[0].Id -AddressPrefix '__.__.__.__/29'
```

儲存線路 1 上的設定，如下所示：
```powershell
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt_1
```

先前的作業完成時，您應該就可在內部部署網路的兩端，透過兩個 ExpressRoute 線路連線到另一端。

### <a name="expressroute-circuits-in-different-azure-subscriptions"></a>在不同 Azure 訂用帳戶中的 ExpressRoute 線路

如果兩個線路不在相同的 Azure 訂用帳戶中，您便需要授權。 在以下設定中，會在線路 2 訂用帳戶中產生授權，並將授權金鑰傳遞給線路 1。

產生授權金鑰。 
```powershell
$ckt_2 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_2_name" -ResourceGroupName "Your_resource_group"
Add-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $ckt_2 -Name "Name_for_auth_key"
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt_2
```
請記下線路 2 的私用對等互連識別碼，以及授權金鑰。

針對線路 1 執行下列命令。 傳入線路 2 的私用對等互連識別碼及授權金鑰。
```powershell
Add-AzureRmExpressRouteCircuitConnectionConfig -Name 'Your_connection_name' -ExpressRouteCircuit $ckt_1 -PeerExpressRouteCircuitPeering "circuit_2_private_peering_id" -AddressPrefix '__.__.__.__/29' -AuthorizationKey '########-####-####-####-############'
```

在線路 1 上儲存設定。
```powershell
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt_1
```

先前的作業完成時，您應該就可在內部部署網路的兩端，透過兩個 ExpressRoute 線路連線到另一端。

## <a name="get-and-verify-the-configuration"></a>取得及驗證設定

使用下列命令來確認進行設定之線路 (例如上述範例中的線路 1) 上的設定。

```powershell
$ckt1 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
```

如果您直接在 PowerShell 中執行 *$ckt1*，就會在輸出中看到 *CircuitConnectionStatus*。 它會告訴您連線狀態是已建立 (已連線) 還是未建立 (已中斷連線)。 

## <a name="disable-connectivity-between-your-on-premises-networks"></a>在內部部署網路之間停用連線

若要停用連線，請針對進行設定的線路執行命令 (例如上述範例中的線路 1)。

```powershell
$ckt1 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
Remove-AzureRmExpressRouteCircuitConnectionConfig -Name "Your_connection_name" -ExpressRouteCircuit $ckt_1
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt_1
```

您可以執行 Get 作業以驗證狀態。 

在上述作業完成後，您的內部部署網路之間便無法再透過 ExpressRoute 線路進行連線。 


## <a name="next-steps"></a>後續步驟
1. [深入了解 ExpressRoute 觸角擴及全球](expressroute-global-reach.md)
2. [驗證 ExpressRoute 連線](expressroute-troubleshooting-expressroute-overview.md)
3. [將 ExpressRoute 線路連結到 Azure 虛擬網路](expressroute-howto-linkvnet-arm.md)


