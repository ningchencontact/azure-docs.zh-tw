---
title: 使用 Azure CLI 管理 Azure Cosmos DB 資源 | Microsoft Docs
description: 使用 Azure CLI 來管理您的 Azure Cosmos DB 帳戶、資料庫和容器。
services: cosmos-db
author: markjbrown
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 10/23/2018
ms.author: mjbrown
ms.openlocfilehash: 3446f4f71349d0b7290a2514edf46efb37203324
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2018
ms.locfileid: "51019765"
---
# <a name="manage-azure-cosmos-db-resources-using-azure-cli"></a>使用 Azure CLI 管理 Azure Cosmos DB 資源

下列指南說明使用 Azure CLI 自動管理 Azure Cosmos DB 帳戶、資料庫及容器的命令。 它也包含用來調整容器輸送量的命令。 您可以在 [Azure CLI 參考](https://docs.microsoft.com/cli/azure/cosmosdb)中取得所有 Azure Cosmos DB CLI 命令的參考頁面。 您也可以在[適用於 Azure Cosmos DB 的 Azure CLI 範例](cli-samples.md)中找到更多範例，包括如何針對 MongoDB、Gremlin、Cassandra 及資料表 API建立和管理 Cosmos DB 帳戶、資料庫和容器。

這個範例 CLI 指令碼會建立 Azure Cosmos DB SQL API 帳戶、資料庫和容器。  

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果您選擇在本機安裝和使用 CLI，本主題會要求您執行 Azure CLI 2.0 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。

## <a name="create-an-azure-cosmos-db-account"></a>建立 Azure Cosmos DB 帳戶

若要在已於美國東部和美國西部啟用 SQL API、工作階段一致性、多重主機的情況下建立 Azure Cosmos DB 帳戶，請開啟 Azure CLI 或 Cloud Shell，然後執行下列命令：

```azurecli-interactive
az cosmosdb create \
   –-name "myCosmosDbAccount" \
   --resource-group "myResourceGroup" \
   --kind GlobalDocumentDB \
   --default-consistency-level "Session" \
   --locations "EastUS=0" "WestUS=1" \
   --enable-multiple-write-locations true \
```

## <a name="create-a-database"></a>建立資料庫

若要建立 Cosmos DB 資料庫，請開啟 Azure CLI 或 Cloud Shell，然後執行下列命令：

```azurecli-interactive
az cosmosdb database create \
   --name "myCosmosDbAccount" \
   --db-name "myDatabase" \
   --resource-group "myResourceGroup"
```

## <a name="create-a-container"></a>建立容器

若要使用 1000 個 RU/秒和分割區索引鍵建立 Cosmos DB 容器，請開啟 Azure CLI 或 Cloud Shell，然後執行下列命令：

```azurecli-interactive
# Create a container
az cosmosdb collection create \
   --collection-name "myContainer" \
   --name "myCosmosDbAccount" \
   --db-name "myDatabase" \
   --resource-group "myResourceGroup" \
   --partition-key-path = "/myPartitionKey" \
   --throughput 1000
```

## <a name="change-the-throughput-of-a-container"></a>變更容器的輸送量

若要將 Cosmos DB 容器的輸送量變更為 400 個 RU/秒，請開啟 Azure CLI 或 Cloud Shell，然後執行下列命令：

```azurecli-interactive
# Update container throughput
az cosmosdb collection update \
   --collection-name "myContainer" \
   --name "myCosmosDbAccount" \
   --db-name "myDatabase" \
   --resource-group "myResourceGroup" \
   --throughput 400
```

## <a name="list-account-keys"></a>列出帳戶金鑰

當您建立 Azure Cosmos DB 帳戶時，服務會產生兩個主要存取金鑰，用於存取 Azure Cosmos DB 帳戶時的驗證。 透過提供這兩個存取金鑰，Azure Cosmos DB 讓您可以重新產生金鑰，同時又不需中斷 Azure Cosmos DB 帳戶。 也提供驗證唯讀作業的唯讀金鑰。 有兩個讀寫金鑰 (主要和次要) 和兩個唯讀金鑰 (主要和次要)。 您可以藉由執行下列命令來取得帳戶金鑰：

```azurecli-interactive
# List account keys
az cosmosdb list-keys \
   --name "myCosmosDbAccount"\
   --resource-group "myResourceGroup"
```

## <a name="list-connection-strings"></a>列出連接字串

您可以使用下列命令，來擷取將應用程式連線到 Cosmos DB 帳戶的連接字串。

```azurecli-interactive
# List connection strings
az cosmosdb list-connection-strings \
   --name "myCosmosDbAccount"\
   --resource-group "myResourceGroup"
```

## <a name="regenerate-account-key"></a>重新產生帳戶金鑰

您應定期變更 Azure Cosmos DB 帳戶的存取金鑰，讓連線更加安全。 指派的兩個存取金鑰可讓您在重新產生一個存取金鑰的同時，使用另一個存取金鑰維持 Azure Cosmos DB 帳戶連線。

```azurecli-interactive
# Regenerate account key
az cosmosdb regenerate-key \
   --name "myCosmosDbAccount"\
   --resource-group "myResourceGroup" \
   --key-kind primary
```

## <a name="next-steps"></a>後續步驟

如需 Azure CLI 的詳細資訊，請參閱：

- [安裝 Azure CLI](/cli/azure/install-azure-cli)
- [Azure CLI 參考](https://docs.microsoft.com/cli/azure/cosmosdb)
- [適用於 Azure Cosmos DB 的其他 Azure CLI 範例](cli-samples.md)