---
title: 舊版 Azure 虛擬網路 VPN 閘道 SKU | Microsoft Docs
description: 如何使用舊的虛擬網路閘道 SKU；基本、標準和高效能。
services: vpn-gateway
documentationcenter: na
author: WenJason
manager: digimobile
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
origin.date: 03/20/2018
ms.date: 03/04/2019
ms.author: v-jay
ms.openlocfilehash: 5a9e3f63a484069bf8cd39f8a545d7c37f05c63c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "66159238"
---
# <a name="working-with-virtual-network-gateway-skus-legacy-skus"></a>使用虛擬網路閘道 SKU (舊版 SKU)

此文章包含舊版虛擬網路閘道 SKU 的相關資訊。 舊版 SKU 仍然可以在針對 VPN 閘道建立的兩個部署模型中運作。 傳統 VPN 閘道仍繼續針對現有閘道以及新閘道使用舊版 SKU。 在建立新的資源管理員 VPN 閘道時，請使用新的閘道 SKU。 如需最新 SKU 的詳細資訊，請參閱[關於 VPN 閘道](vpn-gateway-about-vpngateways.md)。

## <a name="gwsku"></a>閘道 SKU

[!INCLUDE [Legacy gateway SKUs](../../includes/vpn-gateway-gwsku-legacy-include.md)]

## <a name="agg"></a>依 SKU 列出的估計彙總輸送量

[!INCLUDE [Aggregated throughput by legacy SKU](../../includes/vpn-gateway-table-gwtype-legacy-aggtput-include.md)]

## <a name="config"></a>依 SKU 和 VPN 類型列出的支援組態

[!INCLUDE [Table requirements for old SKUs](../../includes/vpn-gateway-table-requirements-legacy-sku-include.md)]

## <a name="resize"></a>調整閘道大小

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

您可以將相同 SKU 系列內的閘道大小調整為閘道 SKU。 例如，如果您有標準 SKU，則可以調整為高效能 SKU。 但是，您無法調整舊 SKU 與新 SKU 系列之間的 VPN 閘道大小。 例如，您不能從標準 SKU 變成 VpnGw2 SKU；也不能從標準 SKU 變成 VpnGw1。

若要調整傳統部署模型的閘道大小，請使用下列命令：

```powershell
Resize-AzureVirtualNetworkGateway -GatewayId <Gateway ID> -GatewaySKU HighPerformance
```

若要使用 PowerShell 調整資源管理員部署模型的閘道大小，請使用下列命令：

```powershell
$gw = Get-AzVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
Resize-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku HighPerformance
```
您也可以在 Azure 入口網站中調整閘道。

## <a name="change"></a>變更為新的閘道 SKU

[!INCLUDE [Change to the new SKUs](../../includes/vpn-gateway-gwsku-change-legacy-sku-include.md)]

## <a name="next-steps"></a>後續步驟

如需新式閘道 SKU 的相關資訊，請參閱[閘道 SKU](vpn-gateway-about-vpngateways.md#gwsku)。

如需組態設定的詳細資訊，請參閱[關於 VPN 閘道組態設定](vpn-gateway-about-vpn-gateway-settings.md)。

<!--Update_Description: wording update-->