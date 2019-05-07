---
title: 將非資料分割的 Azure Cosmos DB 容器移轉至資料分割的容器
description: 了解如何將所有現有非資料分割容器移轉到資料分割的容器。
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: mjbrown
ms.openlocfilehash: e8aaee8cb7df794652b2c560c5250921c1a4e060
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2019
ms.locfileid: "65160631"
---
# <a name="migrate-non-partitioned-containers-to-partitioned-containers"></a>將非資料分割的容器移轉至資料分割的容器

Azure Cosmos DB 支援建立的容器，而不需要資料分割索引鍵。 目前，您可以使用 Azure CLI 和 Azure Cosmos DB Sdk (.Net、 Java、 NodeJs) 具有的版本小於或等於 2.x 建立非資料分割的容器。 您無法建立非資料分割的容器，使用 Azure 入口網站。 不過，這類非資料分割的容器不彈性，而且有固定的儲存體容量為 10 GB 和輸送量限制，為 10k RU/秒。

非資料分割容器是舊版，您應該將您現有的非資料分割容器移轉至資料分割的容器，來調整儲存體和輸送量。 Azure Cosmos DB 會提供系統定義的機制，將您的非資料分割容器移轉至資料分割的容器。 本文件說明如何所有現有非資料分割的容器會自動移轉到資料分割的容器。 您可以利用自動移轉功能只有當您使用 Sdk 的 V3 版本的所有語言。

> [!NOTE] 
> 目前，您無法移轉 Azure Cosmos DB MongoDB 和 Gremlin API 帳戶，使用這份文件中所述的步驟。 

## <a name="migrate-container-using-the-system-defined-partition-key"></a>移轉使用系統定義的資料分割索引鍵的容器

若要支援的移轉，Azure Cosmos DB 會定義名為系統定義資料分割索引鍵`/_partitionkey`沒有資料分割索引鍵的所有容器。 移轉容器之後，您無法變更資料分割索引鍵定義。 比方說，移轉到資料分割容器的容器的定義會如下： 

```json
{
    "Id": "CollId" 
  "partitionKey": {
    "paths": [
      "/_partitionKey"
    ],
    "kind": "Hash"
  },
}
```
 
移轉容器之後，您可以建立文件填入`_partitionKey`及其文件的其他屬性的屬性。 `_partitionKey`屬性代表您的文件的資料分割索引鍵。 

選擇正確的資料分割索引鍵是以最佳方式利用佈建的輸送量很重要。 如需詳細資訊，請參閱 <<c0> [ 如何選擇資料分割索引鍵](partitioning-overview.md)文章。 

> [!NOTE]
> 您可以利用系統定義的資料分割索引鍵只有當您使用 Sdk 的 V3 最新版本/版本的所有語言。

下列範例示範建立系統定義的資料分割索引鍵的文件，並閱讀該文件的範例程式碼：

**文件的 JSON 表示法**

```csharp
DeviceInformationItem = new DeviceInformationItem
{
   "id": "elevator/PugetSound/Building44/Floor1/1",
   "deviceId": "3cf4c52d-cc67-4bb8-b02f-f6185007a808",
   "_partitionKey": "3cf4c52d-cc67-4bb8-b02f-f6185007a808"
} 

public class DeviceInformationItem
{
    [JsonProperty(PropertyName = "id")]
    public string Id { get; set; }

    [JsonProperty(PropertyName = "deviceId")]
    public string DeviceId { get; set; }

    [JsonProperty(PropertyName = "_partitionKey")]
    public string PartitionKey {get {return this.DeviceId; set; }
}

CosmosContainer migratedContainer = database.Containers["testContainer"];

DeviceInformationItem deviceItem = new DeviceInformationItem() {
  Id = "1234", 
  DeviceId = "3cf4c52d-cc67-4bb8-b02f-f6185007a808"
} 

CosmosItemResponse<DeviceInformationItem > response = 
  await migratedContainer.Items.CreateItemAsync(
    deviceItem.PartitionKey, 
    deviceItem
  );

// Read back the document providing the same partition key
CosmosItemResponse<DeviceInformationItem> readResponse = 
  await migratedContainer.Items.ReadItemAsync<DeviceInformationItem>( 
    partitionKey:deviceItem.PartitionKey, 
    id: device.Id
  ); 

```

如需完整的範例，請參閱[.Net 範例](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/CodeSamples)GitHub 存放庫。 
                      
## <a name="migrate-the-documents"></a>移轉文件

在容器內的文件容器定義已增強式資料分割索引鍵屬性，而不自動移轉。 這表示系統資料分割索引鍵屬性`/_partitionKey`路徑不會自動加入至現有的文件。 您需要重新分割現有的文件，藉由讀取資料分割索引鍵沒有建立的文件，並重新撰寫它們再度`_partitionKey`文件中的屬性。 

## <a name="access-documents-that-dont-have-a-partition-key"></a>沒有資料分割索引鍵的存取文件

應用程式可以存取使用稱為 「 CosmosContainerSettings.NonePartitionKeyValue 」 的特殊系統屬性沒有資料分割索引鍵的現有文件，這是未移轉文件的值。 所有 CRUD 和查詢作業中，您可以使用這個屬性。 下列範例顯示從 NonePartitionKey 讀取單一文件範例。 

```csharp
CosmosItemResponse<DeviceInformationItem> readResponse = 
await migratedContainer.Items.ReadItemAsync<DeviceInformationItem>( 
  partitionKey: CosmosContainerSettings.NonePartitionKeyValue, 
  id: device.Id
); 

```

如需如何重新分割的文件的完整範例，請參閱[.Net 範例](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/CodeSamples)GitHub 存放庫。 

## <a name="compatibility-with-sdks"></a>與 Sdk 相容性

舊版本的 Azure Cosmos DB Sdk V2.x.x 等 V1.x.x 不支援的系統定義的資料分割索引鍵屬性。 因此，當您從舊版 SDK 讀取容器定義，它不包含任何資料分割索引鍵定義，而這些容器會完全如之前一樣運作。 使用舊版的 Sdk 所建置的應用程式繼續使用非資料分割因為不進行任何變更。 

如果已移轉的容器/V3 的最新版本的 SDK 和開始填入新的文件內定義的系統資料分割索引鍵使用時，您無法存取 （讀取、 更新、 刪除、 查詢） 這類文件從較舊的 Sdk 不再。

## <a name="next-steps"></a>後續步驟

* [Azure Cosmos DB 中的資料分割](partitioning-overview.md)
* [Azure Cosmos DB 中的要求單位](request-units.md)
* [在容器和資料庫中佈建輸送量](set-throughput.md)
* [使用 Azure Cosmos 帳戶](account-overview.md)