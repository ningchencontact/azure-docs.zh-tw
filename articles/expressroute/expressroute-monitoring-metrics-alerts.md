---
title: 監視、計量和警示 - Azure ExpressRoute | Microsoft Docs
description: 此頁面提供有關 ExpressRoute 監視的資訊
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: 115b1185575232800e060f67317e4973eea9ea53
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/08/2018
ms.locfileid: "53091759"
---
# <a name="expressroute-monitoring-metrics-and-alerts"></a>ExpressRoute 監視、計量和警示

本文將協助您了解使用 Azure 監視器的 ExpressRoute 監視、計量和警示。 Azure 監視器可讓您一次取得所有 Azure 服務上的所有計量、警示和診斷記錄。
 
>[!NOTE]
>不建議使用**傳統計量**。
>

## <a name="circuit-metrics"></a>線路計量

若要瀏覽至 [計量]，請針對您想要監視的線路按一下 [ExpressRoute] 頁面。 在 [監視] 下方，可檢視 [計量]。 選取 BitsInPerSecond 或 BitsOutPerSecond 以及彙總。 您可以選擇性地套用分割，以依據對等互連類型顯示計量。

![線路計量](./media/expressroute-monitoring-metrics-alerts/ermetricspeering.jpg)

## <a name="metrics-per-peering"></a>每個對等互連的計量

您可以檢視私用、公用及 Microsoft 對等互連的計量 (以位元/秒為單位)。

![每個對等互連的計量](./media/expressroute-monitoring-metrics-alerts/erpeeringmetrics.jpg) 

## <a name="expressroute-gateway-connections-in-bitsseconds"></a>ExpressRoute 閘道連線 (以位元/秒為單位)

![閘道連線](./media/expressroute-monitoring-metrics-alerts/erconnections.jpg ) 

## <a name="alerts-for-expressroute-gateway-connections"></a>ExpressRoute 閘道連線的警示

1. 若要設定警示，請瀏覽至 [Azure 監視器]，然後按一下 [警示]。

  ![alerts](./media/expressroute-monitoring-metrics-alerts/eralertshowto.jpg)

2. 按一下 [+ 選取目標]，然後選取 ExpressRoute 閘道連線資源。

  ![目標]( ./media/expressroute-monitoring-metrics-alerts/alerthowto2.jpg)
3. 定義警示詳細資料。

  ![動作群組](./media/expressroute-monitoring-metrics-alerts/alerthowto3.jpg)

4. 定義和新增動作群組。

  ![新增動作群組](./media/expressroute-monitoring-metrics-alerts/actiongroup.png)

## <a name="alerts-based-on-each-peering"></a>以每個對等互連為基礎的警示

 ![什麼](./media/expressroute-monitoring-metrics-alerts/basedpeering.jpg)

## <a name="configure-alerts-for-activity-logs-on-circuits"></a>在線路上設定活動記錄的警示

在 [警示準則] 中，您可以選取 [活動記錄] 作為訊號類型，並選取訊號。

  ![另一個](./media/expressroute-monitoring-metrics-alerts/alertshowto6activitylog.jpg)
  
## <a name="next-steps"></a>後續步驟

設定 ExpressRoute 連線。
  
  * [建立及修改電路](expressroute-howto-circuit-arm.md)
  * [建立和修改對等互連組態](expressroute-howto-routing-arm.md)
  * [將 VNet 連結到 ExpressRoute 線路](expressroute-howto-linkvnet-arm.md)
