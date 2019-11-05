---
title: 適用于 Azure VPN 閘道連線的自訂流量選取器 |Microsoft Docs
description: 瞭解如何在 VPN 閘道連接上使用自訂流量選取器
services: vpn-gateway
author: radwiv
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 10/17/2019
ms.author: radwiv
ms.openlocfilehash: cf9401b21d4aa736a6edeae5146d1355f2d49d1d
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2019
ms.locfileid: "73512154"
---
# <a name="custom-traffic-selectors-for-vpn-gateway-connections"></a>VPN 閘道連接的自訂流量選取器

您可以在 VPN 閘道連線上設定自訂流量選取器。 設定流量選取器的功能，可讓您縮小要傳送流量的 VPN 通道兩側的位址空間。 當您有較大的 VNet 位址空間，但想要使用其中一個子網進行 IPsec/IKE 的協商時，自訂流量選取器會特別有用。

建立新連線時，可以新增自訂流量選取器，也可以針對現有連線進行更新。 `TrafficSelectorPolicies` 參數是由流量選取器的陣列所組成。 每個流量選取器都會以 CIDR 格式保存本機和遠端位址範圍的集合。

## <a name="add-custom-traffic-selectors"></a>新增自訂流量選取器

下列範例示範如何使用 PowerShell 命令來建立虛擬網路閘道連線的自訂流量選取器。 如需如何建立虛擬網路閘道連線的說明，請參閱[設定站對站](vpn-gateway-create-site-to-site-rm-powershell.md)連線。

1. 在 $trafficselectorpolicy 中設定*trafficselectorpolicies*參數的值。 設定此選項時，請注意*AzTrafficSelectorPolicy*值會採用陣列中的本機和遠端位址範圍。

   ```azurepowershell-interactive
   $trafficSelectorPolicy = New-AzTrafficSelectorPolicy `
   -LocalAddressRanges ("10.10.10.0/24", "20.20.20.0/24") `
   -RemoteAddressRanges ("30.30.30.0/24", "40.40.40.0/24")
   ```
2. 建立新的虛擬網路閘道連線。 調整您需求的值參數。

   ```azurepowershell-interactive
   New-AzVirtualNetworkGatewayConnection `
   -ResourceGroupName $rgname `
   -name $vnetConnectionName `
   -location $location `
   -VirtualNetworkGateway1 $vnetGateway `
   -LocalNetworkGateway2 $localnetGateway `
   -ConnectionType IPsec `
   -RoutingWeight 3 `
   -SharedKey $sharedKey `
   -UsePolicyBasedTrafficSelectors $true `
   -TrafficSelectorPolicies ($trafficSelectorPolicy)
   ```

您也可以使用「設定-Get-azvirtualnetworkgatewayconnection」來更新現有虛擬網路閘道連線的自訂流量選取器。 如需 PowerShell 值，請參閱[虛擬網路閘道聯](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworkgatewayconnection?)機。

## <a name="next-steps"></a>後續步驟

如需有關 VPN 閘道的詳細資訊，請參閱[關於 VPN 閘道](vpn-gateway-about-vpngateways.md)。