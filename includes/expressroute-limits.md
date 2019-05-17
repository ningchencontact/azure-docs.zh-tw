---
title: 包含檔案
description: 包含檔案
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: include
ms.date: 06/12/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: c025c431d826d3a2951a9eb5c09308695e172887
ms.sourcegitcommit: f013c433b18de2788bf09b98926c7136b15d36f1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/13/2019
ms.locfileid: "65551214"
---
| 資源 | 預設值/最大限制 |
| --- | --- |
| 每個訂用帳戶的 ExpressRoute 電路 |10 |
| 每個訂用帳戶，使用 Azure Resource Manager 區域的 ExpressRoute 線路 |10 |
| Azure 私人對等互連具有 ExpressRoute Standard 的路由數目上限 |4,000 |
| Azure 私人對等互連具有 ExpressRoute Premium 附加元件的路由數目上限 |10,000 |
| Azure 私人對等互連的 ExpressRoute 連線的 VNet 位址空間路由的最大數目 |200 | 
| 具有 ExpressRoute Standard 的 Microsoft Azure 對等互連的路由數目上限 |200 |
| 具有 ExpressRoute Premium 附加元件的 Microsoft Azure 對等互連的路由數目上限 |200 |
| ExpressRoute 線路連結至相同的虛擬網路位於相同的對等互連位置的最大數目 |4 |
| ExpressRoute 線路連結至相同虛擬網路中不同對等互連位置的最大數目 |4 |
| 每個 ExpressRoute 電路允許的虛擬網路連結數目 |請參閱下表。 |

#### <a name="number-of-virtual-networks-per-expressroute-circuit"></a>每個 ExpressRoute 電路的虛擬網路數目
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
