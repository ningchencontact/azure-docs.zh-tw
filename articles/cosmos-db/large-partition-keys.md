---
title: 建立 Azure Cosmos 容器，使用 Azure 入口網站和各種 Sdk 的大型資料分割索引鍵。
description: 了解如何建立 Azure Cosmos DB 中的容器，使用 Azure 入口網站和不同的 Sdk 的大型資料分割索引鍵。
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/03/2019
ms.author: mjbrown
ms.openlocfilehash: bd1697378e5db0432d181f9f688ccc2468b306e7
ms.sourcegitcommit: d2785f020e134c3680ca1c8500aa2c0211aa1e24
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/04/2019
ms.locfileid: "67566013"
---
# <a name="create-containers-with-large-partition-key"></a>建立具有大型的資料分割索引鍵的容器

Azure Cosmos DB 會使用雜湊為基礎的資料分割配置，來達成的資料水平縮放。 3 個 2019 年之前建立的所有 Azure Cosmos 容器都使用計算前 100 個位元組，資料分割索引鍵為基礎的雜湊值的雜湊函式。 如果有多個具有相同的前 100 個位元組的資料分割索引鍵，然後這些邏輯分割區會被視為相同的邏輯資料分割服務。 這可能會導致問題，例如磁碟分割大小配額所不正確，以及要套用到資料分割索引鍵的唯一索引。 若要解決此問題導入大型資料分割索引鍵。 Azure Cosmos DB 現在支援大型的資料分割索引鍵與值上限為 2 KB。

支援的金鑰使用的功能可以從大型的資料分割產生唯一的雜湊的雜湊函式的增強型版本的大型資料分割索引鍵最多為 2 KB。 此雜湊版本也建議適合具有高的資料分割索引鍵的基數，無論資料分割索引鍵的大小。 資料分割索引鍵的基數被定義為唯一的邏輯磁碟分割，例如順序 ~ 30000 在容器中的邏輯分割區數目。 本文說明如何使用大型的資料分割索引鍵使用不同的 Sdk 與 Azure 入口網站建立容器。 

## <a name="create-a-large-partition-key-net-sdk-v2"></a>建立大型的資料分割索引鍵 (.Net SDK V2)

若要使用大型的資料分割索引鍵使用.NET SDK 來建立容器，指定`PartitionKeyDefinitionVersion.V2`屬性。 下列範例示範如何指定 PartitionKeyDefinition 物件內的 [版本] 屬性，然後將它設定為 PartitionKeyDefinitionVersion.V2。

```csharp
DocumentCollection collection = await newClient.CreateDocumentCollectionAsync(
database,
     new DocumentCollection
        {
           Id = Guid.NewGuid().ToString(),
           PartitionKey = new PartitionKeyDefinition
           {
             Paths = new Collection<string> {"/longpartitionkey" },
             Version = PartitionKeyDefinitionVersion.V2
           }
         },
      new RequestOptions { OfferThroughput = 400 });
```

## <a name="create-a-large-partition-key-azure-portal"></a>建立大型的資料分割索引鍵 （Azure 入口網站） 

若要建立新的容器，使用 Azure 入口網站時，請建立大型的資料分割索引鍵，請檢查**我的資料分割索引鍵長度超過 100 個位元組**選項。 根據預設，所有新的容器加入使用大型的資料分割索引鍵。 如果您不需要大型的資料分割索引鍵，或如果您有超過 1.18 的 Sdk 版本上執行的應用程式，請取消選取此核取方塊。

![建立使用 Azure 入口網站的大型資料分割索引鍵](./media/large-partition-keys/large-partition-key-with-portal.png)

## <a name="create-a-large-partition-key-powershell"></a>建立大型的資料分割索引鍵 (PowerShell)

若要使用大型的資料分割索引鍵使用 PowerShell 來建立容器，包括`"version" = 2`針對`partitionKey`物件。

```azurepowershell-interactive
# Create a Cosmos SQL API container with large partition key support (version 2)
$resourceGroupName = "myResourceGroup"
$containerName = "mycosmosaccount" + "/sql/" + "myDatabase" + "/" + "myContainer"

# Container with large partition key support (version = 2)
$containerProperties = @{
  "resource"=@{
    "id"=$containerName;
    "partitionKey"=@{
        "paths"=@("/myPartitionKey");
        "kind"="Hash";
        "version" = 2
    };
    "indexingPolicy"=@{
        "indexingMode"="Consistent";
        "includedPaths"= @(@{
            "path"="/*"
        });
        "excludedPaths"= @(@{
            "path"="/myPathToNotIndex/*"
        })
    }
  }
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $containerName -PropertyObject $containerProperties
```

## <a name="supported-sdk-versions"></a>支援的 SDK 版本

大型的資料分割索引鍵支援的 Sdk 最低版本如下：

|SDK 類型  | 最小版本   |
|---------|---------|
|.Net     |    1.18     |
|Java 同步處理     |   2.4.0      |
|Java Async   |  2.5.0        |
| REST API | 版本高於`2017-05-03`使用`x-ms-version`要求標頭。|

目前，您無法使用容器在 Power BI 和 Azure Logic Apps 中的大型資料分割索引鍵。 您可以使用沒有大型的資料分割索引鍵，從這些應用程式的容器。

## <a name="next-steps"></a>後續步驟

* [Azure Cosmos DB 中的資料分割](partitioning-overview.md)
* [Azure Cosmos DB 中的要求單位](request-units.md)
* [在容器和資料庫中佈建輸送量](set-throughput.md)
* [使用 Azure Cosmos 帳戶](account-overview.md)


