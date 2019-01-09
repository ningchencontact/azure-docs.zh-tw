---
title: 適用於 Azure Cosmos DB 的 Azure PowerShell 範例
description: Azure PowerShell 範例：協助您建立和管理 Azure Cosmos DB 帳戶的指令碼。
services: cosmos-db
author: SnehaGunda
tags: azure-service-management
ms.service: cosmos-db
ms.custom: mvc
ms.topic: sample
ms.date: 10/16/2017
ms.author: sngun
ms.openlocfilehash: 480bccd73a049d2f181d5acf906882b2f9b6ccdb
ms.sourcegitcommit: 9f87a992c77bf8e3927486f8d7d1ca46aa13e849
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/28/2018
ms.locfileid: "53808395"
---
# <a name="azure-powershell-samples-for-azure-cosmos-db"></a>適用於 Azure Cosmos DB 的 Azure PowerShell 範例

下表包含適用於 Azure Cosmos DB 之範例 Azure PowerShell 指令碼的連結。 目前您只能透過 PowerShell 來管理 Azure Cosmos DB 帳戶；無法透過 PowerShell 來管理諸如資料庫和容器等其他資源。

| |  |
|---|---|
|**建立 Azure Cosmos DB 帳戶**||
|[使用 SQL API 建立及設定 Cosmos 帳戶](scripts/create-database-account-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 建立單一 Azure Cosmos DB 帳戶，以便搭配 SQL API 使用。 |
|[使用 Azure Cosmos DB 的 MongoDB API 建立和設定 Cosmos 帳戶](scripts/create-mongodb-database-account-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 使用 Azure Cosmos DB 的 MongoDB API 建立單一 Cosmos 帳戶。 |
|[使用 Gremlin API 建立及設定 Cosmos 帳戶](scripts/create-graph-database-account-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 建立單一 Azure Cosmos DB 帳戶，以便搭配 Gremlin API 使用。 |
|[使用 Cassandra API 建立及設定 Cosmos 帳戶](scripts/create-and-configure-cassandra-database.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 建立與 Cassandra API 搭配使用的單一 Azure Cosmos DB 帳戶。 |
|[使用資料表 API 建立及設定 Cosmos 帳戶](scripts/create-table-database-account-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 建立與資料表 API 搭配使用的單一 Azure Cosmos DB 帳戶。 |
|**調整 Azure Cosmos DB**||
|[複寫多個區域中的 Azure Cosmos DB 帳戶和設定容錯移轉優先順序](scripts/scale-multiregion-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)|使用指定的容錯移轉優先順序，將帳戶資料複寫到全球多個區域中。|
|**保護 Azure Cosmos DB**||
| [取得帳戶金鑰](scripts/secure-get-account-key-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 取得帳戶主要和次要的主要 (master) 寫入金鑰，以及主要和次要唯讀金鑰。|
| [取得 MongoDB 連接字串](scripts/secure-mongo-connection-string-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 取得連接字串以將您的 MongoDB 應用程式連線到 Azure Cosmos DB 帳戶。|
|[重新產生帳戶金鑰](scripts/secure-regenerate-key-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)|重新產生帳戶的主要或唯讀金鑰。|
|[建立防火牆](scripts/create-firewall-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 建立輸入 IP 存取控制原則，以限制從核准的電腦集合和/或雲端服務存取帳戶。|
|**高可用性、災害復原、備份和還原**||
|[設定容錯移轉原則](scripts/ha-failover-policy-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)|針對要在其中複寫帳戶的每個區域，設定容錯移轉優先順序。|
|||
