---
title: 如何實作自訂同步處理，透過最佳化處理而在 Azure Cosmos DB 中獲得較高的可用性和效能
description: 了解如何實作自訂同步處理，透過最佳化處理而在 Azure Cosmos DB 中獲得較高的可用性和效能
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 2/12/2019
ms.author: mjbrown
ms.openlocfilehash: 43cb73784806358bccb9758be2923d3df5e9badd
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/19/2019
ms.locfileid: "56414876"
---
# <a name="how-to-implement-custom-synchronization-to-optimize-for-higher-availability-and-performance"></a>如何實作自訂同步處理，透過最佳化處理而獲得較高的可用性和效能

Azure Cosmos DB 提供五個定義完善的一致性層級供您選擇，以讓您在一致性、效能和可用性之間做出平衡的取捨。 強式一致性可確保資料在 Azure Cosmos 帳戶可供使用的每個區域中能夠同步複寫並保有持久性。 此設定雖可提供最高層級的持久性，但會犧牲效能和可用性。 如果應用程式想要控制/放寬資料持久性，以符合應用程式需求又不損及可用性，其可以在應用程式層採用自訂的同步處理，以實現所需的持久性層級。

下圖以視覺方式說明自訂同步處理模型。

![自訂同步處理](./media/how-to-custom-synchronization/custom-synchronization.png)

在此案例中，Azure Cosmos 容器會複寫到位於全球各地的數個區域，範圍橫跨多個大陸。 在此案例中的所有區域使用強式一致性將會對效能造成影響。 為了確保有較高的資料持久性層級，又不會損及寫入延遲時間，應用程式可以使用兩個共用相同工作階段權杖的用戶端。

第一個用戶端可以將資料寫入至本機區域 (例如，美國西部)。 第二個用戶端 (例如，在美國東部) 則是用來確保同步處理的讀取用戶端。 藉由讓工作階段權杖從寫入回應傳到下列讀取中，讀取將會確保寫入會同步處理至美國東部。 Azure Cosmos DB 可確保至少會有一個區域能看到寫入，並保證如果原始的寫入區域中斷，該寫入將不受區域中斷影響而存留下來。 在此案例中，每個寫入都會同步處理至美國東部，從而降低在所有區域採用強式一致性所造成的延遲。 在多主機案例中，每個區域都會發生寫入，您可將此模型擴充為會以平行方式同步處理至多個區域。

## <a name="configure-the-clients"></a>設定用戶端

下列範例示範為了實現此目的，而會具現化兩個用戶端的資料存取層。

```csharp
class MyDataAccessLayer
{
    private DocumentClient writeClient;
    private DocumentClient readClient;

    public async Task InitializeAsync(Uri accountEndpoint, string key)
    {
        ConnectionPolicy writeConnectionPolicy = new ConnectionPolicy
        {
            ConnectionMode = ConnectionMode.Direct,
            ConnectionProtocol = Protocol.Tcp,
            UseMultipleWriteLocations = true
        };
        writeConnectionPolicy.SetCurrentLocation(LocationNames.WestUS);

        ConnectionPolicy readConnectionPolicy = new ConnectionPolicy
        {
            ConnectionMode = ConnectionMode.Direct,
            ConnectionProtocol = Protocol.Tcp
        };
        readConnectionPolicy.SetCurrentLocation(LocationNames.EastUS);

        writeClient = new DocumentClient(accountEndpoint, key, writeConnectionPolicy);
        readClient = new DocumentClient(accountEndpoint, key, readConnectionPolicy, ConsistencyLevel.Session);

        await Task.WhenAll(new Task[]
        {
            writeClient.OpenAsync(),
            readClient.OpenAsync()
        });
    }
}
```

## <a name="implement-custom-synchronization"></a>實作自訂同步處理

用戶端完成初始化後，應用程式便可執行對本機區域 (美國西部) 的寫入，並可強制將寫入同步處理至美國東部，如下所示。

```csharp
class MyDataAccessLayer
{
    public async Task CreateItem(string containerLink, Document document)
    {
        ResourceResponse<Document> response = await writeClient.CreateDocumentAsync(
            containerLink, document);

        await readClient.ReadDocumentAsync(response.Resource.SelfLink,
            new RequestOptions { SessionToken = response.SessionToken });
    }
}
```

此模型可擴充為會以平行方式同步處理至多個區域。

## <a name="next-steps"></a>後續步驟

若要深入了解 Azure Cosmos DB 中的全域散發和一致性，請閱讀下列文章：

* [在 Azure Cosmos DB 中選擇正確的一致性層級](consistency-levels-choosing.md)

* [Azure Cosmos DB 中一致性、可用性和效能的取捨](consistency-levels-tradeoffs.md)

* [如何在 Azure Cosmos DB 中管理一致性](how-to-manage-consistency.md)

* [Azure Cosmos DB 中的資料分割和資料散發](partition-data.md)
