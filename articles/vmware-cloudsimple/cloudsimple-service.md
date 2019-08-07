---
title: VMware Solution by CloudSimple 服務
description: 說明 CloudSimple 服務和概念的總覽。
author: sharaths-cs
ms.author: b-shsury
ms.date: 04/24/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: a43fbebb21be82fd751778d6fec95e0ee9c346ad
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/06/2019
ms.locfileid: "68812502"
---
# <a name="cloudsimple-service-overview"></a>CloudSimple 服務總覽

使用 CloudSimple 服務時, 您可以使用 CloudSimple 的 Azure VMware 解決方案。 建立服務之後, 您可以布建節點、保留節點, 以及建立私人雲端。 您會在可使用 CloudSimple 服務的每個 Azure 區域中建立 CloudSimple 服務。 服務會依 CloudSimple 定義 Azure VMware 解決方案的邊緣網路。 Edge 網路支援包含 VPN、Azure ExpressRoute 和網際網路連線的服務, 可連接到您的私人雲端。

## <a name="gateway-subnet"></a>閘道子網路

每個 CloudSimple 服務都需要閘道子網, 而且對其建立所在的區域而言是唯一的。 當您建立 edge 網路時, 會使用閘道子網, 而且需要/28 CIDR 區塊。 閘道子網位址空間必須是唯一的。 它不得與任何與 CloudSimple 環境通訊的網路重迭。 與 CloudSimple 通訊的網路包括內部部署網路和 Azure 虛擬網路。 建立閘道子網之後, 就無法刪除它。 刪除服務時, 會移除閘道子網。

## <a name="next-steps"></a>後續步驟

* 瞭解如何[在 Azure 上建立 CloudSimple 服務](quickstart-create-cloudsimple-service.md)。
