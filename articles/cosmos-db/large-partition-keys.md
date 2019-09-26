---
title: 使用 Azure 入口網站和各種 Sdk，建立具有大型分割區索引鍵的 Azure Cosmos 容器。
description: 瞭解如何使用 Azure 入口網站和不同的 Sdk，在具有大型分割區索引鍵的 Azure Cosmos DB 中建立容器。
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/03/2019
ms.author: mjbrown
ms.openlocfilehash: a1216daade2df832b606fceb648fca998c3fdec8
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/26/2019
ms.locfileid: "71300143"
---
# <a name="create-containers-with-large-partition-key"></a>建立具有大型分割區索引鍵的容器

Azure Cosmos DB 使用以雜湊為基礎的資料分割配置來達到水準的資料調整。 在5月 3 2019 之前建立的所有 Azure Cosmos 容器都會使用雜湊函式，根據資料分割索引鍵的前100個位元組來計算雜湊。 如果有多個分割區索引鍵具有相同的前100個位元組，則服務會將這些邏輯分割區視為相同的邏輯資料分割。 這可能會導致分割區大小配額不正確的問題，以及在資料分割索引鍵上套用的唯一索引。 引進了大型分割區索引鍵來解決此問題。 Azure Cosmos DB 現在支援大小上限為 2 KB 的大型資料分割索引鍵。

使用增強版雜湊函式的功能來支援大型分割區索引鍵，它可以從最多 2 KB 的大型資料分割索引鍵產生唯一的雜湊。 對於具有高資料分割索引鍵基數（不論資料分割索引鍵大小）的案例，也建議使用此雜湊版本。 分割區索引鍵基數會定義為唯一邏輯分割區的數目，例如，在容器中的順序為 ~ 30000 邏輯分割區。 本文說明如何使用 Azure 入口網站和不同的 Sdk 來建立具有大型分割區索引鍵的容器。 

## <a name="create-a-large-partition-key-net-sdk"></a>建立大型分割區索引鍵（.Net SDK）

若要使用 .net SDK 建立具有大型分割區索引鍵的容器，請`PartitionKeyDefinitionVersion.V2`指定屬性。 下列範例顯示如何指定 PartitionKeyDefinition 物件內的 Version 屬性，並將它設定為 PartitionKeyDefinitionVersion。

### <a name="v3-net-sdk"></a>v3 .NET SDK

```csharp
await database.CreateContainerAsync(
    new ContainerProperties(collectionName, $"/longpartitionkey")
    {
        PartitionKeyDefinitionVersion = PartitionKeyDefinitionVersion.V2, 
    })
```

### <a name="v2-net-sdk"></a>v2 .NET SDK

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

## <a name="create-a-large-partition-key-azure-portal"></a>建立大型分割區索引鍵（Azure 入口網站） 

若要建立大型分割區索引鍵，當您使用 Azure 入口網站建立新的容器時，請核取 [我的分割區索引**鍵大於 100-位元組**] 選項。 根據預設，所有的新容器都會使用大型分割區索引鍵來加入宣告。 如果您不需要大型分割區索引鍵，或如果您的應用程式在1.18 之前的 Sdk 版本上執行，請取消選取此核取方塊。

![使用 Azure 入口網站建立大型分割區索引鍵](./media/large-partition-keys/large-partition-key-with-portal.png)

## <a name="create-a-large-partition-key-powershell"></a>建立大型分割區索引鍵（PowerShell）

若要使用 PowerShell 建立具有大型分割區索引鍵的容器`"version" = 2` ，請`partitionKey`為物件包含。

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

下列 Sdk 的最低版本支援大型分割區索引鍵：

|SDK 類型  | 最低版本   |
|---------|---------|
|.Net     |    1.18     |
|JAVA 同步處理     |   2.4.0      |
|JAVA 非同步   |  2.5.0        |
| REST API | 版本高於， `x-ms-version`使用要求標頭。 `2017-05-03`|

目前，您無法在 Power BI 和 Azure Logic Apps 中使用具有大型分割區索引鍵的容器。 您可以從這些應用程式使用沒有大型分割區索引鍵的容器。

## <a name="next-steps"></a>後續步驟

* [Azure Cosmos DB 中的資料分割](partitioning-overview.md)
* [Azure Cosmos DB 中的要求單位](request-units.md)
* [在容器和資料庫中佈建輸送量](set-throughput.md)
* [使用 Azure Cosmos 帳戶](account-overview.md)


