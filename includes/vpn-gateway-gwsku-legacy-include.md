---
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 11/09/2018
ms.author: cherylmc
ms.openlocfilehash: f2ee442d0d6fecf34449ad28f058615a1274bbea
ms.sourcegitcommit: fa758779501c8a11d98f8cacb15a3cc76e9d38ae
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/20/2018
ms.locfileid: "52269325"
---
舊版 (舊式) VPN 閘道 SKU 如下：

* 基本
* 標準
* 高效能

VPN 閘道不會使用 UltraPerformance 閘道 SKU。 如需 UltraPerformance SKU 的相關資訊，請參閱 [ExpressRoute](../articles/expressroute/expressroute-about-virtual-network-gateways.md) 文件。

使用舊版 SKU 時，請考慮下列各項：

* 如果您想要使用原則式 VPN 類型，您必須使用基本 SKU。 其他所有 SKU 均不支援原則式 VPN (先前稱為靜態路由)。
* 基本 SKU 不支援 BGP。
* 基本 SKU 不支援 ExpressRoute-VPN 閘道共存組態。
* 僅可以在高效能 SKU 上設定主動-主動 S2S VPN 閘道連線。