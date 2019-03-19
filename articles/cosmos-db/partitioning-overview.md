---
title: Azure Cosmos DB 中的資料分割
description: Azure Cosmos DB 中的資料分割的概觀。
ms.author: mjbrown
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/30/2018
ms.openlocfilehash: a8db510bea57fa3d6ee873571e586bcef7508b26
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2019
ms.locfileid: "56961630"
---
# <a name="partitioning-in-azure-cosmos-db"></a>Azure Cosmos DB 中的資料分割

Azure Cosmos DB 使用資料分割中的資料庫，以符合您的應用程式的效能需求調整個別的容器。 在分割中，在容器中的項目分成不同的子集，稱為*邏輯磁碟分割*。 邏輯磁碟分割上的值形成基礎*分割區索引鍵*與容器中的每個項目相關聯。 邏輯分割區中的所有項目具有相同的資料分割索引鍵的值。

例如，容器會保留文件。 每份文件具有的唯一值`UserID`屬性。 如果`UserID`容器中的項目可做為資料分割索引鍵，而且有唯一的 1,000`UserID`值，為容器建立 1,000 個邏輯分割區。

決定項目的邏輯資料分割資料分割索引鍵，除了在容器中的每個項目具有*項目識別碼*（邏輯磁碟分割內唯一）。 合併資料分割索引鍵和項目 ID 建立的項目*index*，可唯一識別項目。

[選擇資料分割索引鍵](partitioning-overview.md#choose-partitionkey)是一項重要的決策會影響您的應用程式效能。

## <a name="managing-logical-partitions"></a>管理邏輯磁碟分割

Azure Cosmos DB 會以透明的方式並自動管理實體的資料分割 （您的伺服器基礎結構） 來有效率地滿足的延展性和效能需求的容器上的邏輯磁碟分割的位置。 當應用程式的輸送量和儲存體需求增加時，Azure Cosmos DB 會移動到自動負載分散到大量的伺服器邏輯磁碟分割。 

Azure Cosmos DB 使用雜湊式資料分割來分散到實體分割區的邏輯磁碟分割。 Azure Cosmos DB 會雜湊資料分割索引鍵值的項目。 雜湊的結果會決定實體的資料分割。 然後，Azure Cosmos DB 會配置資料分割的索引鍵空間索引鍵雜湊平均分散到實體分割區。

在單一分割區中存取資料的查詢，會比存取多個分割區的查詢更符合成本效益。 只能針對單一的邏輯分割區中的項目允許交易 （在預存程序或觸發程序）。

若要深入了解 Azure Cosmos DB 管理資料分割的方式，請參閱[邏輯磁碟分割](partition-data.md)。 （它不需要了解這些詳細資料，以建置或執行您的應用程式）。

## <a id="choose-partitionkey"></a>選擇分割區索引鍵

當您選擇分割索引鍵時，請考慮下列詳細資料：

* 單一的邏輯分割區有 10 gb 的儲存體上限。  

* 資料分割的容器具有最小輸送量 （RU/秒） 秒 400 個要求單位。 相同的資料分割索引鍵的要求不能超過配置給資料分割的輸送量。 如果要求超過已配置的輸送量，要求就會是速率限制。 因此，請務必挑選不會在應用程式內產生「作用點」的分割區索引鍵。

* 選擇資料分割索引鍵，將平均分散工作負載，在所有資料分割，且平均經過一段時間。 您選擇的資料分割索引鍵應兼顧高效率的資料分割的查詢和交易，將項目分散到多個資料分割達到延展性目標。

* 選擇具有各種不同值的分割區索引鍵，以及存取平均分散到邏輯分割區的模式。 這有助於分散的邏輯資料分割集合中的資料和您的容器中的活動，讓資料儲存體和輸送量的資源可以分散於邏輯磁碟分割。

* 資料分割索引鍵候選項目可能會包含做為篩選條件查詢中經常出現的屬性。 可藉由在篩選述詞中包含分割區索引鍵，有效地路由傳送查詢。

## <a name="next-steps"></a>後續步驟

* 深入了解[分割區](partition-data.md)。
* 了解 [Azure Cosmos DB 中佈建的輸送量](request-units.md)。
* 了解 [Azure Cosmos DB 中的全域分散](distribute-data-globally.md)。
