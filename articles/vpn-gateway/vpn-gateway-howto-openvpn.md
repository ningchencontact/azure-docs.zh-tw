---
title: 如何在 Azure VPN 閘道上設定 OpenVPN：PowerShell| Microsoft Docs
description: 為 Azure VPN 閘道設定 OpenVPN 的步驟
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: cherylmc
ms.openlocfilehash: 609c2ef91fafe0ae955252a594292d861e772f87
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/22/2019
ms.locfileid: "66002951"
---
# <a name="configure-openvpn-for-azure-point-to-site-vpn-gateway"></a>設定 OpenVPN 適用於 Azure 點對站 VPN 閘道

這篇文章可協助您設定**OpenVPN® 通訊協定**Azure VPN 閘道上。 本文假設您已具有可用的點對站環境。 如果還沒有，請使用步驟 1 中的指示來建立點對站 VPN。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="vnet"></a>1.建立點對站 VPN

如果您還沒有正常運作的點對站台環境，請依照指示來建立一個。 請參閱[建立點對站 VPN](vpn-gateway-howto-point-to-site-resource-manager-portal.md)，來建立及設及使用原生 Azure 憑證驗證的點對站 VPN 閘道。 

> [!IMPORTANT]
> OpenVPN 不支援基本 SKU。

## <a name="enable"></a>2.在閘道上啟用 OpenVPN

在閘道上啟用 OpenVPN。 執行下列命令之前，務必確認閘道已針對點對站 (IKEv2 或 SSTP) 進行設定：

```azurepowershell-interactive
$gw = Get-AzVirtualNetworkGateway -ResourceGroupName $rgname -name $name
Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -VpnClientProtocol OpenVPN
```

## <a name="next-steps"></a>後續步驟

若要設定 OpenVPN 的用戶端，請參閱[設定 OpenVPN 用戶端](vpn-gateway-howto-openvpn-clients.md)。

**「 OpenVPN"是 OpenVPN Inc.的商標。**
