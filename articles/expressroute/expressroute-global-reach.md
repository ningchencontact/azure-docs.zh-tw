---
title: 使用 Global Reach 將內部部署網路連線至 Microsoft Cloud - Azure ExpressRoute | Microsoft Docs
description: 此文章說明 ExpressRoute Global Reach。
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 11/14/2018
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: 8ee57bf101b432049d895b65a1dc7641653c7f21
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/08/2018
ms.locfileid: "53105442"
---
# <a name="expressroute-global-reach-preview"></a>ExpressRoute Global Reach (預覽)
ExpressRoute 是私人且有彈性的方式，可讓您將內部部署網路連線到 Microsoft Cloud。 您可以從私人資料中心或公司網路存取許多 Microsoft 雲端服務，例如 Azure、Office 365 和 Dynamics 365。 例如，您在舊金山分公司的 ExpressRoute 線路可能在美國矽谷，而另一家倫敦分公司的 ExpressRoute 線路則位於同一座城市。 這兩家分公司都具備可連至美國西部和英國南部之 Azure 資源的高速連線能力。 不過，這些分公司彼此無法直接交換資料。 換句話說，10.0.1.0/24 可以將資料傳送到 10.0.3.0/24 和 10.0.4.0/24，但不能傳送到 10.0.2.0/24。

![不含][1]

透過 **ExpressRoute Global Reach**，您可以將 ExpressRoute 線路連結在一起，以在內部部署網路之間產生私人網路。 在上述範例中，透過新增 ExpressRoute Global Reach，舊金山分公司 (10.0.1.0/24) 就能透過現有的 ExpressRoute 線路及透過 Microsoft 的全球網路，直接與倫敦分公司 (10.0.2.0/24) 交換資料。 

![含有][2]

## <a name="use-case"></a>使用案例
ExpressRoute Global Reach 是設計來補充您服務提供者的 WAN 實作，以及連線您位於世界各地的分公司。 例如，如果您服務提供者的營運範圍主要是在美國境內，並且已連結您在美國的所有分公司，但該服務提供者並未在日本和香港特別行政區中營運，透過 ExpressRoute Global Reach，您就能與當地服務提供者合作，而 Microsoft 會使用 ExpressRoute 和全球網路，將您位於該處的分公司連線到美國的分公司。

![使用案例][3]

## <a name="availability"></a>可用性 
下列位置目前支援 ExpressRoute Global Reach。

* 澳大利亞
* 法國
* 香港
* 愛爾蘭
* 日本
* 荷蘭
* 英國
* 美國

您必須在上述國家或地區的 [ExpressRoute 對等互連位置](expressroute-locations.md)上建立 ExpressRoute 線路。 若要在[不同的地緣政治區域](expressroute-locations.md)之間啟用 ExpressRoute 觸角擴及全球，您的線路必須是進階 SKU。

## <a name="next-steps"></a>後續步驟
1. [深入了解 ExpressRoute 觸角擴及全球](expressroute-faqs.md)
2. [如何啟用 ExpressRoute Global Reach](expressroute-howto-set-global-reach.md)
3. [將 ExpressRoute 線路連結到 Azure 虛擬網路](expressroute-howto-linkvnet-arm.md)


<!--Image References-->
[1]: ./media/expressroute-global-reach/1.png "不含觸角擴及全球的圖表"
[2]: ./media/expressroute-global-reach/2.png "包含觸角擴及全球的圖表"
[3]: ./media/expressroute-global-reach/3.png "Global Reach 的使用案例"
