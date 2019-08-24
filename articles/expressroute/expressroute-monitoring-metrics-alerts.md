---
title: 監視、計量和警示 - Azure ExpressRoute | Microsoft Docs
description: 此頁面提供有關 ExpressRoute 監視的資訊
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: 92ec03e20fb6e681a0afd14048449ad004ebca0c
ms.sourcegitcommit: 007ee4ac1c64810632754d9db2277663a138f9c4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/23/2019
ms.locfileid: "69991513"
---
# <a name="expressroute-monitoring-metrics-and-alerts"></a>ExpressRoute 監視、計量和警示

本文將協助您了解使用 Azure 監視器的 ExpressRoute 監視、計量和警示。 Azure 監視器可讓您一次取得所有 Azure 服務上的所有計量、警示和診斷記錄。
 
>[!NOTE]
>不建議使用**傳統計量**。
>

## <a name="circuit-metrics"></a>線路計量

若要瀏覽至 [計量]，請針對您想要監視的線路按一下 [ExpressRoute] 頁面。 在 [監視] 下方，可檢視 [計量]。 從下列計量中選取。 將會套用預設匯總。 (選擇性) 您可以套用 [分割], 這將會顯示具有不同維度的計量。

### <a name="metrics-available"></a>可用的計量: 
* **Availability** 
    * Arp 可用性
      * 可用的維度:
        * 對等 (主要/次要 ExpressRoute 路由器)
        * 對等互連類型 (私用/公用/Microsoft)
    * Bgp 可用性
      * 可用的維度:
        * 對等 (主要/次要 ExpressRoute 路由器)
        * 對等互連類型 (私用/公用/Microsoft)
* **流量**
    * BitsInPerSecond
      * 可用的維度:
        * 對等互連類型 (私用/公用/Microsoft)
    * BitsOutPerSecond
      * 可用的維度:
        * 對等互連類型 (私用/公用/Microsoft)
    * GlobalReachBitsInPerSecond
      * 可用的維度:
        * 對等互連線路 Skey (服務金鑰)
    * GlobalReachBitsOutPerSecond
      * 可用的維度:
        * 對等互連線路 Skey (服務金鑰)

>[!NOTE]
>只有在至少建立了一個全域連線時, 才會顯示使用*GlobalGlobalReachBitsInPerSecond*和*GlobalGlobalReachBitsOutPerSecond* 。
>

## <a name="bits-in-and-out---metrics-across-all-peerings"></a>位 In 和 Out-所有對等互連之間的計量

您可以跨給定 ExpressRoute 線路上的所有對等互連來查看計量。

![線路計量](./media/expressroute-monitoring-metrics-alerts/ermetricspeering.jpg)

## <a name="bits-in-and-out---metrics-per-peering"></a>位 In 和 Out-每個對等互連的計量

您可以檢視私用、公用及 Microsoft 對等互連的計量 (以位元/秒為單位)。

![每個對等互連的計量](./media/expressroute-monitoring-metrics-alerts/erpeeringmetrics.jpg) 

## <a name="bgp-availability---split-by-peer"></a>BGP 可用性-依對等分割  

您可以在對等互連和對等 (主要和次要 ExpressRoute 路由器) 上, 查看 BGP 的即時可用性。 此儀表板會顯示私人對等互連的主要 BGP 會話, 以及針對私人對等互連關閉的第二個 BGP 會話。 

![每個對等的 BGP 可用性](./media/expressroute-monitoring-metrics-alerts/erBgpAvailabilityMetrics.jpg) 

## <a name="arp-availability---split-by-peering"></a>ARP 可用性-依對等互連分割  

您可以在對等互連和對等 (主要和次要 ExpressRoute 路由器) 上查看[ARP](https://docs.microsoft.com/azure/expressroute/expressroute-troubleshooting-arp-resource-manager)的即時可用性。 此儀表板會顯示在這兩個對等互連之間的私用對等互連 ARP 會話, 但會在對等互連的 Microsoft 對等互連完成 在兩個對等之間使用預設匯總 (平均)。  

![每個對等的 ARP 可用性](./media/expressroute-monitoring-metrics-alerts/erArpAvailabilityMetrics.jpg) 

## <a name="expressroute-gateway-connections-in-bitsseconds"></a>ExpressRoute 閘道連線 (以位元/秒為單位)

![閘道連線](./media/expressroute-monitoring-metrics-alerts/erconnections.jpg ) 

## <a name="alerts-for-expressroute-gateway-connections"></a>ExpressRoute 閘道連線的警示

1. 若要設定警示，請瀏覽至 [Azure 監視器]，然後按一下 [警示]。

   ![警示](./media/expressroute-monitoring-metrics-alerts/eralertshowto.jpg)

2. 按一下 [+ 選取目標]，然後選取 ExpressRoute 閘道連線資源。

   ![target]( ./media/expressroute-monitoring-metrics-alerts/alerthowto2.jpg)
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
