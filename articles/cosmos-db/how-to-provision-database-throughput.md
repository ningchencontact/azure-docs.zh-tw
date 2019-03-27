---
title: 在 Azure Cosmos DB 中佈建資料庫輸送量
description: 了解如何在 Azure Cosmos DB 中佈建資料庫層級的輸送量
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 11/06/2018
ms.author: mjbrown
ms.openlocfilehash: c0a947c9cf2c1d4a11d310b9bab0774a70534a1e
ms.sourcegitcommit: aa3be9ed0b92a0ac5a29c83095a7b20dd0693463
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/20/2019
ms.locfileid: "58259426"
---
# <a name="provision-throughput-for-a-database-in-azure-cosmos-db"></a>在 Azure Cosmos DB 中佈建資料庫的輸送量

本文說明如何在 Azure Cosmos DB 中佈建資料庫的輸送量。 您可以佈建單一[容器](how-to-provision-container-throughput.md)的輸送量，或是為資料庫佈建，並在其容器之間共用輸送量。 若要了解何時應使用容器層級和資料庫層級輸送量，請參閱[在容器和資料庫上佈建輸送量的使用案例](set-throughput.md)一文。 您可以使用 Azure 入口網站或 Azure Cosmos DB SDK 來佈建資料庫層級的輸送量。

## <a name="provision-throughput-by-using-azure-portal"></a>使用 Azure 入口網站佈建輸送量

### <a id="portal-sql"></a>SQL (Core) API

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

1. [建立新的 Azure Cosmos DB 帳戶](create-sql-api-dotnet.md#create-account)，或選取現有的帳戶。

1. 開啟 [資料總管] 窗格，然後選取 [新增資料庫]。 提供下列詳細資料：

   * 輸入資料庫識別碼。 
   * 選取 [佈建輸送量]。
   * 輸入輸送量 (例如 1000 RU)。
   * 選取 [確定] 。

![[新增資料庫] 對話方塊的螢幕擷取畫面](./media/how-to-provision-database-throughput/provision-database-throughput-portal-all-api.png)

## <a name="provision-throughput-by-using-net-sdk"></a>使用 .NET SDK 佈建輸送量

> [!Note]
> 使用 SQL API 為所有 API 佈建輸送量。 您也可以選擇性地對 Cassandra API 使用下列範例。

### <a id="dotnet-all"></a>所有 API

```csharp
//set the throughput for the database
RequestOptions options = new RequestOptions
{
    OfferThroughput = 10000
};

//create the database
await client.CreateDatabaseIfNotExistsAsync(
    new Database {Id = databaseName},  
    options);
```

### <a id="dotnet-cassandra"></a>Cassandra API

```csharp
// Create a Cassandra keyspace and provision throughput of 10000 RU/s
session.Execute(CREATE KEYSPACE IF NOT EXISTS myKeySpace WITH cosmosdb_provisioned_throughput=10000);
```

## <a name="next-steps"></a>後續步驟

請參閱下列文章，以了解如何在 Azure Cosmos DB 中佈建輸送量：

* [如何佈建容器的輸送量](how-to-provision-container-throughput.md)
* [Azure Cosmos DB 中的要求單位和輸送量](request-units.md)
