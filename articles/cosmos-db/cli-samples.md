---
title: 適用於 Azure Cosmos DB 的 Azure CLI 範例
description: Azure CLI 範例：建立及管理 Azure Cosmos DB 帳戶、資料庫、容器、區域和防火牆。
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 10/26/2018
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: d7ce5e2c12feeee770de8acfd5df81d340e0a7d0
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/19/2019
ms.locfileid: "69615578"
---
# <a name="azure-cli-samples-for-azure-cosmos-db"></a>適用於 Azure Cosmos DB 的 Azure CLI 範例

下表包含適用於 Azure Cosmos DB 之範例 Azure CLI 指令碼的連結。 您可以在 [Azure CLI 參考](/cli/azure/cosmosdb)中取得所有 Azure Cosmos DB CLI 命令的參考頁面。

| |  |
|---|---|
|**建立 Azure Cosmos DB 帳戶、資料庫和容器**||
| [使用 SQL API 建立 Azure Cosmos DB 帳戶](scripts/create-database-account-collections-cli.md?toc=%2fcli%2fazure%2ftoc.json)| 建立單一 Azure Cosmos DB 帳戶、資料庫和容器。 |
| [使用適用於 MongoDB 的 Cosmos DB API 建立 Azure Cosmos DB 帳戶](scripts/create-mongodb-database-account-cli.md?toc=%2fcli%2fazure%2ftoc.json) | 建立單一 Azure Cosmos DB 帳戶、資料庫和集合。 |
| [使用 Gremlin API 建立 Azure Cosmos DB 帳戶](scripts/create-gremlin-database-account-cli.md?toc=%2fcli%2fazure%2ftoc.json) | 建立單一 Azure Cosmos DB 帳戶、資料庫和圖表。 |
| [使用 Cassandra API 建立 Azure Cosmos DB 帳戶](scripts/create-cassandra-database-account-cli.md?toc=%2fcli%2fazure%2ftoc.json) | 建立單一 Azure Cosmos DB 帳戶和資料庫。 |
| [使用資料表 API 建立 Azure Cosmos DB 帳戶](scripts/create-table-database-account-cli.md?toc=%2fcli%2fazure%2ftoc.json) | 建立單一 Azure Cosmos DB 帳戶、資料庫和資料表。 |
|**調整 Azure Cosmos DB**||
| [調整容器輸送量](scripts/scale-collection-throughput-cli.md?toc=%2fcli%2fazure%2ftoc.json) | 變更容器的已佈建輸送量。|
| [複寫多個區域中的 Azure Cosmos 資料庫帳戶和設定容錯移轉優先順序](scripts/scale-multiregion-cli.md?toc=%2fcli%2fazure%2ftoc.json)|使用指定的容錯移轉優先順序，將帳戶資料複寫到全球多個區域中。|
|**保護 Azure Cosmos DB**||
| [取得帳戶金鑰](scripts/secure-get-account-key-cli.md?toc=%2fcli%2fazure%2ftoc.json) | 取得帳戶主要和次要的主要 (master) 寫入金鑰，以及主要和次要唯讀金鑰。|
| [針對使用 Azure Cosmos DB MongoDB API 所設定的 Cosmos 帳戶取得其連接字串](scripts/secure-mongo-connection-string-cli.md?toc=%2fcli%2fazure%2ftoc.json) | 取得連接字串以將 MongoDB 應用程式連線到 Azure Cosmos DB 帳戶。|
| [重新產生帳戶金鑰](scripts/secure-regenerate-key-cli.md?toc=%2fcli%2fazure%2ftoc.json)|重新產生帳戶的金鑰。|
| [建立防火牆](scripts/create-firewall-cli.md?toc=%2fcli%2fazure%2ftoc.json)| 建立輸入 IP 存取控制原則，以限制從核准的電腦集合和/或雲端服務存取帳戶。|
|**高可用性、災害復原、備份和還原**||
| [設定容錯移轉原則](scripts/ha-failover-policy-cli.md?toc=%2fcli%2fazure%2ftoc.json)|針對要在其中複寫帳戶的每個區域，設定容錯移轉優先順序。|
|**將 Azure Cosmos DB 連線到資源**||
| [將 Web 應用程式連線到 Azure Cosmos DB](../app-service/scripts/cli-connect-to-documentdb.md?toc=%2fcli%2fazure%2ftoc.json)|建立 Azure Cosmos 資料庫和 Azure Web 應用程式並將兩者連線。|
|||
