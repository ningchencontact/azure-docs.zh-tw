---
title: 包含檔案
description: 包含檔案
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: include
ms.date: 05/28/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 9f7f9b1504533e0ec503e97c086c380da71671a0
ms.sourcegitcommit: 8c49df11910a8ed8259f377217a9ffcd892ae0ae
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/29/2019
ms.locfileid: "66299715"
---
| Resource | 預設值/最大限制 |
| --- | --- |
| 每個訂用帳戶的 ExpressRoute 電路 |10 |
| 每個訂用帳戶，使用 Azure Resource Manager 區域的 ExpressRoute 線路 |10 |
| 公告至 Azure 私人對等互連具有 ExpressRoute Standard 的最大路由數目 |4,000 |
| 向 Azure 私人對等互連具有 ExpressRoute Premium 附加元件公告的最大路由數目 |10,000 |
| 從 Azure 私人對等互連的 ExpressRoute 連線的 VNet 位址空間的最大路由數目公告 |200 |
| 公告至 Microsoft 對等互連具有 ExpressRoute Standard 的最大路由數目 |200 |
| 向 Microsoft 對等互連具有 ExpressRoute Premium 附加元件公告的最大路由數目 |200 |
| ExpressRoute 線路連結至相同的虛擬網路位於相同的對等互連位置的最大數目 |4 |
| ExpressRoute 線路連結至相同虛擬網路中不同對等互連位置的最大數目 |4 |
| 每個 ExpressRoute 電路允許的虛擬網路連結數目 |請參閱[的每個 ExpressRoute 電路的虛擬網路數目](#vnetpercircuit)資料表。  |

#### <a name="vnetpercircuit"></a> 每個 ExpressRoute 電路的虛擬網路數目
| **電路大小** | **標準的虛擬網路連結數目** | **具有 Premium 附加元件的虛擬網路連結數目** |
| --- | --- | --- |
| 50 Mbps |10 |20 |
| 100 Mbps |10 |25 |
| 200 Mbps |10 |25 |
| 500 Mbps |10 |40 |
| 1 Gbps |10 |50 |
| 2 Gbps |10 |60 |
| 5 Gbps |10 |75 |
| 10 Gbps |10 |100 |
| 40 Gbps* |10 |100 |
| 100 Gbps* |10 |100 |

* 只有 ExpressRoute Direct
