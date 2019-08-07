---
title: Azure Functions 網路功能選項
description: Azure Functions 提供的所有網路功能選項總覽
services: functions
author: alexkarcher-msft
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 4/11/2019
ms.author: alkarche
ms.openlocfilehash: f4f081001f2573bccc58205ccc7955739b7f5c4c
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/05/2019
ms.locfileid: "68779282"
---
# <a name="azure-functions-networking-options"></a>Azure Functions 網路功能選項

本文說明可跨 Azure Functions 的裝載選項使用的網路功能。 下列所有網路功能選項都可讓您在不使用網際網路可路由位址的情況下存取資源, 或限制對函式應用程式的網際網路存取。 

裝載模型可提供不同層級的網路隔離。 選擇正確的帳戶可協助您符合網路隔離需求。

您可以透過幾種方式來裝載函數應用程式:

* 有一組在多租使用者基礎結構上執行的方案選項, 具有各種層級的虛擬網路連線能力和調整選項:
    * 取用[方案](functions-scale.md#consumption-plan), 它會動態調整以回應負載, 並提供最小的網路隔離選項。
    * 高階[方案](functions-scale.md#premium-plan)也會以動態方式進行調整, 同時提供更全面的網路隔離。
    * Azure [App Service 方案](functions-scale.md#app-service-plan)是以固定的規模運作, 並提供類似于 Premium 方案的網路隔離。
* 您可以在[App Service 環境](../app-service/environment/intro.md)中執行函數。 這個方法會將您的函式部署到您的虛擬網路, 並提供完整的網路控制和隔離功能。

## <a name="matrix-of-networking-features"></a>網路功能的矩陣

|                |[耗用量方案](functions-scale.md#consumption-plan)|[Premium 方案 (預覽)](functions-scale.md#premium-plan)|[App Service 計劃](functions-scale.md#app-service-plan)|[App Service 環境](../app-service/environment/intro.md)|
|----------------|-----------|----------------|---------|-----------------------|  
|[私人網站存取 & 的輸入 IP 限制](#inbound-ip-restrictions)|✅是|✅是|✅是|✅是|
|[虛擬網路整合](#virtual-network-integration)|❌No|✅是 (地區)|✅是 (區域和閘道)|✅是|
|[虛擬網路觸發程式 (非 HTTP)](#virtual-network-triggers-non-http)|❌No| ❌No|✅是|✅是|
|[混合式連線](#hybrid-connections)|❌No|❌No|✅是|✅是|
|[輸出 IP 限制](#outbound-ip-restrictions)|❌No| ❌No|❌No|✅是|


## <a name="inbound-ip-restrictions"></a>輸入 IP 限制

您可以使用 IP 限制, 定義允許/拒絕存取您的應用程式之以優先順序排序的 IP 位址清單。 清單可以包含 IPv4 和 IPv6 位址。 當有一或多個專案時, 清單結尾會有隱含的「全部拒絕」。 IP 限制適用所有函數裝載選項。

> [!NOTE]
> 當有網路限制時, 您只能從虛擬網路內使用入口網站編輯器, 或在您將用來存取 Azure 入口網站之電腦的 IP 列入允許清單。 不過, 您仍然可以從任何電腦存取 [**平臺功能**] 索引標籤上的任何功能。

若要深入瞭解, 請參閱[Azure App Service 靜態存取限制](../app-service/app-service-ip-restrictions.md)。

## <a name="private-site-access"></a>私人網站存取

私用網站存取是指讓您的應用程式只能從私人網路 (例如從 Azure 虛擬網路內) 存取。 
* 設定**服務端點**時, 可在[Premium](./functions-premium-plan.md)和[App Service 方案](functions-scale.md#app-service-plan)中取得私用網站存取。 如需詳細資訊, 請參閱[虛擬網路服務端點](../virtual-network/virtual-network-service-endpoints-overview.md)
    * 請記住, 使用服務端點時, 即使已設定虛擬網路整合, 您的函式仍會擁有網際網路的完整輸出存取權。
* 使用內部負載平衡器 (ILB) 設定的 App Service 環境也可以使用私人網站存取。 如需詳細資訊, 請參閱[建立和使用內部負載平衡器與 App Service 環境](../app-service/environment/create-ilb-ase.md)。

## <a name="virtual-network-integration"></a>虛擬網路整合

虛擬網路整合可讓您的函數應用程式存取虛擬網路內的資源。 這項功能同時適用于 Premium 方案和 App Service 方案。 如果您的應用程式是在 App Service 環境中, 它已經在虛擬網路中, 而且不需要使用虛擬網路整合來連線到相同虛擬網路中的資源。

您可以使用虛擬網路整合, 以啟用從應用程式存取在虛擬網路中執行的資料庫和 web 服務。 透過虛擬網路整合, 您不需要公開 VM 上應用程式的公用端點。 您可以改用私用的非網際網路可路由位址。

虛擬網路整合功能有兩種形式

1. 區域虛擬網路整合可讓您與相同區域中的虛擬網路整合。 這種形式的功能需要相同區域中的虛擬網路中的子網。 這項功能仍處於預覽狀態, 但對於 Windows 應用程式生產工作負載而言, 其支援如下所述的一些注意事項。
2. 閘道所需的虛擬網路整合可讓您與遠端區域中的虛擬網路或傳統虛擬網路整合。 此版本的功能需要將虛擬網路閘道部署至您的 VNet。 這是點對站 VPN 功能, 只支援 Windows 應用程式。

應用程式一次只能使用一種形式的 VNet 整合功能。 問題是, 您應該使用哪一項功能。 您可以使用其中一種來進行許多事項。 但明顯的差異如下:

| 問題  | 方案 | 
|----------|----------|
| 想要在相同的區域中達到 RFC 1918 位址 (10.0.0.0/8、172.16.0.0/12、192.168.0.0/16) | 區域 VNet 整合 |
| 想要觸及傳統 VNet 或另一個區域中的 VNet 中的資源 | 閘道所需的 VNet 整合 |
| 想要透過 ExpressRoute 觸及 RFC 1918 端點 | 區域 VNet 整合 |
| 想要跨服務端點存取資源 | 區域 VNet 整合 |

這兩項功能都不能讓您跨 ExpressRoute 達到非 RFC 1918 位址。 若要這麼做, 您現在必須使用 ASE。

使用區域 VNet 整合並不會將您的 VNet 連線到內部部署或設定服務端點。 這是不同的網路設定。 區域 VNet 整合可讓您的應用程式在這些連線類型之間進行呼叫。

不論使用的版本為何, VNet 整合都會讓您的函數應用程式存取虛擬網路中的資源, 但不會授與從虛擬網路存取您函式應用程式的私用網站。 私人網站存取是指讓您的應用程式只能透過私人網路 (例如從 Azure 虛擬網路中) 存取。 VNet 整合僅適用于從您的應用程式對 VNet 進行輸出呼叫。 

VNet 整合功能：

* 需要 Standard、Premium 或 PremiumV2 App Service 方案
* 支援 TCP 和 UDP
* 適用于 App Service 應用程式和函數應用程式

VNet 整合不支援的事項包括：

* 掛接磁碟機
* AD 整合 
* NetBios

函式中的虛擬網路整合會將共用基礎結構與 App Service web 應用程式搭配使用。 若要深入瞭解這兩種類型的虛擬網路整合, 請參閱:
* [地區 VNET 整合](../app-service/web-sites-integrate-with-vnet.md#regional-vnet-integration)
* [閘道所需的 VNet 整合](../app-service/web-sites-integrate-with-vnet.md#gateway-required-vnet-integration)

若要深入瞭解如何使用虛擬網路整合, 請參閱[整合函數應用程式與 Azure 虛擬網路](functions-create-vnet.md)。

## <a name="virtual-network-triggers-non-http"></a>虛擬網路觸發程式 (非 HTTP)

目前, 若要能夠從虛擬網路內使用 HTTP 以外的函式觸發程式, 您必須在 App Service 計畫或 App Service 環境中執行函數應用程式。

若要提供範例, 如果您要設定 Azure Cosmos DB 只接受來自虛擬網路的流量, 您必須在 app service 方案中部署函數應用程式, 並將虛擬網路與該虛擬網路整合, 以設定 Azure Cosmos DB 觸發程式從該資源。 在預覽期間, 設定 VNET 整合不會讓 Premium 方案觸發該 Azure Cosmos DB 資源。

請檢查[這份清單中的所有非 HTTP 觸發程式](./functions-triggers-bindings.md#supported-bindings), 以再次檢查支援的內容。

## <a name="hybrid-connections"></a>混合式連接

「[混合](../service-bus-relay/relay-hybrid-connections-protocol.md)式連線」是 Azure 轉送的一項功能, 可讓您用來存取其他網路中的應用程式資源。 它可讓您從應用程式存取應用程式端點。 您無法使用它來存取您的應用程式。 混合式連接可供在[App Service 計畫](functions-scale.md#app-service-plan)和[App Service 環境](../app-service/environment/intro.md)中執行的函式使用。

在 Azure Functions 中使用時, 每個混合式連線都會與單一 TCP 主機和埠組合相互關聯。 這表示, 只要您要存取 TCP 接聽埠, 混合式連線的端點就可以在任何作業系統和任何應用程式上。 「混合式連線」功能不知道或在意應用程式通訊協定為何, 或您要存取的內容。 它只是提供網路存取。

若要深入瞭解, 請參閱混合式連線的[App Service 檔](../app-service/app-service-hybrid-connections.md), 其支援 App Service 計畫中的功能。

## <a name="outbound-ip-restrictions"></a>輸出 IP 限制

輸出 IP 限制僅適用于部署到 App Service 環境的函式。 您可以為部署 App Service 環境的虛擬網路設定輸出限制。

將高階方案中的函式應用程式或 App Service 計畫整合到虛擬網路時, 應用程式仍可對網際網路進行輸出呼叫。

## <a name="next-steps"></a>後續步驟
若要深入瞭解網路和 Azure Functions: 

* [遵循有關開始使用虛擬網路整合的教學課程](./functions-create-vnet.md)
* [閱讀函式網路功能常見問題](./functions-networking-faq.md)
* [深入瞭解虛擬網路與 App Service/功能的整合](../app-service/web-sites-integrate-with-vnet.md)
* [深入瞭解 Azure 中的虛擬網路](../virtual-network/virtual-networks-overview.md)
* [利用 App Service 環境啟用更多網路功能和控制](../app-service/environment/intro.md)
* [使用混合式連線連線到個別的內部部署資源, 而不需要變更防火牆](../app-service/app-service-hybrid-connections.md)
