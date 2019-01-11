---
title: 一致性層級與 Azure Cosmos DB API
description: 了解 Azure Cosmos DB 中跨 API 的一致性層級。
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/23/2018
ms.reviewer: sngun
ms.openlocfilehash: 76ebbc8cc8dbea4b7f8f8226cf1d8570a421e8cf
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/04/2019
ms.locfileid: "54034330"
---
# <a name="consistency-levels-and-azure-cosmos-db-apis"></a>一致性層級與 Azure Cosmos DB API

這五個由 Azure Cosmos DB 所提供的一致性模型都會受到 Azure Cosmos DB SQL API 原生支援。 當您使用 Azure Cosmos DB 時，SQL API 是預設值。 

Azure Cosmos DB 也會針對熱門資料庫來為與網路通訊協定相容的 API 提供原生支援。 這些資料庫包括 MongoDB、Apache Cassandra、Gremlin 和 Azure 資料表儲存體。 這些資料庫不提供精確定義的一致性模型，也不提供對於一致性層級的 SLA 支援保證。 它們通常只提供由 Azure Cosmos DB 所提供的五個一致性模型子集。 對於 SQL API、Gremlin API 及資料表 API，會使用設定於 Azure Cosmos DB 帳戶上的預設一致性層級。 

下列各節顯示 OSS 用戶端驅動程式針對 Apache Cassandra 4.x 和 MongoDB 3.4 所要求之資料一致性間的對應。 這份文件也會顯示 Apache Cassandra 與 MongoDB 的對應 Azure Cosmos DB 一致性層級。

## <a id="cassandra-mapping"></a>Apache Cassandra 與 Azure Cosmos DB 一致性層級之間的對應

下表顯示 Apache Cassandra 4.x 用戶端與 Azure Cosmos DB 中預設一致性層級之間的「讀取一致性」對應。 下表顯示多個區域和單一區域的部署。

| **Apache Cassandra 4.x** | **Azure Cosmos DB (多個區域)** | **Azure Cosmos DB (單一區域)** |
| - | - | - |
| ONE、TWO、THREE | 一致的前置詞 | 一致的前置詞 |
| LOCAL_ONE | 一致的前置詞 | 一致的前置詞 |
| QUORUM、ALL、SERIAL | 限定過期為預設值。 Strong 為個人預覽版。 | 強式 |
| LOCAL_QUORUM | 界限-陳舊 | 強式 |
| LOCAL_SERIAL | 界限-陳舊 | 強式 |

## <a id="mongo-mapping"></a>MongoDB 3.4 和 Azure Cosmos DB 一致性層級之間的對應

下表顯示 MongoDB 3.4 與 Azure Cosmos DB 中預設一致性層級之間的「讀取考量」對應。 下表顯示多個區域和單一區域的部署。

| **MongoDB 3.4** | **Azure Cosmos DB (多個區域)** | **Azure Cosmos DB (單一區域)** |
| - | - | - |
| 線性 | 強式 | 強式 |
| 多數 | 界限-陳舊 | 強式 |
| 本機 | 一致的前置詞 | 一致的前置詞 |

## <a name="next-steps"></a>後續步驟

深入了解 Azure Cosmos DB API 與開放原始碼 API 之間的一致性層級與相容性。 請參閱下列文章：

* [各種一致性層級的可用性和效能權衡取捨](consistency-levels-tradeoffs.md)
* [適用於 MongoDB 的 Azure Cosmos DB API 所支援的 MongoDB 功能](mongodb-feature-support.md)
* [Azure Cosmos DB Cassandra API 支援的 Apache Cassandra 功能](cassandra-support.md)