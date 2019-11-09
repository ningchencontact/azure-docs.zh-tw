---
title: Azure Cosmos DB 的 Azure Resource Manager 範本
description: 使用 Azure Resource Manager 範本來建立和設定 Azure Cosmos DB。
author: TheovanKraay
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/07/2019
ms.author: thvankra
ms.openlocfilehash: 7c508d736c7c04cc6df2b42e0a90a2906052adbc
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/08/2019
ms.locfileid: "73835526"
---
# <a name="azure-resource-manager-templates-for-azure-cosmos-db"></a>Azure Cosmos DB 的 Azure Resource Manager 範本

下表包含 Azure Cosmos DB Azure Resource Manager 範本的連結：

## <a name="sql-core-api"></a>SQL (Core) API

|**範本**|**說明**|
|---|---|
|[建立 Azure Cosmos 帳戶、資料庫、容器](manage-sql-with-resource-manager.md#create-resource) | 此範本會在兩個區域中建立 SQL （核心） API 帳戶，其中包含兩個具有共用資料庫輸送量的容器，以及一個具有專用輸送量的容器。 藉由重新提交具有更新的輸送量屬性值的範本，即可更新輸送量。 |
|[使用預存程式、觸發程式和 UDF 建立 Azure Cosmos 帳戶、資料庫和容器](manage-sql-with-resource-manager.md#create-sproc) | 此範本會在兩個區域中建立 SQL （核心） API 帳戶，其中包含容器的預存程式、觸發程式和 UDF。 |

## <a name="mongodb-api"></a>MongoDB API

|**範本**|**說明**|
|---| ---|
|[建立 Azure Cosmos 帳戶、資料庫、集合](manage-mongodb-with-resource-manager.md#create-resource) | 此範本會使用適用于 MongoDB 的 Azure Cosmos DB API，在已啟用多宿主的兩個區域中建立帳戶。 Azure Cosmos 帳戶將會有兩個共用資料庫層級輸送量的容器。 |
|[更新資料庫的輸送量（RU/秒）](manage-mongodb-with-resource-manager.md#database-ru-update) | 此範本會更新 MongoDB API 帳戶中資料庫的輸送量。 |
|[更新集合的輸送量（RU/秒）](manage-mongodb-with-resource-manager.md#collection-ru-update) | 此範本會更新 MongoDB API 帳戶中容器的輸送量。 |

## <a name="cassandra-api"></a>Cassandra API

|**範本**|**說明**|
|---| ---|
|[建立 Azure Cosmos 帳戶、keyspace、資料表](manage-cassandra-with-resource-manager.md#create-resource) | 此範本會在已啟用多宿主的兩個區域中建立 Cassandra API 帳戶。 Azure Cosmos 帳戶將會有兩個共用 keyspace 層級輸送量的資料表。 |

## <a name="gremlin-api"></a>Gremlin API

|**範本**|**說明**|
|---| ---|
|[建立 Azure Cosmos 帳戶、資料庫、圖表](manage-gremlin-with-resource-manager.md#create-resource) | 此範本會在已啟用多宿主的兩個區域中建立 Gremlin API 帳戶。 Azure Cosmos 帳戶將會有兩個共用資料庫層級輸送量的圖形。 |
|[更新資料庫的輸送量（RU/秒）](manage-gremlin-with-resource-manager.md#database-ru-update) | 此範本會更新 Gremlin API 帳戶中資料庫的輸送量。 |
|[更新圖形的輸送量（RU/秒）](manage-gremlin-with-resource-manager.md#graph-ru-update) | 此範本會在 Gremlin API 帳戶中更新圖形的輸送量。 |

## <a name="table-api"></a>資料表 API

|**範本**|**說明**|
|---| ---|
|[建立 Azure Cosmos 帳戶，資料表](manage-table-with-resource-manager.md#create-resource) | 此範本會在已啟用多宿主的兩個區域中建立資料表 API 帳戶。 Azure Cosmos 帳戶將會有單一資料表。 |
|[更新資料表的輸送量（RU/秒）](manage-table-with-resource-manager.md#table-ru-update) | 此範本會更新資料表 API 帳戶中資料表的輸送量。 |

> [!TIP]
> 若要在使用資料表 API 時啟用共用輸送量，請在 Azure 入口網站中啟用帳戶層級輸送量。

如需參考檔，請參閱 Azure Cosmos DB 頁面的[ARM 參考](/azure/templates/microsoft.documentdb/allversions)。
