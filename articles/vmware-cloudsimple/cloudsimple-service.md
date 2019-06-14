---
title: VMware CloudSimple-服務方案
description: 描述 CloudSimple 服務和概念的概觀。
author: sharaths-cs
ms.author: b-shsury
ms.date: 4/24/19
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: f7b4be0ff3997e27dd5b5321dd44b5006ae52102
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66358104"
---
# <a name="cloudsimple-service-overview"></a>CloudSimple 服務概觀

CloudSimple 服務可讓您取用 Azure CloudSimple VMware 方案。  建立此服務可讓您購買節點保留節點，以及建立私人雲端。  您可以建立 CloudSimple 服務 CloudSimple 服務可使用的每個 Azure 區域中。  服務定義 CloudSimple Azure VMware 解決方案的 edge 網路。  邊緣網路支援，包括 VPN、 ExpressRoute，以及您的私用雲端的網際網路連線的服務。

## <a name="gateway-subnet"></a>閘道器子網路

閘道子網路需要每個 CloudSimple 服務，而且是專用的建立所在的區域。 閘道子網路可用於建立緣網路時，而且需要/28 的 CIDR 區塊。  閘道子網路位址空間必須是唯一的。 它不得與任何 CloudSimple 環境與通訊的網路重疊。 CloudSimple 與通訊的網路會包含在內部部署網路與 Azure 虛擬網路。  建立後即無法刪除閘道子網路。  刪除服務時，會移除閘道子網路。

## <a name="next-steps"></a>後續步驟

* 了解如何[在 Azure 上建立 CloudSimple 服務](quickstart-create-cloudsimple-service.md)