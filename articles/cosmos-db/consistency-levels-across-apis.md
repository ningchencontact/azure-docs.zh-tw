---
title: 一致性層級和 Azure Cosmos DB API | Microsoft Docs
description: 了解 Azure Cosmos DB 中跨 API 的一致性層級。
keywords: 一致性, azure cosmos db, azure, 模型, mongodb, cassandra, 圖表, 資料表, Microsoft azure
services: cosmos-db
author: markjbrown
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 10/23/2018
ms.author: mjbrown
ms.openlocfilehash: 974531cd5907e4f69e7d064125d3e51fa4974949
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/02/2018
ms.locfileid: "50956378"
---
# <a name="consistency-levels-and-azure-cosmos-db-apis"></a>一致性層級與 Azure Cosmos DB API

這五個由 Azure Cosmos DB 所提供的一致性模型都會受到 Cosmos DB SQL API 原生支援，這是使用 Cosmos DB 時的預設 API。 除了 SQL API，Cosmos DB 也會針對熱門資料庫 (例如 MongoDB、Apache Cassandra、Gremlin 和 Azure 資料表) 來為與網路通訊協定相容的 API 提供原生支援。 這些資料庫都不提供精確定義的一致性模型，也不提供對於一致性層級的 SLA 支援保證。 這些資料庫通常只提供由 Cosmos DB 所提供的五個一致性模型子集。 對於 SQL API、Gremlin API 及資料表 API，會使用您在 Cosmos 帳戶上設定的預設一致性層級。

下列各節顯示在分別使用 Cassandra API 和 MongoDB API 以及對應的 Cosmos DB 一致性層級時，OSS 用戶端驅動程式針對 Apache Cassandra 4.x 和 MongoDB 3.4 所要求之資料一致性間的對應。

## <a id="cassandra-mapping"></a>Apache Cassandra 與 Cosmos DB 一致性層級之間的對應

下表顯示適用於多個區域和單一區域部署之 Apache Cassandra 4.x 用戶端和 Cosmos DB 中預設一致性層級之間的「讀取一致性」對應。

| **Apache Cassandra 4.x** | **Cosmos DB (多個區域)** | **Cosmos DB (單一區域)** |
| - | - | - |
| ONE、TWO、THREE | 一致前置詞 | 一致前置詞 |
| LOCAL_ONE | 一致前置詞 | 一致前置詞 |
| QUORUM、ALL、SERIAL | 限定過期 (預設值) 或強式 (在個人預覽版中) | 強式 |
| LOCAL_QUORUM | 限定過期 | 強式 |
| LOCAL_SERIAL | 限定過期 | 強式 |

## <a id="mongo-mapping"></a>MongoDB 3.4 和 Cosmos DB 一致性層級之間的對應

下表顯示適用於多個區域和單一區域部署之 MongoDB 3.4 和 Cosmos DB 中預設一致性層級之間的「讀取考量」對應。

| **MongoDB 3.4** | **Cosmos DB (多個區域)** | **Cosmos DB (單一區域)** |
| - | - | - |
| 線性 | 強式 | 強式 |
| 多數 | 限定過期 | 強式 |
| 本機 | 一致前置詞 | 一致前置詞 |

## <a name="next-steps"></a>後續步驟

在下列文章中，深入了解 Cosmos DB API 與開放原始碼 API 之間的一致性層級與相容性：

* [各種一致性層級的可用性和效能權衡取捨](consistency-levels-tradeoffs.md)
* [Cosmos DB MongoDB API 支援的 MongoDB 功能](mongodb-feature-support.md)
* [Cosmos DB Cassandra API 支援的 Apache Cassandra 功能](cassandra-support.md)