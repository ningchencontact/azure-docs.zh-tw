---
title: Azure 網路功能選項的函式
description: 在 Azure Functions 中，您可以使用所有網路功能選項的概觀
services: functions
author: alexkarcher-msft
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 4/11/2019
ms.author: alkarche
ms.openlocfilehash: f13e498859986d5ee697cbd67907fd344147ed0c
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/04/2019
ms.locfileid: "66492844"
---
# <a name="azure-functions-networking-options"></a>Azure 網路功能選項的函式

本文會說明可用的網路功能，在 Azure Functions 的裝載選項。 下列網路功能選項的所有提供一些能夠存取資源，而不使用網際網路可路由的位址，或函式應用程式限制網際網路存取。 

主控模型各有不同的可用的網路隔離層級。 選擇正確的密碼，可協助您符合您的網路隔離需求。

您可以裝載在數種方式中的函式應用程式：

* 還有一組的多租用戶的基礎結構，以各種層級的虛擬網路連線和延展選項執行的計劃選項：
    * [耗用量計劃](functions-scale.md#consumption-plan)，這會動態調整，以回應負載，並提供基本的網路隔離選項。
    * [進階方案](functions-scale.md#premium-plan-public-preview)，這也會調整，以動態方式同時提供更完整的網路隔離。
    * Azure [App Service 方案](functions-scale.md#app-service-plan)，這是固定的小數位數在運作，並提供類似的網路隔離，以 「 進階 」 方案。
* 您可以在執行函式[App Service Environment](../app-service/environment/intro.md)。 這個方法會將您的函式部署到您的虛擬網路，並提供完整的網路控制和隔離。

## <a name="matrix-of-networking-features"></a>網路功能矩陣

|                |[取用方案](functions-scale.md#consumption-plan)|[進階方案 （預覽）](functions-scale.md#premium-plan-public-preview)|[App Service 計劃](functions-scale.md#app-service-plan)|[App Service 環境](../app-service/environment/intro.md)|
|----------------|-----------|----------------|---------|-----------------------|  
|[輸入的 IP 限制](#inbound-ip-restrictions)|✅Yes|✅Yes|✅Yes|✅Yes|
|[輸出 IP 限制](#private-site-access)|❌No| ❌No|❌No|✅Yes|
|[虛擬網路整合](#virtual-network-integration)|❌No|❌No|✅Yes|✅Yes|
|[預覽 （Azure ExpressRoute 和輸出的服務端點） 的虛擬網路整合](#preview-version-of-virtual-network-integration)|❌No|✅Yes|✅Yes|✅Yes|
|[混合式連線](#hybrid-connections)|❌No|❌No|✅Yes|✅Yes|
|[私人網站存取](#private-site-access)|❌No| ✅Yes|✅Yes|✅Yes|

## <a name="inbound-ip-restrictions"></a>輸入的 IP 限制

您可以使用 IP 限制，定義被允許/拒絕存取您的應用程式的 IP 位址的優先順序排序清單。 此清單可以包含 IPv4 和 IPv6 位址。 一或多個項目時，隱含 「 全部拒絕 」 清單結尾處存在。 IP 限制使用所有的函式裝載選項。

> [!NOTE]
> 若要使用 Azure 入口網站的編輯器，在入口網站必須能夠直接存取您執行的函式應用程式。 此外，您用來存取入口網站的裝置必須具有其 IP 允許清單。 在位置的網路限制，您仍然可以存取任何功能上**平台功能** 索引標籤。

若要進一步了解，請參閱[Azure App Service 靜態存取限制](../app-service/app-service-ip-restrictions.md)。

## <a name="outbound-ip-restrictions"></a>輸出 IP 限制

輸出 IP 限制僅可供部署至 App Service Environment 的函式。 您可以設定 App Service Environment 部署所在的虛擬網路的輸出限制。

## <a name="virtual-network-integration"></a>虛擬網路整合

虛擬網路整合可讓您的函式應用程式存取虛擬網路內的資源。 這項功能可在 「 進階 」 方案和 App Service 方案中。 如果您的應用程式在 App Service 環境，它已在虛擬網路，並不需要使用虛擬網路整合來觸達相同的虛擬網路中的資源。

虛擬網路整合可讓您函式應用程式存取您的虛擬網路中的資源，但不會授與[私人網站存取](#private-site-access)至您的函式應用程式，從虛擬網路。

您可以使用虛擬網路整合，以從應用程式存取資料庫和虛擬網路中執行的 web 服務。 與虛擬網路整合，您不需要公開 VM 上的應用程式的公用端點。 您可以改為使用私用、 非網際網路可路由傳送位址。

虛擬網路整合的正式運作版本會依賴函式應用程式連接至虛擬網路的 VPN 閘道。 在裝載於 App Service 方案中的函式中使用。 若要了解如何設定這項功能，請參閱[整合您的應用程式與 Azure 虛擬網路](../app-service/web-sites-integrate-with-vnet.md)。

### <a name="preview-version-of-virtual-network-integration"></a>預覽版本的虛擬網路整合

新版本的虛擬網路整合功能處於預覽狀態。 它不會相依於 點對站 VPN。 它透過 ExpressRoute 支援存取資源或服務端點。 App Service 方案調整為 PremiumV2 和 「 進階 」 方案中有提供。

以下是此版本的某些特性：

* 您不需要閘道來使用它。
* 除了與連線到 ExpressRoute 的虛擬網路整合之外，您還可以透過 ExpressRoute 連線來存取資源，而不需要進行任何額外設定。
* 您可以使用服務端點保護的資源，無法執行函式。 若要這樣做，請啟用用於虛擬網路整合的子網路上的服務端點。
* 您無法設定觸發程序來使用服務端點保護的資源。 
* 函式應用程式與虛擬網路必須位於相同的區域。
* 新的功能都需要您透過 「 Azure Resource Manager 部署的虛擬網路中未使用的子網路。
* 雖然此功能處於預覽狀態，不支援生產工作負載。
* 路由表與全域對等互連尚無法使用此功能。
* 一個地址用於每個函式應用程式的潛在執行個體。 因為您無法變更子網路大小指派之後，請使用可以輕鬆地支援您的最大的規模大小的子網路。 例如，若要支援可縮放至 80 的執行個體的進階方案，我們建議`/25`提供 126 主機位址的子網路。

若要深入了解使用虛擬網路整合的預覽版本，請參閱[函式應用程式與 Azure 虛擬網路整合](functions-create-vnet.md)。

## <a name="hybrid-connections"></a>混合式連線

[混合式連線](../service-bus-relay/relay-hybrid-connections-protocol.md)是可用來存取其他網路中的應用程式資源的 Azure 轉送的功能。 它可讓您從應用程式存取應用程式端點。 您無法使用它來存取您的應用程式。 混合式連線可供執行的函式[App Service 方案](functions-scale.md#app-service-plan)並[App Service Environment](../app-service/environment/intro.md)。

使用 Azure Functions 中，每個混合式連線與相互關聯單一 TCP 主機和連接埠組合。 這表示混合式連線端點可以位於任何作業系統和任何應用程式，只要您正在存取 TCP 接聽連接埠。 「 混合式連線 」 功能不知道或在意的應用程式通訊協定為何，或您正在存取。 它只會提供網路存取。

若要進一步了解，請參閱[混合式連線的 App Service 文件](../app-service/app-service-hybrid-connections.md)，可支援在 App Service 方案中的函式。

## <a name="private-site-access"></a>私人網站存取

私人網站存取是指讓您的應用程式只能從私人網路這類來自 Azure 的虛擬網路內存取。 
* 私人網站存取是適用於 Premium 和 App Service 方案**服務端點**設定。如需詳細資訊，請參閱[虛擬網路服務端點](../virtual-network/virtual-network-service-endpoints-overview.md)
    * 請記住，透過服務端點，您的函式仍有完整的對外存取網際網路，即使使用 VNET 整合設定。
* 私人網站存取可僅與使用內部負載平衡器 (ILB) 設定 App Service Environment。 如需詳細資訊，請參閱 <<c0> [ 建立及使用內部負載平衡與 App Service Environment](../app-service/environment/create-ilb-ase.md)。

有許多方式來存取其他裝載選項中的虛擬網路資源。 但是，App Service Environment 是允許透過虛擬網路會發生函式的觸發程序的唯一方法。

## <a name="next-steps"></a>後續步驟
若要深入了解網路和 Azure 函式： 

* [在本教學課程，解如何開始使用虛擬網路整合](./functions-create-vnet.md)
* [讀取網路常見問題集函式](./functions-networking-faq.md)
* [深入了解虛擬網路整合與應用程式服務/函式](../app-service/web-sites-integrate-with-vnet.md)
* [深入了解在 Azure 中的虛擬網路](../virtual-network/virtual-networks-overview.md)
* [啟用更多的網路功能和使用 App Service 環境的控制](../app-service/environment/intro.md)
* [使用混合式連線連線到內部個別資源，而不需要防火牆變更](../app-service/app-service-hybrid-connections.md)
