---
title: 使用 Azure Cosmos DB 全域散發資料
description: 了解如何從 Azure Cosmos DB (全域散發的多模型資料庫服務)，使用全域資料庫進行全球規模的異地複寫、多重主機、容錯移轉及資料復原。
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/20/2019
ms.openlocfilehash: e58a8cd286e4d416dd5f4e6d3fddedf1897fed1c
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/20/2019
ms.locfileid: "65954164"
---
# <a name="global-data-distribution-with-azure-cosmos-db---overview"></a>透過 Azure Cosmos DB 進行全域資料散發 - 概觀

現今的應用程式需要具有快速回應能力，且一律保持線上狀態。 為了達到低延遲和高可用性，這些應用程式的執行個體必須部署在接近使用者的資料中心內。 這些應用程式通常會部署在多個資料中心內，因此稱為全域散發的應用程式。 全域散發的應用程式需要全域散發的資料庫，這類資料庫可以透明方式複寫世界各地的資料，讓應用程式使用接近其使用者的資料複本。 

Azure Cosmos DB 是一個全域散發的資料庫服務，旨在提供低延遲性、有彈性的輸送量延展性、妥善定義以保有資料一致性的語法，以及高可用性。 簡單地說，如果您的應用程式需要保證的快速回應時間隨處在世界中，只有在必須一律在線上，而且需要無限制且彈性的輸送量和儲存體的延展性，您應該建置 Azure Cosmos DB 上的應用程式。

您可以將資料庫設定為全域散發，並且在任何可用的 Azure 區域中使用。 若要降低延遲，將靠近使用者所在的資料。 選擇所需的區域，取決於您應用程式能夠觸達的全域範圍以及使用者所在的位置。 Cosmos DB 會自動將資料複寫至您的 Cosmos 帳戶相關聯的所有區域。 它會提供全域散發的 Azure Cosmos 資料庫和容器的單一系統映像，使您的應用程式可以在本機讀取和寫入。 

透過 Azure Cosmos DB，您可以隨時新增或移除任何與您帳戶相關聯的區域。 您不需暫停或重新部署應用程式，就能新增或移除區域。 它會繼續為高度可用的所有時間因為服務原生提供的多路連接功能。

![高可用性的部署拓撲](./media/distribute-data-globally/deployment-topology.png)

## <a name="key-benefits-of-global-distribution"></a>全域散發的主要優點

**建置全域主動-主動應用程式。** 使用其嶄新的多重主機複寫通訊協定中，每個區域支援寫入和讀取。 也可讓多重主機功能：

- 無限制的彈性寫入和讀取延展性。 
- 在世界各地具有 99.999% 的讀取和寫入可用性。
- 保證讀取和寫入會以第 99 百分位數且小於 10 毫秒的方式提供服務。

使用 Azure Cosmos DB 的多路連接 Api，您的應用程式知道最接近的區域，以及可以將要求傳送到該區域。 識別最靠近的區域不會變更任何組態。 您新增及移除區域，以，並從您的 Azure Cosmos 帳戶，您的應用程式不需要重新部署或已暫停，仍能在任何時間都高度可用。

**建置高度回應的應用程式。** 您的應用程式可以執行接近即時的讀取，然後選擇您的資料庫針對所有區域的寫入。 Azure Cosmos DB 在內部處理使用一致性層級的保證層級，您已選取的區域之間的資料複寫。

**建置高可用性應用程式。** 執行全球多個區域中資料庫時，會增加資料庫的可用性。 如果一個區域無法使用，其他區域就會自動處理應用程式的要求。 Azure Cosmos DB 可為多重區域資料庫提供達 99.999% 的讀取和寫入可用性。

**在區域中斷期間維護商務持續性。** Azure Cosmos DB 支援在區域中斷期間[自動容錯移轉](how-to-manage-database-account.md#automatic-failover)。 在區域性中斷期間，Azure Cosmos DB 會繼續維持其延遲性、可用性、一致性和輸送量 SLA。 若要可協助您確認整個應用程式為高可用性，Cosmos DB 會提供手動的容錯移轉來模擬區域性中斷的 API。 您可以使用此 API 來進行一般的業務持續性演練。

**全域調整讀取和寫入輸送量。** 您可以啟用為可寫入及彈性調整世界各地的 讀取和寫入的每個區域。 您的應用程式設定的 Azure Cosmos 資料庫或容器的輸送量保證傳遞在您的 Azure Cosmos 帳戶相關聯的所有區域。 佈建的輸送量，會由保證向上[有財務支援的 Sla](https://aka.ms/acdbsla)。

**從數個定義完善的一致性模型中選擇。** Azure Cosmos DB 複寫通訊協定會提供五個定義完善、實用且直覺式的一致性模型。 每個模型都會在一致性與效能之間進行權衡取捨。 使用這些一致性模型來輕鬆建置全域散發的應用程式。

## <a id="Next Steps"></a>接續步驟

從下列文章中深入了解全域散發：

* [全域散發 - 運作原理](global-dist-under-the-hood.md)
* [如何在應用程式中設定多重主機](how-to-multi-master.md)
* [設定多路連接的用戶端](how-to-manage-database-account.md#configure-multiple-write-regions)
* [從您的 Azure Cosmos 帳戶新增或移除區域](how-to-manage-database-account.md#addremove-regions-from-your-database-account)
* [建立 SQL API 帳戶的自訂衝突解決原則](how-to-manage-conflicts.md#create-a-custom-conflict-resolution-policy)