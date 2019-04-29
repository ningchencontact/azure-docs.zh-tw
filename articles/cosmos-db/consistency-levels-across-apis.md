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
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60892455"
---
# <a name="consistency-levels-and-azure-cosmos-db-apis"></a>一致性層級與 Azure Cosmos DB API

Azure Cosmos DB 为常用数据库提供对与线路协议兼容的 API 的本机支持。 这些数据库包括 MongoDB、Apache Cassandra、Gremlin 和 Azure 表存储。 这些数据库既没有提供准确定义的一致性模型，也没有为一致性级别提供由 SLA 支持的保证。 它們通常只提供由 Azure Cosmos DB 所提供的五個一致性模型子集。 

使用 SQL API、Gremlin API 和表 API 时，会使用 Azure Cosmos 帐户上配置的默认一致性级别。 

當使用適用於 MongoDB 的 Cassandra API 或 Azure Cosmos DB 的 API，應用程式會取得一組完整的一致性層級分別提供 Apache Cassandra 和 MongoDB，甚至更強的一致性和持久性保證。 本文档介绍了与 Apache Cassandra 和 MongoDB 一致性级别对应的 Azure Cosmos DB 一致性级别。


## <a id="cassandra-mapping"></a>Apache Cassandra 與 Azure Cosmos DB 一致性層級之間的對應

与 AzureCosmos DB 不一样，Apache Cassandra 并不以原生方式提供精确定义的一致性保证。  与之相反，Apache Cassandra 提供一个写入一致性级别和一个读取一致性级别，以便进行高可用性、一致性和延迟方面的权衡。 當使用 Azure Cosmos DB 的 Cassandra API: 

* Apache Cassandra 的写入一致性级别映射到在 Azure Cosmos 帐户上配置的默认一致性级别。 

* Azure Cosmos DB 会将 Cassandra 客户端驱动程序指定的读取一致性级别动态映射到根据读取请求动态配置的某个 Azure Cosmos DB 一致性级别。 

下表將說明如何在原生的 Cassandra 一致性層級對應至 Azure Cosmos DB 的一致性層級使用 Cassandra API 時：  

[![Cassandra 一致性模型映射](./media/consistency-levels-across-apis/consistency-model-mapping-cassandra.png)](./media/consistency-levels-across-apis/consistency-model-mapping-cassandra.png#lightbox)

## <a id="mongo-mapping"></a>MongoDB 和 Azure Cosmos DB 的一致性層級之間的對應

与 Azure Cosmos DB 不一样，本机 MongoDB 并不提供精确定义的一致性保证。 与之相反，本机 MongoDB 允许用户配置下述一致性保证：写入关注、读取关注以及 isMaster 指令 - 目的是将读取操作定向到主副本或辅助副本，以便实现所需的一致性级别。 

使用適用於 MongoDB 的 Azure Cosmos DB 的 API，MongoDB 驅動程式會將您的寫入區域視為主要複本和所有其他區域所讀取的複本。 可以选择将哪个与 Azure Cosmos 帐户关联的区域作为主副本。 

同時使用適用於 MongoDB 的 Azure Cosmos DB 的 API:

* 写入关注映射到在 Azure Cosmos 帐户上配置的默认一致性级别。
 
* Azure Cosmos DB 会将 MongoDB 客户端驱动程序指定的读取关注动态映射到根据读取请求动态配置的某个 Azure Cosmos DB 一致性级别。 

* 可以将与 Azure Cosmos 帐户关联的特定区域批注为“主区域”，方法是将该区域设置为第一个可写区域。 

下表說明原生 MongoDB 寫入/讀取考量的如何使用適用於 MongoDB 的 Azure Cosmos DB 的 API 時，會對應至 Azure Cosmos 一致性層級：

[![MongoDB 一致性模型映射](./media/consistency-levels-across-apis/consistency-model-mapping-mongodb.png)](./media/consistency-levels-across-apis/consistency-model-mapping-mongodb.png#lightbox)

## <a name="next-steps"></a>後續步驟

深入了解 Azure Cosmos DB API 與開放原始碼 API 之間的一致性層級與相容性。 請參閱下列文章：

* [各種一致性層級的可用性和效能權衡取捨](consistency-levels-tradeoffs.md)
* [適用於 MongoDB 的 Azure Cosmos DB API 所支援的 MongoDB 功能](mongodb-feature-support.md)
* [Azure Cosmos DB Cassandra API 支援的 Apache Cassandra 功能](cassandra-support.md)