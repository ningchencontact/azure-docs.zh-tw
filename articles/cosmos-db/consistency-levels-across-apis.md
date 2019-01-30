---
title: 一致性層級與 Azure Cosmos DB API
description: 了解 Azure Cosmos DB 中跨 API 的一致性層級。
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/23/2018
ms.reviewer: sngun
ms.openlocfilehash: 4d2994ea6ab6d6472ec56f0f2e378062590c8920
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2019
ms.locfileid: "54806992"
---
# <a name="consistency-levels-and-azure-cosmos-db-apis"></a>一致性層級與 Azure Cosmos DB API

這五個由 Azure Cosmos DB 所提供的一致性模型都會受到 Azure Cosmos DB SQL API 原生支援。 當您使用 Azure Cosmos DB 時，SQL API 是預設值。 

Azure Cosmos DB 也會針對熱門資料庫來為與網路通訊協定相容的 API 提供原生支援。 這些資料庫包括 MongoDB、Apache Cassandra、Gremlin 和 Azure 資料表儲存體。 這些資料庫不提供精確定義的一致性模型，也不提供對於一致性層級的 SLA 支援保證。 它們通常只提供由 Azure Cosmos DB 所提供的五個一致性模型子集。 對於 SQL API、Gremlin API 及資料表 API，會使用設定於 Azure Cosmos DB 帳戶上的預設一致性層級。 

下列各節顯示 OSS 用戶端驅動程式針對 Apache Cassandra 4.x 和 MongoDB 3.4 所要求之資料一致性間的對應。 這份文件也會顯示 Apache Cassandra 與 MongoDB 的對應 Azure Cosmos DB 一致性層級。

## <a id="cassandra-mapping"></a>Apache Cassandra 與 Azure Cosmos DB 一致性層級之間的對應

下表顯示 Apache Cassandra 與 Azure Cosmos DB 中一致性層級之間的一致性對應。 針對每個 Cassandra 讀取和寫入的一致性層級，對應的 Cosmos DB 一致性層級可提供更強，也就是更嚴格的保證。

下表顯示 Azure Cosmos DB 和 Cassandra 之間的**寫入一致性對應**：

| Cassandra | Azure Cosmos DB | 保證 |
| - | - | - |
|ALL|強式  | 線性化能力 |
| EACH_QUORUM   | 強式    | 線性化能力 | 
| QUORUM、SERIAL |  強式 |    線性化能力 |
| LOCAL_QUORUM、THREE、TWO、ONE、LOCAL_ONE、ANY | 一致前置詞 |全域一致前置詞 |
| EACH_QUORUM   | 強式    | 線性化能力 |
| QUORUM、SERIAL |  強式 |    線性化能力 |
| LOCAL_QUORUM、THREE、TWO、ONE、LOCAL_ONE、ANY | 一致前置詞 | 全域一致前置詞 |
| QUORUM、SERIAL | 強式   | 線性化能力 |
| LOCAL_QUORUM、THREE、TWO、ONE、LOCAL_ONE、ANY | 一致前置詞 | 全域一致前置詞 |
| LOCAL_QUORUM、LOCAL_SERIAL、TWO、THREE    | 限定過期 | <ul><li>限定過期。</li><li>在大部分的 K 版本或 t 時間之後。</li><li>讀取區域中最新的認可值。</li></ul> |
| ONE、LOCAL_ONE、ANY   | 一致前置詞 | 每個區域一致前置詞 |

下表顯示 Azure Cosmos DB 和 Cassandra 之間的**讀取一致性對應**：

| Cassandra | Azure Cosmos DB | 保證 |
| - | - | - |
| ALL、QUORUM、SERIAL、LOCAL_QUORUM、LOCAL_SERIAL、THREE、TWO、ONE、LOCAL_ONE | 強式  | 線性化能力|
| ALL、QUORUM、SERIAL、LOCAL_QUORUM、LOCAL_SERIAL、THREE、TWO   |強式 |   線性化能力 |
|LOCAL_ONE、ONE | 一致前置詞 | 全域一致前置詞 |
| ALL、QUORUM、SERIAL   | 強式    | 線性化能力 |
| LOCAL_ONE、ONE、LOCAL_QUORUM、LOCAL_SERIAL、TWO、THREE |  一致前置詞   | 全域一致前置詞 |
| LOCAL_ONE、ONE、TWO、THREE、LOCAL_QUORUM、QUORUM |    一致前置詞   | 全域一致前置詞 |
| ALL、QUORUM、SERIAL、LOCAL_QUORUM、LOCAL_SERIAL、THREE、TWO   |強式 |   線性化能力 |
| LOCAL_ONE、ONE    | 一致前置詞 | 全域一致前置詞|
| ALL、QUORUM、SERIAL   Strong  Linearizability
LOCAL_ONE、ONE、LOCAL_QUORUM、LOCAL_SERIAL、TWO、THREE  |一致前置詞  | 全域一致前置詞 |
|ALL    |強式 |線性化能力 |
| LOCAL_ONE、ONE、TWO、THREE、LOCAL_QUORUM、QUORUM  |一致前置詞  |全域一致前置詞|
|ALL、QUORUM、SERIAL    Strong  Linearizability
LOCAL_ONE、ONE、LOCAL_QUORUM、LOCAL_SERIAL、TWO、THREE  |一致前置詞  |全域一致前置詞 |
|ALL    |強式 | 線性化能力 |
| LOCAL_ONE、ONE、TWO、THREE、LOCAL_QUORUM、QUORUM  | 一致前置詞 | 全域一致前置詞 |
| QUORUM、LOCAL_QUORUM、LOCAL_SERIAL、TWO、THREE |  限定過期   | <ul><li>限定過期。</li><li>在大部分的 K 版本或 t 時間之後。 </li><li>讀取區域中最新的認可值。</li></ul>
| LOCAL_ONE、ONE |一致前置詞 | 每個區域一致前置詞 |
| LOCAL_ONE、ONE、TWO、THREE、LOCAL_QUORUM、QUORUM  | 一致前置詞 | 每個區域一致前置詞 |


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