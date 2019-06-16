---
title: 包含檔案
description: 包含檔案
services: expressroute
author: jaredr80
ms.service: expressroute
ms.topic: include
ms.date: 02/25/2019
ms.author: jaredro
ms.custom: include file
ms.openlocfilehash: ab74c331bdc8b72612aa848688e1de080314337a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67132997"
---
### <a name="what-is-expressroute-direct"></a>什麼是 ExpressRoute Direct？

ExpressRoute Direct 為客戶提供能夠將策略性分散於世界各地的對等互連位置，都直接連線至 Microsoft 全球網路的能力。 ExpressRoute Direct 提供雙向 100 Gbps 連線，且支援大規模主動/主動連線。 

### <a name="how-do-customers-connect-to-expressroute-direct"></a>客戶如何連線至 ExpressRoute Direct？ 

客戶需要與當地的電訊廠商和共置提供者合作，才能連線到 ExpressRoute 路由器以充分利用 ExpressRoute Direct。

### <a name="what-locations-currently-support-expressroute-direct"></a>哪些位置目前支援 ExpressRoute Direct？ 

可用的連接埠將會是動態的，且將可透過 PowerShell 來檢視容量。 位置包括如下，且根據可用性可能會有所變更  ：

* 阿姆斯特丹
* 芝加哥
* 華盛頓
* 達拉斯 
* 香港特別行政區
* 倫敦
* 洛杉磯
* New York City
* 巴黎
* 伯斯
* 多倫多
* San Antonio
* Seattle
* 首爾
* 矽谷
* 新加坡 
* 雪梨

### <a name="what-is-the-sla-for-expressroute-direct"></a>ExpressRoute Direct 的 SLA 為何？

ExpressRoute Direct 會利用相同的[企業級 ExpressRoute](https://azure.microsoft.com/support/legal/sla/expressroute/v1_3/)。 

### <a name="what-scenarios-should-customers-consider-with-expressroute-direct"></a>客戶應針對哪些案例考慮使用 ExpressRoute Direct？  

ExpressRoute Direct 提供客戶直接與 Microsoft 全球骨幹配對的 100 Gbps 連接埠。 能為客戶提供絕佳好處的案例包括：大規模資料擷取、針對受嚴格規範市場的物理隔離和高載案例 (例如轉譯) 的專用容量。 

### <a name="what-is-the-billing-model-for-expressroute-direct"></a>ExpressRoute Direct 的計費模型為何？ 

ExpressRoute Direct 會針對連接埠配對收取固定的金額。 標準線路將包含在內，不會計算額外時數，而進階線路將會有些許附加元件費用。 輸出將會根據對等位置的區域，按每個線路計費。

### <a name="when-does-billing-start-for-the-expressroute-direct-port-pairs"></a>何時會計費開始的 ExpressRoute 直接連接埠配對？

ExpressRoute Direct 的連接埠配對在建立 ExpressRoute Direct 資源 45 天後或在啟用其中一個或同時兩個連結 (視何者先) 時收費。 在 45 天寬限期內會允許客戶與共置提供者完成跨連線程序。
