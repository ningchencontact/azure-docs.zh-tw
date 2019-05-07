---
title: 適用於 Azure Cosmos DB 的 azure Resource Manager 範本
description: 使用 Azure Resource Manager 範本來建立和設定 Azure Cosmos DB。
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: mjbrown
ms.openlocfilehash: c907de019b64a6a9d03206514a1147fd43c78106
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2019
ms.locfileid: "65078456"
---
# <a name="azure-resource-manager-templates-for-azure-cosmos-db"></a>適用於 Azure Cosmos DB 的 azure Resource Manager 範本

下表包含適用於 Azure Cosmos DB 的 Azure Resource Manager 範本的連結：

|**API 類型** | **連結至範例**| **說明** |
|---|---| ---|
|核心 (SQL) API| [建立 Azure Cosmos DB 帳戶 （多重主機）](manage-sql-with-resource-manager.md) | 此範本會建立兩個區域中透過啟用多個主要的 SQL API 帳戶。 Azure Cosmos 帳戶會有兩個容器的共用資料庫層級的輸送量。 |
| MongoDB API | [建立 Azure Cosmos DB 帳戶 （多重主機）](manage-mongodb-with-resource-manager.md) | 此範本會建立兩個區域中適用於 MongoDB 的 Azure Cosmos DB 的 API 使用多重主機已啟用的帳戶。 Azure Cosmos 帳戶會有兩個容器的共用資料庫層級的輸送量。 |
| Cassandra API | [建立 Azure Cosmos DB 帳戶 （多重主機）](manage-cassandra-with-resource-manager.md) | 此範本會建立兩個區域中透過啟用多個主要的 Cassandra API 帳戶。 Azure Cosmos 帳戶會有兩個資料表共用 keyspace 層級的輸送量。 |
| Gremlin API| [建立 Azure Cosmos DB 帳戶 （多重主機）](manage-gremlin-with-resource-manager.md) | 此範本會建立兩個區域中透過啟用多個主要的 Gremlin API 帳戶。 Azure Cosmos 帳戶會有兩個共用資料庫層級輸送量的圖表。 |
| 資料表 API | [建立 Azure Cosmos DB 帳戶 （多重主機）](manage-table-with-resource-manager.md) | 此範本會建立兩個區域中的資料表 API 帳戶，透過多個主要啟用。 Azure Cosmos 帳戶會有單一資料表。 |

> [!TIP]
> 若要使用資料表 API 時，請啟用共用的輸送量，可讓在 Azure 入口網站中的帳戶層級輸送量。

請參閱[適用於 Azure Cosmos DB 的 ARM 參考](/azure/templates/microsoft.documentdb/allversions)頁面的參考文件。