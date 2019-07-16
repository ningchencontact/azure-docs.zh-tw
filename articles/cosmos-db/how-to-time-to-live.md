---
title: 了解如何在 Azure Cosmos DB 中設定和管理存留時間
description: 了解如何在 Azure Cosmos DB 中設定和管理存留時間
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 05/23/2019
ms.author: mjbrown
ms.openlocfilehash: 618e7e19b20f361aa0a8c668e9621a29db43772d
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2019
ms.locfileid: "67797744"
---
# <a name="configure-time-to-live-in-azure-cosmos-db"></a>在 Azure Cosmos DB 中設定存留時間

在 Azure Cosmos DB 中，您可以選擇在容器層級設定存留時間 (TTL)，也可以在為容器設定後於項目層級加以覆寫。 您可以使用 Azure 入口網站或語言專屬 SDK，來為容器設定 TTL。 項目層級的 TTL 覆寫可使用 SDK 來加以設定。

## <a name="enable-time-to-live-on-a-container-using-azure-portal"></a>使用 Azure 入口網站在容器上啟用存留時間

您可以使用下列步驟，在容器上啟用存留時間 (無到期時間)。 啟用此選項可讓您能夠在項目層級覆寫 TTL。 您也可以輸入非零值的秒數，以設定 TTL。

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

2. 建立新的 Azure Cosmos 帳戶，或選取現有帳戶。

3. 開啟 [資料總管]  窗格。

4. 選取現有容器，加以展開並修改下列值：

   * 開啟 [規模與設定]  視窗。
   * 在 [設定]  底下，尋找 [存留時間]  。
   * 選取 [開啟 (無預設值)]  ，或選取 [開啟]  並設定 TTL 值
   * 按一下 [儲存]  儲存變更。

   ![在 Azure 入口網站中設定存留時間](./media/how-to-time-to-live/how-to-time-to-live-portal.png)


- 當 DefaultTimeToLive 為 null 時，存留時間會關閉
- 當 DefaultTimeToLive 為 -1 時，存留時間會開啟 (無預設值)
- 當 DefaultTimeToLive 具有任何其他整數值 (0 除外)，存留時間設定會開啟

## <a name="enable-time-to-live-on-a-container-using-sdk"></a>使用 SDK 在容器上啟用存留時間

### <a id="dotnet-enable-noexpiry"></a>.NET SDK

```csharp
// Create a new collection with TTL enabled and without any expiration value
DocumentCollection collectionDefinition = new DocumentCollection();
collectionDefinition.Id = "myContainer";
collectionDefinition.PartitionKey.Paths.Add("/myPartitionKey");
collectionDefinition.DefaultTimeToLive = -1; //(never expire by default)

DocumentCollection ttlEnabledCollection = await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("myDatabaseName"),
    collectionDefinition,
    new RequestOptions { OfferThroughput = 20000 });
```

## <a name="set-time-to-live-on-a-container-using-sdk"></a>使用 SDK 在容器上設定存留時間

### <a id="dotnet-enable-withexpiry"></a>.NET SDK

若要在容器上設定存留時間，您必須提供零以外的正數，來指出以秒為單位的時間週期。 根據所設定的 TTL 值，容器中時間在 `_ts` 項目上次修改時間戳記之後的所有項目都會遭到刪除。

```csharp
// Create a new collection with TTL enabled and a 90 day expiration
DocumentCollection collectionDefinition = new DocumentCollection();
collectionDefinition.Id = "myContainer";
collectionDefinition.PartitionKey.Paths.Add("/myPartitionKey");
collectionDefinition.DefaultTimeToLive = 90 * 60 * 60 * 24; // expire all documents after 90 days

DocumentCollection ttlEnabledCollection = await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("myDatabaseName"),
    collectionDefinition,
    new RequestOptions { OfferThroughput = 20000 });
```

### <a id="nodejs-enable-withexpiry"></a>NodeJS SDK

```javascript
const containerDefinition = {
          id: "sample container1",
        };

async function createcontainerWithTTL(db: Database, containerDefinition: ContainerDefinition, collId: any, defaultTtl: number) {
      containerDefinition.id = collId;
      containerDefinition.defaultTtl = defaultTtl;
      await db.containers.create(containerDefinition);
}
```

## <a name="set-time-to-live-on-an-item"></a>在項目上設定存留時間

除了在容器上設定預設存留時間外，您還可以為項目設定存留時間。 在項目層級設定存留時間，將會覆寫該容器中項目的預設 TTL。

* 若要在項目上設定 TTL，您必須提供一個非零的正數，指出在 `_ts` 項目上次修改時間戳記之後要將項目到期的期間 (以秒為單位)。

* 如果該項目沒有 TTL 欄位，則根據預設，為容器設定的 TTL 便會套用至該項目。

* 如果已在容器層級停用 TTL，則會忽略項目上的 TTL 欄位，直到在容器上重新啟用 TTL 為止。

### <a id="portal-set-ttl-item"></a>Azure 入口網站

使用下列步驟，在項目上啟用存留時間：

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

2. 建立新的 Azure Cosmos 帳戶，或選取現有帳戶。

3. 開啟 [資料總管]  窗格。

4. 選取現有容器，加以展開並修改下列值：

   * 開啟 [規模與設定]  視窗。
   * 在 [設定]  底下，尋找 [存留時間]  。
   * 選取 [開啟 (無預設)]  ，或選取 [開啟]  並設定 TTL 值。 
   * 按一下 [儲存]  儲存變更。

5. 接著瀏覽至您要設定存留時間的項目、新增 `ttl` 屬性，然後選取 [更新]  。 

   ```json
   {
    "id": "1",
    "_rid": "Jic9ANWdO-EFAAAAAAAAAA==",
    "_self": "dbs/Jic9AA==/colls/Jic9ANWdO-E=/docs/Jic9ANWdO-EFAAAAAAAAAA==/",
    "_etag": "\"0d00b23f-0000-0000-0000-5c7712e80000\"",
    "_attachments": "attachments/",
    "ttl": 10,
    "_ts": 1551307496
   }
   ```

### <a id="dotnet-set-ttl-item"></a>.NET SDK

```csharp
// Include a property that serializes to "ttl" in JSON
public class SalesOrder
{
    [JsonProperty(PropertyName = "id")]
    public string Id { get; set; }
    [JsonProperty(PropertyName="cid")]
    public string CustomerId { get; set; }
    // used to set expiration policy
    [JsonProperty(PropertyName = "ttl", NullValueHandling = NullValueHandling.Ignore)]
    public int? ttl { get; set; }

    //...
}
// Set the value to the expiration in seconds
SalesOrder salesOrder = new SalesOrder
{
    Id = "SO05",
    CustomerId = "CO18009186470",
    ttl = 60 * 60 * 24 * 30;  // Expire sales orders in 30 days
};
```

### <a id="nodejs-set-ttl-item"></a>NodeJS SDK

```javascript
const itemDefinition = {
          id: "doc",
          name: "sample Item",
          key: "value", 
          ttl: 2
        };
```


## <a name="reset-time-to-live"></a>重設存留時間

您可以在項目上執行寫入或更新作業，以重設項目上的存留時間。 寫入或更新作業會將 `_ts` 設定為目前的時間，並重新開始項目的到期 TTL。 如果您想要變更項目的 TTL，則可以如同更新任何其他欄位一樣地更新此欄位。

### <a id="dotnet-extend-ttl-item"></a>.NET SDK

```csharp
// This examples leverages the Sales Order class above.
// Read a document, update its TTL, save it.
response = await client.ReadDocumentAsync(
    "/dbs/salesdb/colls/orders/docs/SO05"),
    new RequestOptions { PartitionKey = new PartitionKey("CO18009186470") });

Document readDocument = response.Resource;
readDocument.ttl = 60 * 30 * 30; // update time to live
response = await client.ReplaceDocumentAsync(readDocument);
```

## <a name="turn-off-time-to-live"></a>關閉存留時間

如果項目上已設定存留時間，而您不再希望該項目會到期，則可以取得該項目、移除 TTL 欄位，然後取代伺服器上的項目。 當項目中移除了 TTL 欄位時，指派給容器的預設 TTL 值便會套用至該項目。 將 TTL 值設定為 -1 可避免項目到期，且不會從容器繼承 TTL 值。

### <a id="dotnet-turn-off-ttl-item"></a>.NET SDK

```csharp
// This examples leverages the Sales Order class above.
// Read a document, turn off its override TTL, save it.
response = await client.ReadDocumentAsync(
    "/dbs/salesdb/colls/orders/docs/SO05"),
    new RequestOptions { PartitionKey = new PartitionKey("CO18009186470") });

Document readDocument = response.Resource;
readDocument.ttl = null; // inherit the default TTL of the collection

response = await client.ReplaceDocumentAsync(readDocument);
```

## <a name="disable-time-to-live"></a>停用存留時間

若要在容器上停用存留時間，並阻止背景處理程序檢查到期的項目，則應刪除容器上的 `DefaultTimeToLive` 屬性。 刪除此屬性與將它設定為 -1 不同。 當您將它設定為 -1 時，新增至容器的新項目將會永遠存在，不過，您可以在容器中的特定項目上覆寫此值。 當您從容器中移除 TTL 屬性時，項目將永遠不會過期，即使這些項目已明確覆寫先前預設的 TTL 值也是一樣。

### <a id="dotnet-disable-ttl"></a>.NET SDK

```csharp
// Get the collection, update DefaultTimeToLive to null
DocumentCollection collection = await client.ReadDocumentCollectionAsync("/dbs/salesdb/colls/orders");
// Disable TTL
collection.DefaultTimeToLive = null;
await client.ReplaceDocumentCollectionAsync(collection);
```

## <a name="next-steps"></a>後續步驟

請於下列文章深入了解存留時間：

* [存留時間](time-to-live.md)
