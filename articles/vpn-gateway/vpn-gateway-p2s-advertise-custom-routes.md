---
title: 公告點對站 VPN 用戶端的自訂路由 |Azure |Microsoft Docs
description: 將自訂路由公告至您的點對站用戶端的步驟
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 09/26/2019
ms.author: cherylmc
ms.openlocfilehash: 38250d1cd9853013ba9721ece0201a8df6dd1b4a
ms.sourcegitcommit: e1b6a40a9c9341b33df384aa607ae359e4ab0f53
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/27/2019
ms.locfileid: "71336298"
---
# <a name="advertise-custom-routes-for-p2s-vpn-clients"></a>公告 P2S VPN 用戶端的自訂路由

您可能想要將自訂路由公告至您所有的點對站 VPN 用戶端。 例如，當您在 VNet 中啟用儲存體端點，並希望遠端使用者能夠透過 VPN 連線來存取這些儲存體帳戶時。 您可以將儲存體端點的 IP 位址公告給所有遠端使用者，讓儲存體帳戶的流量通過 VPN 通道，而不是公用網際網路。

![Azure VPN 閘道多網站連接範例](./media/vpn-gateway-p2s-advertise-custom-routes/custom-routes.png)

## <a name="to-advertise-custom-routes"></a>公告自訂路由

若要通告自訂路由， `Set-AzVirtualNetworkGateway cmdlet`請使用。 下列範例說明如何公告[Contoso 儲存體帳戶資料表](https://contoso.table.core.windows.net)的 IP。

1. Ping *contoso.table.core.windows.net*並記下 IP 位址。 例如:

    ```cmd
    C:\>ping contoso.table.core.windows.net
    Pinging table.by4prdstr05a.store.core.windows.net [13.88.144.250] with 32 bytes of data:
    ```

2. 執行下列 PowerShell 命令：

    ```azurepowershell-interactive
    $gw = Get-AzVirtualNetworkGateway -Name <name of gateway> -ResourceGroupName <name of resource group>
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -CustomRoute 13.88.144.250/32
    ```

3. 若要新增多個自訂路由，請使用逗號和空格來分隔位址。 例如:

    ```azurepowershell-interactive
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -CustomRoute x.x.x.x/xx , y.y.y.y/yy
    ```
## <a name="to-view-custom-routes"></a>若要查看自訂路由

使用下列範例來查看自訂路由：

  ```azurepowershell-interactive
  $gw = Get-AzVirtualNetworkGateway -Name <name of gateway> -ResourceGroupName <name of resource group>
  $gw.CustomRoutes | Format-List
  ```

## <a name="next-steps"></a>後續步驟

如需其他 P2S 路由資訊，請參閱[關於點對站路由](vpn-gateway-about-point-to-site-routing.md)。
