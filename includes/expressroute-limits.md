---
title: 包含檔案
description: 包含檔案
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: include
ms.date: 07/25/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 1fc63dc53d61a8b2e26f97cc09a359b3f2c7665c
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2020
ms.locfileid: "75901668"
---
| 資源 | 預設/最大限制 |
| --- | --- |
| 每個訂用帳戶的 ExpressRoute 電路 |10 |
| 每個訂用帳戶每個區域的 ExpressRoute 線路，Azure Resource Manager |10 |
| 使用 ExpressRoute Standard 向 Azure 私用對等互連通告的最大路由數目 |4,000 |
| 使用 ExpressRoute Premium 附加元件通告至 Azure 私用對等互連的最大路由數目 |10,000 |
| 從 ExpressRoute 連線的 VNet 位址空間針對 Azure 私用對等互連通告的路由數目上限 |200 |
| 使用 ExpressRoute Standard 公告至 Microsoft 對等互連的最大路由數目 |200 |
| 使用 ExpressRoute Premium 附加元件公告至 Microsoft 對等互連的最大路由數目 |200 |
| 在相同的對等互連位置中連結至相同虛擬網路的 ExpressRoute 線路數目上限 |4 |
| ExpressRoute 線路連結至相同虛擬網路中不同對等互連位置的最大數目 |4 |
| 每個 ExpressRoute 電路允許的虛擬網路連結數目 |請參閱[每個 ExpressRoute 線路的虛擬網路數目](#vnetpercircuit)。  |

#### <a name="vnetpercircuit"></a>每個 ExpressRoute 線路的虛擬網路數目
| **線路大小** | **標準的虛擬網路連結數目** | **具有 Premium 附加元件的虛擬網路連結數目** |
| --- | --- | --- |
| 50 Mbps |10 |20 |
| 100 Mbps |10 |25 |
| 200 Mbps |10 |25 |
| 500 Mbps |10 |40 |
| 1 Gbps |10 |50 |
| 2 Gbps |10 |60 |
| 5 Gbps |10 |75 |
| 10 Gbps |10 |100 |
| 40 Gbps * |10 |100 |
| 100 Gbps * |10 |100 |

*僅 *100 Gbps ExpressRoute Direct*

> [!NOTE]
> 全球觸達連線計數會與每個 ExpressRoute 線路的虛擬網路連線限制有關。 例如，10 Gbps 高階線路可允許5個全球連線和95連線至 ExpressRoute 閘道或 95 Global 連線，以及連線至 ExpressRoute 閘道或任何其他組合的5個連線，最多可達100個連線的限制用於電路。
