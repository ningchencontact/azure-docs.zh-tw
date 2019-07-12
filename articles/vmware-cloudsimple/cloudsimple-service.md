---
title: VMware CloudSimple 服務的解決方案
description: 描述 CloudSimple 服務和概念的概觀。
author: sharaths-cs
ms.author: b-shsury
ms.date: 04/24/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 0bebc68129ee2ff79241bcefec1ce0c3ca0b472d
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/05/2019
ms.locfileid: "67595321"
---
# <a name="cloudsimple-service-overview"></a>CloudSimple 服務概觀

CloudSimple 服務時，您可以使用 Azure CloudSimple VMware 方案。 建立服務之後，您可以佈建節點，保留節點，並建立私人雲端。 您可以建立 CloudSimple 服務 CloudSimple 服務可使用的每個 Azure 區域中。 服務定義 CloudSimple Azure VMware 解決方案的 edge 網路。 邊緣網路支援，包括 VPN、 Azure ExpressRoute 和私人雲端的網際網路連線的服務。

## <a name="gateway-subnet"></a>閘道器子網路

閘道子網路需要每個 CloudSimple 服務，而且是專用的建立所在的區域。 閘道子網路可用於當您建立邊緣網路，而且需要/28 的 CIDR 區塊。 閘道子網路位址空間必須是唯一的。 它不得與任何 CloudSimple 環境與通訊的網路重疊。 CloudSimple 與通訊的網路會包含在內部部署網路與 Azure 虛擬網路。 在建立之後，就無法刪除閘道子網路。 刪除服務時，會移除閘道子網路。

## <a name="next-steps"></a>後續步驟

* 了解如何[在 Azure 上建立 CloudSimple 服務](quickstart-create-cloudsimple-service.md)。
