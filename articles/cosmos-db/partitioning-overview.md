---
title: Azure Cosmos DB 中的資料分割
description: Azure Cosmos DB 中的資料分割概觀
author: aliuy
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/30/2018
ms.author: andrl
ms.openlocfilehash: cc4b165b4fa04e22c9c57547df1657a0c6fc4537
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/07/2018
ms.locfileid: "51263373"
---
# <a name="partitioning-in-azure-cosmos-db"></a>Azure Cosmos DB 中的資料分割

資料分割是 Cosmos DB 用來調整資料庫中的個別容器以符合您應用程式效能需求的技術。 藉由使用資料分割，將容器中的項目分割為不同子集，稱為「邏輯分割區」。 邏輯分割區會根據與每個項目相關聯的分割區索引鍵屬性值來建立。

邏輯分割區是容器中不同的項目子集。 邏輯分割區中的項目會依分割區索引鍵值來識別，該值會由邏輯分割區中的所有項目共用。  例如，假設有一個容器會保留文件，而且每份文件都具有 `UserID` 屬性。  如果 `UserID` 會用來作為容器中項目的分割區索引鍵，而且有 1000 個唯一的 `UserID` 值，將會為該容器建立 1000 個邏輯分割區。

容器中的每個項目都有可決定**邏輯分割區**的**分割區索引鍵**，而且每個項目也都有**項目識別碼** (這在邏輯分割區內是唯一的)。  項目的**索引**可唯一識別該項目，而且會藉由結合分割區索引鍵和項目識別碼來形成該項目。

選擇分割區索引鍵，是將會影響應用程式效能的重要決策。  若要深入了解，請參閱[選擇分割區索引鍵](partitioning-overview.md#choose-partitionkey)一文以取得詳細指引。

## <a name="logical-partition-management"></a>邏輯分割區管理

Cosmos DB 會以透明的方式自動管理在實體分割區 (伺服器基礎結構) 上放置邏輯分割區的位置，以便有效率地滿足容器的延展性和效能需求。 當應用程式的輸送量和儲存體需求增加時，Cosmos DB 就會移動邏輯分割區，自動將負載分散到更多伺服器上。 若要深入了解 Cosmos DB 管理分割區的方式，請參閱[邏輯分割區](partition-data.md)一文。 您不需了解這些詳細資料，就能建置或執行您的應用程式。

Cosmos DB 會使用雜湊型分割，來將邏輯分割區分散到實體分割區上。  Cosmos DB 會對項目的分割區索引鍵值進行雜湊，而雜湊的結果會決定實體分割區。 Cosmos DB 會在 'N' 個實體分割區上平均配置分割區索引鍵雜湊的索引鍵空間。

在單一分割區中存取資料的查詢，會比存取多個分割區的查詢更符合成本效益。 針對單一邏輯分割區中的項目，僅允許預存程序或觸發程序中的交易。  

## <a id="choose-partitionkey"></a>選擇分割區索引鍵

選擇分割區索引鍵時，請考慮下列詳細資料：

* 單一邏輯分割區的儲存體允許上限為 10 GB。  

* 針對已分割的容器設定的最低輸送量為 400 個 RU/秒。 對相同分割區索引鍵提出的要求不能超過配置給分割區的輸送量。 如果它們超過配置的輸送量，則要求將會受到速率限制。 因此，請務必挑選不會在應用程式內產生「作用點」的分割區索引鍵。

* 選擇分割區索引鍵，將工作負載平均分散到所有分割區，並隨著時間平均分散。  您的分割區索引鍵選項應該基於將項目散發到多個分割區以達成延展性的目標，來兼顧高效率的分割區查詢和/或交易。

* 選擇具有各種不同值的分割區索引鍵，以及存取平均分散到邏輯分割區的模式。 基本概念是將容器中的資料和活動分散到這組邏輯分割區，以便將適用於資料儲存體和輸送量的資源散發到邏輯分割區。

* 分割區索引鍵的候選項目可能包含經常在您的查詢中顯示為篩選的屬性。 可藉由在篩選述詞中包含分割區索引鍵，有效地路由傳送查詢。

## <a name="next-steps"></a>後續步驟

* 了解如何[選擇分割區索引鍵](partitioning-overview.md#choose-partitionkey)
* 了解[分割區](partition-data.md)
* 了解 [Azure Cosmos DB 中佈建的輸送量](request-units.md)
* 了解 [Azure Cosmos DB 中的全域分散](distribute-data-globally.md)
