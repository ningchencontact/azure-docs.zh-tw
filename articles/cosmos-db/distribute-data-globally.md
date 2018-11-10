---
title: 使用 Azure Cosmos DB 全域散發資料 | Microsoft Docs
description: 了解如何從 Azure Cosmos DB (全域散發的多模型資料庫服務)，使用全域資料庫進行全球規模的異地複寫、多重主機、容錯移轉及資料復原。
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 10/26/2018
ms.author: mjbrown
ms.openlocfilehash: 68ac603964593892dfcbc3488b4e6f2c91a0eb92
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2018
ms.locfileid: "50238269"
---
# <a name="global-data-distribution-with-azure-cosmos-db"></a>透過 Azure Cosmos DB 進行全域資料散發

現在許多應用程式會以全球性的規模執行，或是希望能達到這樣的規模。 這些應用程式永遠處於啟用狀態，並讓世界各地的使用者可以存取。 提供高效能和高可用性的同時，還要管理這些應用程式使用的全域散發資料，並不是簡單的事。 Cosmos DB 是一項從零開始架構的全域分散式資料庫服務，目的就是要克服這些挑戰。

Cosmos DB 是基本的 Azure 服務，預設為可在所有 [Azure 區域](https://azure.microsoft.com/global-infrastructure/regions/)上使用。 Microsoft 會操控世界上超過 50 個區域中的 Azure 資料中心，並繼續進行擴充，以滿足不斷成長的客戶需求。 Microsoft 會全天候操控 Cosmos DB 服務，讓您可以專注在您的應用程式上。 建立 Cosmos DB 帳戶時，您會決定要部署此帳戶的區域。

Cosmos DB 客戶可以將他們的資料庫設定為全域散發，並在 1 到 50 多個 Azure 區域中使用。 為達到較低的延遲，您應該將資料放在靠近您使用者的位置，因此，區域數量和區域位置取決於您應用程式的全球覆蓋率和使用者位置。 Cosmos DB 會以透明方式將您 Cosmos 帳戶內的所有資料複寫到所有已設定的區域。 Cosmos DB 會提供 Cosmos 資料庫和容器的單一系統映像，使您的應用程式可以在本機讀取和寫入。 透過 Cosmos DB，您可以隨時新增或移除任何與您帳戶相關聯的區域。 您的應用程式不需要暫停或重新部署，而且，由於服務會提供多路連接功能，應用程式仍可保持隨時處理資料的高可用性。

## <a name="key-benefits-of-global-distribution"></a>全域散發的主要優點

**輕鬆建置全域性的主動-主動應用程式**：透過多主機功能，每個區域都處於可寫入狀態 (亦可讀取)，並具有無限制的彈性寫入延展性、達 99.999% 的讀取和寫入能力 (從世界各地)，以及提供保證低於 10 毫秒的快速讀取和寫入 (位於第 99 個百分位數)。  

若使用 Azure Cosmos DB 的多路連接 API，您的應用程式一律會知道最靠近的區域，並將要求傳送至該區域。 識別最靠近的區域不會變更任何組態。 當您在 Cosmos DB 帳戶中新增及移除區域時，應用程式不需要重新部署，就可繼續保有高可用性。

**建置回應迅速的應用程式**：您可以輕鬆地設計應用程式，讓其在您選擇的所有資料庫區域中，執行近乎即時的讀取和寫入作業，而且只有個位數的毫秒延遲。  Azure Cosmos DB 會在內部處理區域之間的資料複寫，並保證達到您為 Cosmos 帳戶選擇的一致性層級。

許多應用程式都受益於多重區域 (本機) 寫入功能帶來的效能提升。 但某些應用程式 (例如需要強式一致性的應用程式) 會想要將所有寫入作業傳送到單一區域。 Cosmos DB 支援兩種組態：單一區域和多重區域。

**建置高可用性應用程式**：在多個區域中執行資料庫可提升資料庫的可用性。 如果一個區域無法使用，其他區域就會自動處理應用程式的要求。 Azure Cosmos DB 可為多重區域資料庫提供達 99.999% 的讀取和寫入可用性。

**區域性中斷期間的業務持續性**：發生區域性中斷時，Azure Cosmos DB 可支援[自動容錯移轉](how-to-manage-database-account.md#enable-automatic-failover-for-your-cosmos-account)。 此外，在區域性中斷期間，Cosmos DB 會繼續維持其延遲性、可用性、一致性和輸送量 SLA。 為協助確保整個應用程式的高可用性，Azure Cosmos DB 會提供手動容錯移轉 API 來模擬區域性中斷。 您可以使用此 API 執行一般的業務持續性演練，並做好一切準備。

**全域讀取和寫入延展性**：透過多重主機功能，您可以彈性地調整世界各地的讀取和寫入輸送量。 多重主機功能可確保您應用程式在 Azure Cosmos DB 資料庫或容器上設定的輸送量會傳遞到所有區域，並且受到[費用補償 SLA](https://aka.ms/acdbsla) 的保護。

**多個定義完善的一致性模型**：Azure Cosmos DB 的複寫通訊協定可提供五個定義完善、實用且直覺式的一致性模型，而且每一個在一致性與效能之間都有清楚的權衡考量。 這些一致性模型可讓您輕鬆建置正確的全域散發應用程式。

## <a id="Next Steps"></a>接續步驟

從下列文章中深入了解全域散發：

* [全域散發 - 運作原理](global-dist-under-the-hood.md)
* [如何設定多路連接的用戶端](how-to-manage-database-account.md#configure-clients-for-multi-homing)
* [如何在資料庫中新增/移除區域](how-to-manage-database-account.md#addremove-regions-from-your-database-account)
* [如何建立 SQL API 帳戶的自訂衝突解決原則](how-to-manage-conflicts.md#create-a-custom-conflict-resolution-policy)