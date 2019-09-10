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
ms.openlocfilehash: 45d34297bf37a6e46bc57e95ff49def49051e32e
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/10/2019
ms.locfileid: "67622087"
---
| Resource | 預設/最大限制 |
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

**僅限 100 Gbps ExpressRoute Direct*
