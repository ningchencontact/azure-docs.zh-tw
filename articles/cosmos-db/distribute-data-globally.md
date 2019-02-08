---
title: 使用 Azure Cosmos DB 全域散發資料
description: 了解如何從 Azure Cosmos DB (全域散發的多模型資料庫服務)，使用全域資料庫進行全球規模的異地複寫、多重主機、容錯移轉及資料復原。
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/26/2018
ms.openlocfilehash: 28c945223d225d7e91df1041bcbe02ee87b93c6a
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/31/2019
ms.locfileid: "55475052"
---
# <a name="global-data-distribution-with-azure-cosmos-db---overview"></a>透過 Azure Cosmos DB 進行全域資料散發 - 概觀

現今的應用程式需要具有快速回應能力，且一律保持線上狀態。 為了達到低延遲和高可用性，這些應用程式的執行個體必須部署在接近使用者的資料中心內。 這些應用程式通常會部署在多個資料中心內，因此稱為全域散發的應用程式。 全域散發的應用程式需要全域散發的資料庫，這類資料庫可以透明方式複寫世界各地的資料，讓應用程式使用接近其使用者的資料複本。 

Azure Cosmos DB 是一個全域散發的資料庫服務，旨在提供低延遲性、有彈性的輸送量延展性、妥善定義以保有資料一致性的語法，以及高可用性。 簡單地說，如果您的應用程式必須確保在世界各地都具有快速回應能力，且一律需保持線上狀態，並且需要無限制且有彈性的輸送量和儲存體延展性，請考慮使用 Azure Cosmos DB 來建置應用程式。

您可以將資料庫設定為全域散發，並且在任何可用的 Azure 區域中使用。 若要降低延遲，請將資料放置於更接近使用者的所在位置。 選擇所需的區域，取決於您應用程式能夠觸達的全域範圍以及使用者所在的位置。 Azure Cosmos 會以透明方式將您帳戶內的資料複寫到所有與您的帳戶相關聯的區域。 它會提供全域散發的 Azure Cosmos 資料庫和容器的單一系統映像，使您的應用程式可以在本機讀取和寫入。 

透過 Azure Cosmos DB，您可以隨時新增或移除任何與您帳戶相關聯的區域。 您不需暫停或重新部署應用程式，就能新增或移除區域。 由於服務所提供的多路連接功能，讓它始終都能持續保持高度可用狀態。

## <a name="key-benefits-of-global-distribution"></a>全域散發的主要優點

**建置全域主動-主動應用程式。** 使用多重主機功能，每個區域都是寫入區域。 它也可以讀取。 多重主機功能也可保證：

- 無限制的彈性寫入延展性。 
- 在世界各地具有 99.999% 的讀取和寫入可用性。
- 保證讀取和寫入會以第 99 百分位數且小於 10 毫秒的方式提供服務。

藉由使用 Azure Cosmos DB 的多路連接 API，您的應用程式就能感知最靠近的區域。 然後可將要求傳送至該區域。 識別最靠近的區域不會變更任何組態。 當您在 Azure Cosmos DB 帳戶中新增及移除區域時，應用程式不需要重新部署。 應用程式可繼續保有高可用性。

**建置高度回應的應用程式。** 您可以輕鬆地將應用程式設計為執行接近即時的讀取和寫入。 它可以針對您為資料庫選擇的所有區域使用單一位數毫秒延遲。 Azure Cosmos DB 會在內部處理區域之間的資料複寫。 因此，保證提供針對 Azure Cosmos DB 帳戶選取的一致性層級。

許多應用程式都受益於多重區域 (本機) 寫入功能帶來的效能提升。 某些需要強式一致性的應用程式偏好將所有寫入傳送到單一區域。 對於這些應用程式，Azure Cosmos DB 支援單一區域和多重區域組態。

**建置高可用性應用程式。** 在數個區域中執行資料庫可提高資料庫的可用性。 如果一個區域無法使用，其他區域就會自動處理應用程式的要求。 Azure Cosmos DB 可為多重區域資料庫提供達 99.999% 的讀取和寫入可用性。

**在區域中斷期間維護商務持續性。** Azure Cosmos DB 支援在區域中斷期間[自動容錯移轉](how-to-manage-database-account.md#automatic-failover)。 在區域性中斷期間，Azure Cosmos DB 會繼續維持其延遲性、可用性、一致性和輸送量 SLA。 為了確保整個應用程式的高可用性，Azure Cosmos DB 會提供手動容錯移轉 API 來模擬區域性中斷。 您可以使用此 API 來進行一般的業務持續性演練。

**全域調整讀取和寫入輸送量。** 透過多重主機功能，您可以彈性地調整世界各地的讀取和寫入輸送量。 多重主機功能可保證您的應用程式在 Azure Cosmos DB 資料庫或容器上設定的輸送量會傳遞到所有區域。 輸送量也會受到[費用補償 SLA](https://aka.ms/acdbsla) 保護。

**從數個定義完善的一致性模型中選擇。** Azure Cosmos DB 複寫通訊協定會提供五個定義完善、實用且直覺式的一致性模型。 每個模型都會在一致性與效能之間進行權衡取捨。 使用這些一致性模型來輕鬆建置全域散發的應用程式。

## <a id="Next Steps"></a>接續步驟

從下列文章中深入了解全域散發：

* [全域散發 - 運作原理](global-dist-under-the-hood.md)
* [設定多路連接的用戶端](how-to-manage-database-account.md#configure-clients-for-multi-homing)
* [從您的 Azure Cosmos 帳戶新增或移除區域](how-to-manage-database-account.md#addremove-regions-from-your-database-account)
* [建立 SQL API 帳戶的自訂衝突解決原則](how-to-manage-conflicts.md#create-a-custom-conflict-resolution-policy)