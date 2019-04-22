---
title: 如何在 Azure VPN 閘道上設定 OpenVPN：PowerShell| Microsoft Docs
description: 為 Azure VPN 閘道設定 OpenVPN 的步驟
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 01/16/2019
ms.author: cherylmc
ms.openlocfilehash: 8cc2a6d4ad06bf4a55d4ef078970823df1e0d910
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/18/2019
ms.locfileid: "59281959"
---
# <a name="configure-openvpn-for-azure-point-to-site-vpn-gateway-preview"></a>為 Azure 點對站 VPN 閘道設定 OpenVPN (預覽)

這篇文章可協助您設定**OpenVPN® 通訊協定**Azure VPN 閘道上。 本文假設您已具有可用的點對站環境。 如果還沒有，請使用步驟 1 中的指示來建立點對站 VPN。

> [!IMPORTANT]
> 此公開預覽版是在沒有服務等級協定的情況下提供，不得用於生產工作負載。 可能不支援特定功能、可能已經限制功能，或者可能無法在所有 Azure 位置提供使用。 如需詳細資訊，請參閱 [Microsoft Azure 預覽專用的補充使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="register"></a>註冊此功能

按一下這些步驟中的 [TryIt] 即可輕鬆地使用 Azure Cloud Shell 註冊此功能。

>[!NOTE]
>如果您未註冊此功能，將無法使用它。
>

按一下 [TryIt] 可開啟 Azure Cloud Shell，請複製並貼上下列命令：

```azurepowershell-interactive
Register-AzProviderFeature -ProviderNamespace Microsoft.Network -FeatureName AllowVnetGatewayOpenVpnProtocol
```
 
```azurepowershell-interactive
Get-AzProviderFeature -ProviderNamespace Microsoft.Network -FeatureName AllowVnetGatewayOpenVpnProtocol
```

一旦該功能顯示為已註冊，請將訂用帳戶註冊到 Microsoft.Network 命名空間。

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Network
```

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