---
title: 使用適用於 MongoDB 的 Azure Cosmos DB 的 API 時的移轉後的最佳化步驟
description: 此文件從 MongoDB，Azure Cosmos DB 的 api 的 Mongo DB，提供的移轉後的最佳化技術。
author: roaror
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: roaror
ms.openlocfilehash: c0c761fef481a1fdaa027f1329e9a4e72d62985a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61331194"
---
# <a name="post-migration-optimization-steps-when-using-azure-cosmos-dbs-api-for-mongodb"></a>使用適用於 MongoDB 的 Azure Cosmos DB 的 API 時的移轉後的最佳化步驟 

將 MongoDB 資料庫中儲存的資料遷移至 Azure Cosmos DB 的 MongoDB 版 API 之後，您就可以連線到 Azure Cosmos DB 並管理這些資料。 本指南提供您應該考慮在移轉之後的步驟。 請參閱[移轉至 Azure Cosmos DB 的 MongoDB 教學課程中的 API 的 MongoDB](../dms/tutorial-mongodb-cosmos-db.md)移轉步驟。

在本指南中，您將了解如何：
- [連接您的應用程式](#connect-account)
- [最佳化編製索引原則](#indexing)
- [設定適用於 Azure Cosmos DB 的 API for MongoDB 的全域散發](#distribute-data)
- [設定一致性層級](#consistency)

> [!NOTE]
> 唯一的強制的移轉後步驟，在您的應用程式層級會變更您的應用程式，以指向新的 Azure Cosmos DB 帳戶中的連接字串。 其他所有移轉步驟被都建議的最佳化。
>

## <a id="connect-account"></a>連接您的應用程式 

1. 在新的視窗登入[Azure 入口網站](https://www.portal.azure.com/)
2. 從[Azure 入口網站](https://www.portal.azure.com/)，在左窗格中開啟**所有資源**功能表，並尋找您要移轉您的資料的 Azure Cosmos DB 帳戶。
3. 開啟**連接字串**刀鋒視窗。 右窗格中有您成功連接到您的帳戶所需的所有資訊。
4. 使用您的應用程式設定 （或其他相關的位置） 以反映 Azure Cosmos DB API 中的連接資訊給您的應用程式中的 MongoDB 連接。 
![Connection-String](./media/mongodb-post-migration/connection-string.png)

如需詳細資訊，請參閱[連接到 Azure Cosmos DB 的 MongoDB 應用程式](connect-mongodb-account.md)頁面。

## <a id="indexing"></a>最佳化編製索引原則

所有資料欄位時自動編製都索引，根據預設，移轉到 Azure Cosmos DB 的資料。 在許多情況下，此索引編製原則的預設值是可接受的。 一般情況下，移除索引最佳化寫入要求，並具有預設值編製索引原則 （亦即，自動編製索引） 會最佳化讀取的要求。

如需有關如何編製索引的詳細資訊，請參閱 <<c0> [ 資料編製索引在 Azure Cosmos DB 的 API for MongoDB](mongodb-indexing.md) ，以及[Azure Cosmos DB 中編製索引](index-overview.md)文章。

## <a id="distribute-data"></a>全域散發您的資料

Azure Cosmos DB 可用於所有[Azure 區域](https://azure.microsoft.com/regions/#services)全球。 選取您的 Azure Cosmos DB 帳戶的預設一致性層級之後, 您可以建立一或多個 Azure 區域關聯 （根據您的全域散發的需求）。 如需高可用性和商務持續性，我們一律建議至少 2 個區域中執行。 您可以檢閱的秘訣[最佳化的 Azure Cosmos DB 中的多區域部署成本](optimize-cost-regions.md)。

若要全域散發您的資料，請參閱[散發適用於 MongoDB 的 Azure Cosmos DB 的 API 上的全域資料](tutorial-global-distribution-mongodb.md)。 

## <a id="consistency"></a>設定一致性層級
Azure Cosmos DB 提供定義完善的 5[一致性層級](consistency-levels.md)。 若要深入了解 MongoDB 和 Azure Cosmos DB 的一致性層級之間的對應，請閱讀[一致性層級和 Azure Cosmos DB Api](consistency-levels-across-apis.md)。 預設一致性層級是工作階段一致性層級。 一致性層級變更為選用，您可以最佳化您的應用程式。 若要變更使用 Azure 入口網站的一致性層級：

1. 移至**預設一致性**刀鋒視窗中 [設定] 下的。
2. 選取您[一致性層級](consistency-levels.md)

大部分的使用者將保留其預設工作階段一致性設定的一致性層級。 不過，還有[各種一致性層級的可用性和效能取捨](consistency-levels-tradeoffs.md)。 

## <a name="next-steps"></a>後續步驟

* [將 MongoDB 應用程式連線至 Azure Cosmos DB](connect-mongodb-account.md)
* [連接到 Azure Cosmos DB 帳戶使用 Studio 3T](mongodb-mongochef.md)
* [如何全域散發讀取使用 Azure Cosmos DB 的 API for MongoDB](mongodb-readpreference.md)
* [MongoDB 的過期資料，使用 Azure Cosmos DB 的 API](mongodb-time-to-live.md)
* [Azure Cosmos DB 中的一致性層級](consistency-levels.md)
* [Azure Cosmos DB 中的編製索引](index-overview.md)
* [Azure Cosmos DB 中的要求單位](request-units.md)





