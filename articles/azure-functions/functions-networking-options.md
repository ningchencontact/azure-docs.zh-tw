---
title: Azure Functions 的網路功能選項
description: 在 Azure Functions 中，您可以使用所有網路功能選項的概觀
services: functions
author: alexkarcher-msft
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 4/11/2019
ms.author: alkarche
ms.openlocfilehash: a4ae2d8bad50a4103da6afaa0bee5cbb75c877aa
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2019
ms.locfileid: "59545497"
---
# <a name="azure-functions-networking-options"></a>Azure 網路功能選項的函式

本文件說明可用的網路功能的套件，在 Azure Functions 裝載選項。 下列網路功能選項的所有提供一些能夠存取資源，而不使用網際網路可路由的位址，或函式應用程式限制網際網路存取。 所有的裝載模型有不同程度的網路隔離，並選擇正確的密碼，將可讓您以符合您的網路隔離需求。

函式應用程式可以裝載數個不同的方式。

* 有一組的多租用戶的基礎結構，具有各種層級 VNET 連線能力和延展選項執行的計劃選項。
    1. 取用方案，其中會以動態方式調整，以回應負載，並提供基本的網路隔離選項。
    1. 進階方案，這也會調整，以動態方式同時提供更完整的網路隔離。
    1. App Service 方案，其中是固定的小數位數，在運作，並提供類似的網路隔離，以 「 進階 」 方案。
* 函式也可以執行在 App Service Environment (ASE) 至您的 VNet 中部署您的函式，並提供完整的網路控制和隔離。

## <a name="networking-feature-matrix"></a>網路功能對照表

|                |[取用方案](functions-scale.md#consumption-plan)|⚠ [進階方案](functions-scale.md##premium-plan-public-preview)|[App Service 方案](functions-scale.md#app-service-plan)|[App Service 環境](../app-service/environment/intro.md)|
|----------------|-----------|----------------|---------|-----------------------|  
|[**輸入的 IP 限制**](#inbound-ip-restrictions)|✅Yes|✅Yes|✅Yes|✅Yes|
|[**VNET 整合**](#vnet-integration)|❌No|❌No|✅Yes|✅Yes|
|[**預覽 VNET 整合 （Express Route 與服務端點）**](#preview-vnet-integration)|❌No|⚠[是]|⚠[是]|✅Yes|
|[**混合式連線**](#hybrid-connections)|❌No|❌No|✅Yes|✅Yes|
|[**私人網站存取**](#private-site-access)|❌No| ❌No|❌No|✅Yes|

⚠ 預覽功能，不適用於生產環境中使用

## <a name="inbound-ip-restrictions"></a>輸入的 IP 限制

IP 限制可讓您針對允許存取應用程式的 IP 位址，定義有先後順序的允許/拒絕清單。 允許清單可以包含 IPv4 和 IPv6 位址。 有一個或多個項目時，清單結尾會有隱含的「拒絕全部」語句。 IP 限制功能適用於裝載選項的所有函式。

> [!NOTE]
> 若要能夠使用 Azure 入口網站的編輯器，在入口網站必須要能夠直接存取您執行的函式應用程式，和您用來存取入口網站的裝置必須有其 IP 允許清單。 在位置的網路限制，您仍然可以存取任何功能**平台功能** 索引標籤。

[在這裡深入了解](https://docs.microsoft.com/azure/app-service/app-service-ip-restrictions)

## <a name="vnet-integration"></a>VNet 整合

VNET 整合可讓您的函式應用程式，來存取 VNET 內的資源。 VNET 整合可在進階方案和 App Service 方案中。 如果您的應用程式在 App Service 環境，它已在 VNet 中，而且不需要使用 VNet 整合功能來觸達相同 VNet 中的資源。

VNet 整合能讓您函式應用程式存取您的虛擬網路中的資源，但不會授與[私人網站存取](#private-site-access)至您的函式應用程式，從虛擬網路。

VNet 整合通常用於啟用從應用程式到 VNet 中執行之資料庫和 Web 服務的存取。 使用 VNet 整合時，您不需要公開 VM 上應用程式的公用端點，但可以改用專用的非網際網路可路由位址。

VNET 整合的正式運作版本會依賴函式應用程式連接至虛擬網路的 VPN 閘道。 在裝載於 app service 方案中的函式中使用。 若要了解如何設定這項功能，請參閱[相同的功能的 App Service 文件](../app-service/web-sites-integrate-with-vnet.md#enabling-vnet-integration)。

### <a name="preview-vnet-integration"></a>預覽 VNET 整合

VNet 整合功能的新版本處於預覽狀態。 它不會相依於點對站 VPN，而且也支援跨 ExpressRoute 或服務端點存取資源。 App Service 方案調整為 PremiumV2 和 「 進階 」 方案中使用這項功能。

新版本的 VNet 整合，目前處於預覽狀態，提供下列優點：

* 不需要任何閘道，即可使用新的 VNet 整合功能
* 除了與 ExpressRoute 連線的 VNet 整合之外，您可以透過 ExpressRoute 連線來存取資源，而不需要進行任何額外設定。
* 您可以取用受保護資源，無法執行函式的服務端點。 若要這樣做，請在用於 VNet 整合的子網路上啟用服務端點。
* 您無法設定觸發程序來使用服務端點保護的資源使用新的 VNet 整合功能。 
* 函式應用程式與 VNet 必須位於相同的區域。
* 新功能需要 Resource Manager VNet 中的未使用子網路。
* 處於預覽時，生產工作負載不會支援 VNet 整合的新版本。
* 新的 VNet 整合尚未提供路由表和全域對等互連。
* 一個地址用於每個潛在的函式應用程式執行個體。 因為指派之後，就無法變更子網路大小，請使用可以輕鬆地支援您的最大的規模大小的子網路。 例如，若要支援可縮放至 80 的執行個體的進階方案，我們建議`/25`提供 126 主機位址的子網路。

若要深入了解使用預覽 VNET 整合，請參閱[函式應用程式與 Azure 虛擬網路整合](functions-create-vnet.md)。

## <a name="hybrid-connections"></a>混合式連線

[混合式連線](../service-bus-relay/relay-hybrid-connections-protocol.md)是可用來存取其他網路中的應用程式資源的 Azure 轉送的功能。 它可讓您從應用程式存取應用程式端點。 它不能存取您的應用程式。 混合式連線可供執行的函式[App Service 方案](functions-scale.md#app-service-plan)並[App Service Environment](../app-service/environment/intro.md)。

使用函式中，每個混合式連線與相互關聯單一 TCP 主機和連接埠組合。 這意謂著「混合式連線」端點可以位於任何作業系統和任何應用程式上，只要您可以存取 TCP 接聽連接埠即可。 混合式連線不會知道 (或不在意) 應用程式通訊協定為何，或您要存取什麼資源。 它只負責提供網路存取。

若要進一步了解，請參閱[混合式連線的 App Service 文件](../app-service/app-service-hybrid-connections.md)，可支援函式與 Web 應用程式。

## <a name="private-site-access"></a>私人網站存取

私人網站存取是指讓您的應用程式只能透過私人網路 (例如從 Azure 虛擬網路中) 存取。 只有使用透過內部負載平衡器 (ILB) 設定的 ASE 才可使用私人網站存取。 如需使用 ILB ASE 的詳細資訊，請參閱[建立和使用 ILB ASE](../app-service/environment/create-ilb-ase.md)。

有許多方法來存取 VNET 資源在其他裝載選項，但 ASE 是允許的函式以在 VNET 上發生的觸發程序的唯一方法。

## <a name="next-steps"></a>後續步驟
若要深入了解網路和函式： 

* [請依照下列快速入門開始使用的 VNET 整合教學課程](./functions-create-vnet.md)
* [讀取網路常見問題集，此函式](./functions-networking-faq.md)
* [在此處深入了解與 App Service / Functions 的 VNET 整合](../app-service/web-sites-integrate-with-vnet.md)
* [深入了解 Azure 中的 VNET](../virtual-network/virtual-networks-overview.md)
* [啟用更多的網路功能和使用 App Service 環境的控制](../app-service/environment/intro.md)
* [連線到個別的內部部署資源，而不需使用混合式連線進行防火牆變更](../app-service/app-service-hybrid-connections.md)
