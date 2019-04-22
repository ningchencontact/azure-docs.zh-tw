---
title: 一致性層級與 Azure Cosmos DB API
description: 了解 Azure Cosmos DB 中跨 API 的一致性層級。
author: rimman
ms.author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/08/2019
ms.reviewer: sngun
ms.openlocfilehash: 7a8617ae2b01fc89a4c957b8610164a2b53a16f5
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/18/2019
ms.locfileid: "59274768"
---
# <a name="consistency-levels-and-azure-cosmos-db-apis"></a>一致性層級與 Azure Cosmos DB API

Azure Cosmos DB 會提供原生支援的網路通訊協定相容的 Api，針對熱門的資料庫。 這些包括 MongoDB、 Apache Cassandra、 Gremlin 和 Azure 資料表儲存體。 這些資料庫不提供精確地定義的一致性模型或 SLA 為後盾的一致性層級的保證。 它們通常只提供由 Azure Cosmos DB 所提供的五個一致性模型子集。 

使用 SQL API、 Gremlin API 和資料表 API 時，會使用 Azure Cosmos 帳戶上設定的預設一致性層級。 

當使用適用於 MongoDB 的 Cassandra API 或 Azure Cosmos DB 的 API，應用程式會取得一組完整的一致性層級分別提供 Apache Cassandra 和 MongoDB，甚至更強的一致性和持久性保證。 這份文件會顯示對應的 Azure Cosmos DB 一致性層級的 Apache Cassandra 與 MongoDB 的一致性層級。


## <a id="cassandra-mapping"></a>Apache Cassandra 與 Azure Cosmos DB 一致性層級之間的對應

不同於 AzureCosmos DB Apache Cassandra 並不會以原生方式提供精確地定義的一致性保證。  相反地，Apache Cassandra 提供寫入的一致性層級和讀取的一致性層級，若要啟用的高可用性、 一致性和延遲的取捨。 當使用 Azure Cosmos DB 的 Cassandra API: 

* Apache Cassandra 的寫一致性層級會對應至您的 Azure Cosmos 帳戶上設定的預設一致性層級。 

* Azure Cosmos DB 會以動態方式將 Cassandra 用戶端驅動程式，以動態方式設定的讀取要求上的 Azure Cosmos DB 一致性層級的其中一個所指定的讀取的一致性層級的對應。 

下表將說明如何在原生的 Cassandra 一致性層級對應至 Azure Cosmos DB 的一致性層級使用 Cassandra API 時：  

[![Cassandra 的一致性模型對應](./media/consistency-levels-across-apis/consistency-model-mapping-cassandra.png)](./media/consistency-levels-across-apis/consistency-model-mapping-cassandra.png#lightbox)

## <a id="mongo-mapping"></a>MongoDB 和 Azure Cosmos DB 的一致性層級之間的對應

原生 MongoDB 不像 Azure Cosmos DB 中，無法提供精確地定義的一致性保證。 相反地，原生 MongoDB 可讓使用者設定下列的一致性保證： 寫入考量、 讀取的問題和 Ismaster="0 指示詞-導向到主要或次要複本，以達到所需的一致性層級的讀取的作業。 

使用適用於 MongoDB 的 Azure Cosmos DB 的 API，MongoDB 驅動程式會將您的寫入區域視為主要複本和所有其他區域所讀取的複本。 您可以選擇與您的 Azure Cosmos 帳戶作為主要的複本相關聯的區域。 

同時使用適用於 MongoDB 的 Azure Cosmos DB 的 API:

* 寫入考量會對應至您的 Azure Cosmos 帳戶上設定的預設一致性層級。
 
* Azure Cosmos DB 會以動態方式將對應 MongoDB 用戶端驅動程式，以動態方式設定的讀取要求的 Azure Cosmos DB 一致性層級的其中一個所指定的讀取的考量。 

* 您可以標註藉由第一個可寫入區域的區域，與您的 Azure Cosmos 帳戶，作為 「 主要 」 相關聯的特定區域。 

下表說明原生 MongoDB 寫入/讀取考量的如何使用適用於 MongoDB 的 Azure Cosmos DB 的 API 時，會對應至 Azure Cosmos 一致性層級：

[![MongoDB 的一致性模型對應](./media/consistency-levels-across-apis/consistency-model-mapping-mongodb.png)](./media/consistency-levels-across-apis/consistency-model-mapping-mongodb.png#lightbox)

## <a name="next-steps"></a>後續步驟

深入了解 Azure Cosmos DB API 與開放原始碼 API 之間的一致性層級與相容性。 請參閱下列文章：

* [各種一致性層級的可用性和效能權衡取捨](consistency-levels-tradeoffs.md)
* [適用於 MongoDB 的 Azure Cosmos DB API 所支援的 MongoDB 功能](mongodb-feature-support.md)
* [Azure Cosmos DB Cassandra API 支援的 Apache Cassandra 功能](cassandra-support.md)