---
title: 關於 ExpressRoute 虛擬網路閘道 - Azure | Microsoft Docs
description: 了解 ExpressRoute 的虛擬網路閘道。 本文包含閘道 SKU 和類型的相關資訊。
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: mialdrid
ms.custom: seodec18
ms.openlocfilehash: 18615cf737eedcd188fd59d2aa98482210b9333a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "65991595"
---
# <a name="expressroute-virtual-network-gateway-and-fastpath"></a>ExpressRoute 虛擬網路閘道和 FastPath
若要連接您的 Azure 虛擬網路和內部部署網路透過 ExpressRoute，您必須先建立虛擬網路閘道。 虛擬網路閘道有兩種用途： exchange 之間的網路和路由傳送網路流量的 IP 路由。 這篇文章說明閘道類型、 閘道 Sku 和依 sku 列出的估計的效能。 這篇文章也會說明 ExpressRoute[快速](#fastpath)，此功能可讓網路流量從您的內部部署網路，以略過虛擬網路閘道，以改善效能。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="gateway-types"></a>閘道類型

當您建立虛擬網路閘道時，您必須指定數項設定。 其中一個必要設定 '-GatewayType' 會指定是否要對 ExpressRoute 或 VPN 閘道流量使用閘道。 兩種閘道類型如下：

* **Vpn** - 若要透過公用網際網路傳送已加密的流量，請使用 'Vpn' 閘道類型。 也稱之為 VPN 閘道。 站對站、點對站和 VNet 對 VNet 連線都使用 VPN 閘道。

* **ExpressRoute** - 若要在私人連線上傳送網路流量，請使用 'ExpressRoute' 閘道類型。 這也稱為 ExpressRoute 閘道，而且是您設定 ExpressRoute 時所用的閘道類型。

對於每種閘道類型，每個虛擬網路只能有一個虛擬網路閘道。 例如，您可以有一個使用 -GatewayType Vpn 的虛擬網路閘道，以及一個使用 -GatewayType ExpressRoute 的虛擬網路閘道。

## <a name="gwsku"></a>閘道 SKU
[!INCLUDE [expressroute-gwsku-include](../../includes/expressroute-gwsku-include.md)]

如果您想要升級您的閘道更強大的閘道 SKU，在大部分情況下您可以使用 ' 調整 AzVirtualNetworkGateway' PowerShell cmdlet。 這適用於升級至 Standard 和 HighPerformance SKU。 不過，若要升級至 UltraPerformance SKU，您必須重新建立閘道器。 重新建立閘道時會導致停機。

### <a name="aggthroughput"></a>閘道 SKU 預估的效能
下表顯示閘道類型和預估的效能。 此資料表適用於資源管理員與傳統部署模型。

[!INCLUDE [expressroute-table-aggthroughput](../../includes/expressroute-table-aggtput-include.md)]

> [!IMPORTANT]
> 應用程式效能取決於多項因素，例如端對端延遲以及應用程式開啟之流量的數目。 表格中的數字代表應用程式在理想的環境中，理論上可以達成的最高上限。
>
>

### <a name="zrgw"></a>區域備援閘道 SKU

您也可以在 Azure 可用性區域中部署 ExpressRoute 閘道。 這能夠在實際上和邏輯上將閘道分隔為不同的可用性區域，同時還能在發生區域層級的失敗時，保護內部部署項目與 Azure 的網路連線。

![區域備援 ExpressRoute 閘道](./media/expressroute-about-virtual-network-gateways/zone-redundant.png)

區域備援閘道會使用適用於 ExpressRoute 閘道的特定新式閘道 SKU。

* ErGw1AZ
* ErGw2AZ
* ErGw3AZ

新的閘道 SKU 也支援其他部署選項，以充分符合您的需求。 使用新的閘道 SKU 建立虛擬網路閘道時，您也可選擇在特定區域中部署閘道。 稱之為區域閘道。 當您部署分區閘道時，閘道的所有執行個體都會部署在相同的可用性區域中。

## <a name="fastpath"></a>FastPath
ExpressRoute 虛擬網路閘道被設計來交換網路路由，路由傳送網路流量。 快速的目的是要改善您的內部部署網路與虛擬網路之間的資料路徑效能。 啟用時，快速的網路流量將直接傳送至虛擬機器在虛擬網路中，略過的閘道。 

快速位於[ExpressRoute 直接](expressroute-erdirect-about.md)只。 換句話說，您可以啟用此功能只有當您[將虛擬網路連線](expressroute-howto-linkvnet-arm.md)到 ExpressRoute 線路的 ExpressRoute 直接連接埠上建立。 快速仍然需要以交換路由虛擬網路與內部部署網路之間建立虛擬網路閘道。 超級效能或 ErGw3AZ，必須是虛擬網路閘道。

快速並不支援下列功能：
* 閘道子網路上的 UDR： 如果您套用至閘道子網路，您將會繼續從您的內部部署網路的網路流量傳送到虛擬網路閘道的虛擬網路的 UDR。
* VNet 對等互連： 如果您有其他虛擬網路對等互連以連線至其他虛擬網路 (也就是所謂 「 輪輻 」 Vnet) 之網路流量從您的內部部署網路將會繼續傳送至虛擬網路的 ExpressRoute閘道。 因應措施是直接連接到 ExpressRoute 線路的所有虛擬網路。

## <a name="resources"></a>REST API 和 PowerShell Cmdlet
如需將 REST API 和 PowerShell Cmdlet 使用於虛擬網路閘道組態時的其他技術資源和特定語法需求，請參閱下列頁面︰

| **傳統** | **Resource Manager** |
| --- | --- |
| [PowerShell](https://docs.microsoft.com/powershell/module/servicemanagement/azure/?view=azuresmps-4.0.0#azure) |[PowerShell](https://docs.microsoft.com/powershell/module/az.network#networking) |
| [REST API](https://msdn.microsoft.com/library/jj154113.aspx) |[REST API](https://msdn.microsoft.com/library/mt163859.aspx) |

## <a name="next-steps"></a>後續步驟
如需有關可用連線組態的詳細資訊，請參閱 [ExpressRoute 概觀](expressroute-introduction.md) 。

如需建立 ExpressRoute 閘道的詳細資訊，請參閱[為 ExpressRoute 建立虛擬網路閘道](expressroute-howto-add-gateway-resource-manager.md)。

如需設定區域備援閘道的詳細資訊，請參閱[建立區域備援虛擬網路閘道](../../articles/vpn-gateway/create-zone-redundant-vnet-gateway.md)。

請參閱[連結至 ExpressRoute 的虛擬網路](expressroute-howto-linkvnet-arm.md)如需有關如何啟用快速。 
