---
title: Azure Functions 網路功能選項
description: Azure Functions 中提供的所有網路功能選項的總覽。
author: alexkarcher-msft
ms.topic: conceptual
ms.date: 4/11/2019
ms.author: alkarche
ms.openlocfilehash: a3df48115dde27478446614c0446d64709adbc6f
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/20/2019
ms.locfileid: "74226807"
---
# <a name="azure-functions-networking-options"></a>Azure Functions 網路功能選項

本文說明可跨 Azure Functions 的裝載選項使用的網路功能。 下列所有網路功能選項都可讓您在不使用網際網路可路由位址的情況下存取資源，或限制對函數應用程式的網際網路存取。

裝載模型可提供不同層級的網路隔離。 選擇正確的帳戶可協助您符合網路隔離需求。

您可以透過幾種方式來裝載函數應用程式：

* 有一組在多租使用者基礎結構上執行的方案選項，具有各種層級的虛擬網路連線能力和調整選項：
    * 取用[方案](functions-scale.md#consumption-plan)，它會動態調整以回應負載，並提供最小的網路隔離選項。
    * 高階[方案](functions-scale.md#premium-plan)也會以動態方式進行調整，同時提供更全面的網路隔離。
    * Azure [App Service 方案](functions-scale.md#app-service-plan)，會以固定的規模運作，並提供類似 Premium 方案的網路隔離。
* 您可以在[App Service 環境](../app-service/environment/intro.md)中執行函數。 這個方法會將您的函式部署到您的虛擬網路，並提供完整的網路控制和隔離功能。

## <a name="matrix-of-networking-features"></a>網路功能的矩陣

|                |[耗用量方案](functions-scale.md#consumption-plan)|[Premium 方案](functions-scale.md#premium-plan)|[App Service 計劃](functions-scale.md#app-service-plan)|[App Service 環境](../app-service/environment/intro.md)|
|----------------|-----------|----------------|---------|-----------------------|  
|[私人網站存取 & 的輸入 IP 限制](#inbound-ip-restrictions)|✅是|✅是|✅是|✅是|
|[虛擬網路整合](#virtual-network-integration)|❌否|✅是（地區）|✅是（區域和閘道）|✅是|
|[虛擬網路觸發程式（非 HTTP）](#virtual-network-triggers-non-http)|❌否| ❌否|✅是|✅是|
|[混合式連線](#hybrid-connections)|❌否|✅是|✅是|✅是|
|[輸出 IP 限制](#outbound-ip-restrictions)|❌否| ❌否|❌否|✅是|

## <a name="inbound-ip-restrictions"></a>輸入 IP 限制

您可以使用 IP 限制，定義已允許或拒絕存取您應用程式的優先順序排序的 IP 位址清單。 清單可以包含 IPv4 和 IPv6 位址。 當有一或多個專案時，清單結尾會有隱含的「全部拒絕」。 IP 限制適用所有函數裝載選項。

> [!NOTE]
> 有了網路限制之後，您就可以只從虛擬網路內使用入口網站編輯器，或是當您在 [安全收件者] 清單上放置用來存取 Azure 入口網站之電腦的 IP 位址時。 不過，您仍然可以從任何電腦存取 [**平臺功能**] 索引標籤上的任何功能。

若要深入瞭解，請參閱[Azure App Service 靜態存取限制](../app-service/app-service-ip-restrictions.md)。

## <a name="private-site-access"></a>私人網站存取

私用網站存取是指讓您的應用程式只能從私人網路（例如 Azure 虛擬網路）存取。

* 設定服務端點時，可在[Premium](./functions-premium-plan.md)、[耗用量](functions-scale.md#consumption-plan)和[App Service](functions-scale.md#app-service-plan)方案中取得私用網站存取。
    * 服務端點可以在 [**平臺功能**] > [**網路** > 設定**存取限制**] > [**新增規則**] 底下，以每個應用程式為基礎。 現在可以選取 [虛擬網路] 做為 [規則類型]。
    * 如需詳細資訊，請參閱[虛擬網路服務端點](../virtual-network/virtual-network-service-endpoints-overview.md)。
    * 請記住，使用服務端點時，即使已設定虛擬網路整合，您的函式仍會擁有網際網路的完整輸出存取權。
* 您也可以在使用內部負載平衡器（ILB）設定的 App Service 環境中使用私人網站存取。 如需詳細資訊，請參閱[建立和使用內部負載平衡器與 App Service 環境](../app-service/environment/create-ilb-ase.md)。

## <a name="virtual-network-integration"></a>虛擬網路整合

虛擬網路整合可讓您的函數應用程式存取虛擬網路內的資源。 這項功能同時適用于 Premium 方案和 App Service 方案。 如果您的應用程式是在 App Service 環境中，它已經在虛擬網路中，而且不需要虛擬網路整合就能連線到相同虛擬網路中的資源。

您可以使用虛擬網路整合，以啟用從應用程式存取在虛擬網路中執行的資料庫和 web 服務。 透過虛擬網路整合，您不需要公開 VM 上應用程式的公用端點。 您可以改用私用的非網際網路可路由位址。

虛擬網路整合有兩種形式：

+ **區域虛擬網路整合（預覽）** ：啟用與相同區域中的虛擬網路整合。 這種類型的整合需要在相同區域中的虛擬網路中有子網。 這項功能仍處於預覽狀態，但可支援在 Windows 上執行的函式應用程式，並包含下列問題/解決方案資料表之後所述的注意事項。
+ **閘道所需的虛擬網路整合**：啟用與遠端區域中的虛擬網路，或與傳統虛擬網路的整合。 這種類型的整合需要將虛擬網路閘道部署到您的 VNet。 這是點對站 VPN 功能，僅支援在 Windows 上執行的函式應用程式。

應用程式一次只能使用一種類型的虛擬網路整合功能。 雖然這兩種方法在許多情況下都很有用，但下表指出每個應該使用的位置：

| 問題  | 解決方案 |
|----------|----------|
| 想要在相同的區域中達到 RFC 1918 位址（10.0.0.0/8、172.16.0.0/12、192.168.0.0/16） | 區域虛擬網路整合 |
| 想要連線至傳統虛擬網路或另一個區域中的虛擬網路中的資源 | 閘道所需的虛擬網路整合 |
| 想要跨 Azure ExpressRoute 觸及 RFC 1918 端點 | 區域虛擬網路整合 |
| 想要跨服務端點存取資源 | 區域虛擬網路整合 |

這兩項功能都不能讓您跨 ExpressRoute 達到非 RFC 1918 位址。 若要這麼做，您目前必須使用 App Service 環境。

使用區域虛擬網路整合並不會將您的虛擬網路連線到內部部署端點或設定服務端點。 這是個別的網路設定。 區域虛擬網路整合可讓您的應用程式在這些連線類型之間進行呼叫。

不論使用的版本為何，虛擬網路整合可讓您的函數應用程式存取虛擬網路中的資源，但不會授與從虛擬網路存取您函式應用程式的私用網站。 私用網站存取是指讓您的應用程式只能從私人網路（例如 Azure 虛擬網路）存取。 虛擬網路整合只是用來從您的應用程式對虛擬網路進行輸出呼叫。

虛擬網路整合功能：

* 需要 Standard、Premium 或 PremiumV2 App Service 方案
* 支援 TCP 和 UDP
* 適用于 App Service apps 和函數應用程式

虛擬網路整合不支援一些事項，包括：

* 掛接磁碟機
* Active Directory 整合
* NetBIOS

Azure Functions 中的虛擬網路整合使用共用的基礎結構搭配 App Service web 應用程式。 若要深入瞭解這兩種類型的虛擬網路整合，請參閱：

* [區域虛擬網路整合](../app-service/web-sites-integrate-with-vnet.md#regional-vnet-integration)
* [閘道所需的虛擬網路整合](../app-service/web-sites-integrate-with-vnet.md#gateway-required-vnet-integration)

若要深入瞭解如何使用虛擬網路整合，請參閱[整合函數應用程式與 Azure 虛擬網路](functions-create-vnet.md)。

## <a name="connecting-to-service-endpoint-secured-resources"></a>連接至服務端點保護的資源

> [!NOTE]
> 目前，在您設定下游資源的存取限制之後，最多可能需要12小時的時間，新的服務端點才會變成可供函數應用程式使用。 在這段期間，您的應用程式將完全無法使用資源。

若要提供較高層級的安全性，您可以使用服務端點，將一些 Azure 服務限制在虛擬網路上。 接著，您必須將函數應用程式與該虛擬網路整合，才能存取資源。 支援虛擬網路整合的所有方案都支援此設定。

[深入瞭解虛擬網路服務端點。](../virtual-network/virtual-network-service-endpoints-overview.md)

### <a name="restricting-your-storage-account-to-a-virtual-network"></a>將您的儲存體帳戶限制為虛擬網路

當您建立函數應用程式時，您必須建立或連結至支援 Blob、佇列和資料表儲存體的一般用途 Azure 儲存體帳戶。 您目前無法在此帳戶上使用任何虛擬網路限制。 如果您在用來處理函式應用程式的儲存體帳戶上設定虛擬網路服務端點，將會中斷您的應用程式。

[深入瞭解儲存體帳戶需求。](./functions-create-function-app-portal.md#storage-account-requirements)

### <a name="using-key-vault-references"></a>使用 Key Vault 參考 

Key Vault 參考可讓您在 Azure Functions 應用程式中使用來自 Azure Key Vault 的秘密，而不需要變更任何程式碼。 Azure Key Vault 是提供集中式秘密管理的服務，可完整控制存取原則和審核歷程記錄。

如果您的 Key Vault 受到服務端點的保護，目前[Key Vault 的參考](../app-service/app-service-key-vault-references.md)將無法使用。 若要使用虛擬網路整合來連線到 Key Vault，您必須在應用程式代碼中呼叫 Key Vault。

## <a name="virtual-network-triggers-non-http"></a>虛擬網路觸發程式（非 HTTP）

目前，若要從虛擬網路內使用 HTTP 以外的函式觸發程式，您必須在 App Service 計畫或 App Service 環境中執行函數應用程式。

例如，假設您想要將 Azure Cosmos DB 設定為只接受來自虛擬網路的流量。 您必須在 app service 方案中部署函式應用程式，以提供與該虛擬網路的虛擬網路整合，以便從該資源設定 Azure Cosmos DB 的觸發程式。 在預覽期間，設定虛擬網路整合並不允許 Premium 方案觸發該 Azure Cosmos DB 資源。

請參閱[這份清單，以取得所有非 HTTP 觸發](./functions-triggers-bindings.md#supported-bindings)程式，以再次檢查支援的內容。

## <a name="hybrid-connections"></a>混合式連線

「[混合](../service-bus-relay/relay-hybrid-connections-protocol.md)式連線」是 Azure 轉送的一項功能，可讓您用來存取其他網路中的應用程式資源。 它可讓您從應用程式存取應用程式端點。 您無法使用它來存取您的應用程式。 混合式連接可供在所有取用方案中執行的函式使用。

在 Azure Functions 中使用時，每個混合式連線都會與單一 TCP 主機和埠組合相互關聯。 這表示，只要您要存取 TCP 接聽埠，混合式連線的端點就可以在任何作業系統和任何應用程式上。 「混合式連線」功能不知道或在意應用程式通訊協定或您要存取的內容。 它只會提供網路存取。

若要深入瞭解，請參閱[混合式連接的 App Service 檔](../app-service/app-service-hybrid-connections.md)。 這些相同的設定步驟支援 Azure Functions。

## <a name="outbound-ip-restrictions"></a>輸出 IP 限制

輸出 IP 限制僅適用于部署到 App Service 環境的函式。 您可以為部署 App Service 環境的虛擬網路設定輸出限制。

當您將高階計畫中的函式應用程式或使用虛擬網路的 App Service 計畫整合時，應用程式仍可對網際網路進行輸出呼叫。

## <a name="next-steps"></a>後續步驟

若要深入瞭解網路和 Azure Functions：

* [遵循有關開始使用虛擬網路整合的教學課程](./functions-create-vnet.md)
* [閱讀函式網路功能常見問題](./functions-networking-faq.md)
* [深入瞭解虛擬網路與 App Service/功能的整合](../app-service/web-sites-integrate-with-vnet.md)
* [深入瞭解 Azure 中的虛擬網路](../virtual-network/virtual-networks-overview.md)
* [利用 App Service 環境啟用更多網路功能和控制](../app-service/environment/intro.md)
* [使用混合式連線連線到個別的內部部署資源，而不需要變更防火牆](../app-service/app-service-hybrid-connections.md)
