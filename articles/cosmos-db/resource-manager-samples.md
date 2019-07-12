---
title: 適用於 Azure Cosmos DB 的 azure Resource Manager 範本
description: 使用 Azure Resource Manager 範本來建立和設定 Azure Cosmos DB。
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: mjbrown
ms.openlocfilehash: 4a32b0497d2457a740e9c082f990bb9112208bfd
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "65969173"
---
# <a name="azure-resource-manager-templates-for-azure-cosmos-db"></a>適用於 Azure Cosmos DB 的 azure Resource Manager 範本

下表包含適用於 Azure Cosmos DB 的 Azure Resource Manager 範本的連結：

## <a name="sql-core-api"></a>SQL (Core) API

|**範本**|**描述**|
|---| ---|
|[建立 Azure Cosmos 帳戶、 資料庫、 容器](manage-sql-with-resource-manager.md#create-resource) | 此範本會建立兩個區域中透過啟用多個主要 （核心） 的 SQL API 帳戶。 Azure Cosmos 帳戶會有兩個容器的共用資料庫層級的輸送量。 |
|[更新資料庫的輸送量 （RU/秒）](manage-sql-with-resource-manager.md#database-ru-update) | 此範本會更新資料庫中 （核心） 的 SQL API 帳戶的輸送量。 |
|[更新容器的輸送量 （RU/秒）](manage-sql-with-resource-manager.md#container-ru-update) | 此範本更新 （核心） 的 SQL API 帳戶中的容器的輸送量。 |

## <a name="mongodb-api"></a>MongoDB API

|**範本**|**說明**|
|---| ---|
|[建立 Azure Cosmos 帳戶、 資料庫、 集合](manage-mongodb-with-resource-manager.md#create-resource) | 此範本會建立兩個區域中適用於 MongoDB 的 Azure Cosmos DB API 使用多重主機已啟用的帳戶。 Azure Cosmos 帳戶會有兩個容器的共用資料庫層級的輸送量。 |
|[更新資料庫的輸送量 （RU/秒）](manage-mongodb-with-resource-manager.md#database-ru-update) | 此範本會更新資料庫中的 MongoDB API 帳戶的輸送量。 |
|[更新集合的輸送量 （RU/秒）](manage-mongodb-with-resource-manager.md#collection-ru-update) | 此範本更新為 MongoDB API 帳戶中的容器輸送量。 |

## <a name="cassandra-api"></a>Cassandra API

|**範本**|**說明**|
|---| ---|
|[建立 Azure Cosmos 帳戶、 keyspace、 資料表](manage-cassandra-with-resource-manager.md#create-resource) | 此範本會建立兩個區域中透過啟用多個主要的 Cassandra API 帳戶。 Azure Cosmos 帳戶會有兩個資料表共用 keyspace 層級的輸送量。 |
|[更新的 keyspace 的輸送量 （RU/秒）](manage-cassandra-with-resource-manager.md#keyspace-ru-update) | 此範本更新 Cassandra API 帳戶中的 keyspace 的輸送量。 |
|[更新資料表的輸送量 （RU/秒）](manage-cassandra-with-resource-manager.md#table-ru-update) | 此範本會更新資料表中的 Cassandra API 帳戶的輸送量。 |

## <a name="gremlin-api"></a>Gremlin API

|**範本**|**描述**|
|---| ---|
|[建立 Azure Cosmos 帳戶、 資料庫、 圖形](manage-gremlin-with-resource-manager.md#create-resource) | 此範本會建立兩個區域中透過啟用多個主要的 Gremlin API 帳戶。 Azure Cosmos 帳戶會有兩個共用資料庫層級輸送量的圖表。 |
|[更新資料庫的輸送量 （RU/秒）](manage-gremlin-with-resource-manager.md#database-ru-update) | 此範本會更新資料庫中的 Gremlin API 帳戶的輸送量。 |
|[更新圖表的輸送量 （RU/秒）](manage-gremlin-with-resource-manager.md#graph-ru-update) | 此範本更新圖形，Gremlin API 帳戶中的輸送量。 |

## <a name="table-api"></a>資料表 API

|**範本**|**描述**|
|---| ---|
|[建立 Azure Cosmos 帳戶資料表](manage-table-with-resource-manager.md#create-resource) | 此範本會建立兩個區域中的資料表 API 帳戶，透過多個主要啟用。 Azure Cosmos 帳戶會有單一資料表。 |
|[更新資料表的輸送量 （RU/秒）](manage-table-with-resource-manager.md#table-ru-update) | 此範本會更新資料表 API 帳戶中的資料表的輸送量。 |

> [!TIP]
> 若要使用資料表 API 時，請啟用共用的輸送量，可讓在 Azure 入口網站中的帳戶層級輸送量。

請參閱[適用於 Azure Cosmos DB 的 ARM 參考](/azure/templates/microsoft.documentdb/allversions)頁面的參考文件。