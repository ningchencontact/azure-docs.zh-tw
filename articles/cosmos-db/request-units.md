---
title: 要求單位和預估輸送量 - Azure Cosmos DB | Microsoft Docs
description: 了解如何在 Azure Cosmos DB 中了解、指定及預估要求單位需求。
services: cosmos-db
author: SnehaGunda
manager: kfile
documentationcenter: ''
ms.assetid: d0a3c310-eb63-4e45-8122-b7724095c32f
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2018
ms.author: rimman
ms.openlocfilehash: 7290c12e7d96ac01c66d97103920793f98120b38
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/07/2018
---
# <a name="request-units-in-azure-cosmos-db"></a>Azure Cosmos DB 中的要求單位

[Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) 是 Microsoft 的全域分散式多模型資料庫。 有了 Azure Cosmos DB，您就不需要租用虛擬機器、部署軟體或監視資料庫。 Microsoft 頂尖工程師會負責操作並持續監視 Azure Cosmos DB，提供世界級的可用性、效能和資料保護能力。 您可以使用所選擇的 API 來存取資料，例如 [SQL API](documentdb-introduction.md)、[MongoDB API](mongodb-introduction.md)、[資料表 API](table-introduction.md) 和透過 [Gremlin API](graph-introduction.md) 的圖形，這些全都是原生支援的 API。 

Azure Cosmos DB 的貨幣是**要求單位 (RU)**。 使用 RU 時，您不需保留讀取/寫入容量，或是佈建 CPU、記憶體及 IOPS。 Azure Cosmos DB 支援數種 API 以執行各種不同的作業，從簡單地讀取及寫入，到複雜的圖表查詢等等。 因為並非所有的要求都相等，所以系統會根據處理要求所需的計算量，指派標準化的**要求單位**數量。 作業的要求單位數具決定性，您可以在 Azure Cosmos DB 中透過回應標頭追蹤任何作業所取用的要求單位數。 

若要提供可預測的效能，您需要保留每秒 100 RU 的輸送量單位。 您可以使用 Azure Cosmos DB [要求單位計算機](https://www.documentdb.com/capacityplanner) \(英文\)，以[評估您的輸送量需求](request-units.md#estimating-throughput-needs)。

![輸送量計算機][5]

閱讀本文後，您將能夠回答下列問題：  

* Azure Cosmos DB 中的要求單位和要求費用是什麼？
* 如何指定 Azure Cosmos DB 中的容器或一組容器的要求單位容量？
* 如何估計應用程式的要求單位需求？
* 如果超過 Azure Cosmos DB 中的容器或一組容器的要求單位容量，會發生什麼情況？

因為 Azure Cosmos DB 是多模型資料庫，請留意這篇文章是適用於 Azure Cosmos DB 中的所有資料模型和 API。 本文會使用一般詞彙 (例如*容器*和*項目*) 來分別統稱集合、圖形或資料表和文件、節點或實體。

## <a name="request-units-and-request-charges"></a>要求單位和要求費用
Azure Cosmos DB 藉由「保留」資源以滿足應用程式的輸送量需求，來提供快速且可預測的效能。  因為應用程式會隨著時間載入和存取模式變化，所以 Azure Cosmos DB 可讓您輕鬆地增加或減少應用程式可用的保留輸送量。

有了 Azure Cosmos DB，保留的輸送量是根據每秒處理的要求單位來指定。 您可以將要求單位想像為輸送量貨幣，因此您每秒可「保留」  保證可供應用程式使用的要求單位數量。  Azure Cosmos DB 中的每個作業 (寫入文件、執行查詢、更新文件) 都會耗用 CPU、記憶體和 IOPS。  也就是說，每個作業都會產生「要求費用」，這是以「要求單位」來表示。  了解影響要求單位費用的因素，以及您應用程式的輸送量需求，可讓您儘可能以最符合經濟效益的方式來執行應用程式。 Azure 入口網站中的 [資料總管] 也是測試查詢核心的絕佳工具。

我們建議您一開始先觀賞下列影片，Azure Cosmos DB 程式總監 Andrew Liu 會在影片中探討要求單位。

> [!VIDEO https://www.youtube.com/embed/stk5WSp5uX0]
> 
> 

## <a name="specifying-request-unit-capacity-in-azure-cosmos-db"></a>在 Azure Cosmos DB 中指定要求單位容量

您可以指定要為個別容器或一組容器保留的每秒要求單位數 (每秒 RU)。 Azure Cosmos DB 會根據所佈建的輸送量，配置實體分割區來裝載您的容器，並隨著輸送量的成長來跨分割區分割/重新平衡資料。

指派個別容器層級的 RU/秒時，容器可以建立為*固定*或*無限制的*形式。 固定大小的容器具有上限為 10 GB 和 10,000 RU/秒的輸送量。 若要建立無限制的容器，您必須指定最小輸送量 1,000 RU/s 和[分割區索引鍵](partition-data.md)。 由於您的資料可能必須分散在多個分割區，因此必須挑選基數高 (100 個到數百萬個相異值) 的分割區索引鍵。 藉由選取具有許多相異值的分割區索引鍵，您便可確保 Azure Cosmos DB 可以一致地調整您的容器/資料表/圖表和要求。 

指派跨一組容器的 RU/秒時，屬於這組容器的容器會視為*無限制的*容器，且必須指定分割區索引鍵。

![佈建個別容器和一組容器的要求單位][6]

> [!NOTE]
> 資料分割索引鍵是一種邏輯界限，而不是實體界限。 因此，您不需要限制不同資料分割索引鍵值的數目。 事實上，最好能擁有較多不同的資料分割索引鍵值，Azure Cosmos DB 才有更多的負載平衡選項。

以下程式碼片段會使用 SQL API 的 .NET SDK，為個別容器建立一個具有每秒 3,000 個要求單位的容器：

```csharp
DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "coll";
myCollection.PartitionKey.Paths.Add("/deviceId");

await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("db"),
    myCollection,
    new RequestOptions { OfferThroughput = 3000 });
```

以下程式碼片段會使用 SQL API 的 .NET SDK，在一組容器間佈建每秒 100,000 個要求單位：

```csharp
// Provision 100,000 RU/sec at the database level. 
// sharedCollection1 and sharedCollection2 will share the 100,000 RU/sec from the parent database
// dedicatedCollection will have its own dedicated 4,000 RU/sec, independant of the 100,000 RU/sec provisioned from the parent database
Database database = client.CreateDatabaseAsync(new Database { Id = "myDb" }, new RequestOptions { OfferThroughput = 100000 }).Result;

DocumentCollection sharedCollection1 = new DocumentCollection();
sharedCollection1.Id = "sharedCollection1";
sharedCollection1.PartitionKey.Paths.Add("/deviceId");

await client.CreateDocumentCollectionAsync(database.SelfLink, sharedCollection1, new RequestOptions())

DocumentCollection sharedCollection2 = new DocumentCollection();
sharedCollection2.Id = "sharedCollection2";
sharedCollection2.PartitionKey.Paths.Add("/deviceId");

await client.CreateDocumentCollectionAsync(database.SelfLink, sharedCollection2, new RequestOptions())

DocumentCollection dedicatedCollection = new DocumentCollection();
dedicatedCollection.Id = "dedicatedCollection";
dedicatedCollection.PartitionKey.Paths.Add("/deviceId");

await client.CreateDocumentCollectionAsync(database.SelfLink, dedicatedCollection, new RequestOptions { OfferThroughput = 4000 )
```


Azure Cosmos DB 針對輸送量會以保留模型的方式運作。 也就是說，您需要針對所「保留」的輸送量支付費用，而不論該輸送量中有多少數量是主動「使用」的。 當您應用程式的負載、資料及使用方式模式變更時，您可以透過 SDK 或使用 [Azure 入口網站](https://portal.azure.com)，輕鬆地相應增加和減少保留 RU 的數量。

每個容器或一組容器會對應至 Azure Cosmos DB 中的一個 `Offer` 資源，其中有所佈建輸送量的相關中繼資料。 藉由查閱容器的對應 offer 資源，然後使用新的輸送量值加以更新，即可變更已配置的輸送量。 以下程式碼片段會使用 .NET SDK 將容器的輸送量變更為每秒 5,000 個要求單位：

```csharp
// Fetch the resource to be updated
// For a updating throughput for a set of containers, replace the collection's self link with the database's self link
Offer offer = client.CreateOfferQuery()
                .Where(r => r.ResourceLink == collection.SelfLink)    
                .AsEnumerable()
                .SingleOrDefault();

// Set the throughput to 5000 request units per second
offer = new OfferV2(offer, 5000);

// Now persist these changes to the database by replacing the original resource
await client.ReplaceOfferAsync(offer);
```

當您變更輸送量時，不會影響容器或一組容器的可用性。 新保留的輸送量通常會在套用新輸送量的數秒內於生效。

## <a name="throughput-isolation-in-globally-distributed-databases"></a>輸送量隔離是分散在世界各地的資料庫

當您將資料庫複寫至多個區域後，Azure Cosmos DB 會提供輸送量隔離，以確保一個區域的 RU 使用量不會影響另一個地區的 RU 使用量。 例如，如果您將資料寫入至一個區域，並從另一個區域讀取資料，則用於在區域 *A* 執行寫入作業的 RU，將不會減少用於在區域 *B* 執行讀取作業的 RU。RU 不會分散於已部署的多個區域。 複製資料庫的每個區域都已佈建充分的 RU 數量。 如需全域複寫的詳細資訊，請參閱[如何使用 Azure Cosmos DB 在全域散發資料](distribute-data-globally.md)。

## <a name="request-unit-considerations"></a>要求單位的考量
在估計要佈建的要求單位數量時，請務必考量下列變數：

* **項目大小**。 大小增加時，讀取或寫入資料所耗用的要求單位也會增加。
* **項目屬性計數**。 假設預設會編製所有屬性的索引，撰寫文件/節點/實體所耗用的單位將會隨著屬性計數增加而增加。
* **資料一致性**。 當使用「強式」或「限定過期」的資料一致性模型時，會耗用額外要求單位來讀取項目。
* **已編製索引的屬性**。 每個容器上的索引原則會判定預設要編製哪些索引的屬性。 您可以限制已編製索引的屬性數目或讓索引延遲編製，以減少寫入作業的要求單位耗用量。
* **編製文件索引**。 預設會自動編製每個項目的索引。 如果您選擇不要編製某些項目的索引，則會耗用較少的要求單位。
* **查詢模式**。 查詢的複雜性會影響針對作業所耗用的要求單位數量。 查詢結果數目、述詞數目、述詞性質、預測、UDF 數目，以及來源資料的大小，全都會影響查詢作業的成本。
* **指令碼使用方式**。  查詢、預存程序和觸發程序會根據所執行作業的複雜度來耗用要求單位。 開發應用程式時，請檢查要求費用標頭，進一步了解每項作業如何耗用要求單位容量。

## <a name="estimating-throughput-needs"></a>估計輸送量需求
要求單位是要求處理成本的標準化量值。 單一要求單位代表讀取 (透過自我連結或識別碼) 由 10 個唯一屬性值 (不包括系統屬性) 所組成的單一 1KB 項目所需的處理容量。 建立 (插入)、取代或刪除相同項目的要求將會耗用服務的更多處理，因此會耗用更多的要求單位。   

> [!NOTE]
> 1 KB 項目 1 個要求單位的基準，可與依據項目自我連結或識別碼的簡單 GET 對應。
> 
> 

例如，下表顯示在三個不同大小 (1 KB、4 KB 和 64 KB) 的項目 ，以及兩個不同效能等級 (500 次讀取/秒 + 100 次寫入/秒和 500 次讀取/秒 + 500 次寫入/秒)，要佈建多少要求單位。 資料一致性已設定為 [工作階段]，且索引編製原則已設定為 [無]。

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top"><p><strong>項目大小</strong></p></td>
            <td valign="top"><p><strong>讀取/秒</strong></p></td>
            <td valign="top"><p><strong>寫入/秒</strong></p></td>
            <td valign="top"><p><strong>要求單位</strong></p></td>
        </tr>
        <tr>
            <td valign="top"><p>1 KB</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>100</p></td>
            <td valign="top"><p>(500 * 1) + (100 * 5) = 1,000 RU/s</p></td>
        </tr>
        <tr>
            <td valign="top"><p>1 KB</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>(500 * 1) + (500 * 5) = 3,000 RU/s</p></td>
        </tr>
        <tr>
            <td valign="top"><p>4 KB</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>100</p></td>
            <td valign="top"><p>(500 * 1.3) + (100 * 7) = 1,350 RU/s</p></td>
        </tr>
        <tr>
            <td valign="top"><p>4 KB</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>(500 * 1.3) + (500 * 7) = 4,150 RU/s</p></td>
        </tr>
        <tr>
            <td valign="top"><p>64 KB</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>100</p></td>
            <td valign="top"><p>(500 * 10) + (100 * 48) = 9,800 RU/s</p></td>
        </tr>
        <tr>
            <td valign="top"><p>64 KB</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>(500 * 10) + (500 * 48) = 29,000 RU/s</p></td>
        </tr>
    </tbody>
</table>

### <a name="use-the-request-unit-calculator"></a>使用要求單位計算機
若要協助客戶微調其輸送量預估，有一個 Web 型[要求單位計算機](https://www.documentdb.com/capacityplanner) \(英文\) 可協助預估一般作業的要求單位需求，包括︰

* 項目建立 (寫入)
* 項目讀取
* 項目刪除
* 項目更新

此工具也支援根據您提供的範例項目預估資料儲存需求。

使用此工具很簡單︰

1. 上傳一或多個具代表性的項目 (例如範例 JSON 文件)。
   
    ![將項目上傳至要求單位計算機][2]
2. 若要預估資料儲存需求，請輸入您預期要儲存的項目 (例如文件、資料列或頂點) 總數。
3. 輸入您需要的建立、讀取、更新和刪除作業數 (以每秒為單位)。 若要預估項目更新作業的要求單位費用，請上傳一份上述步驟 1 中包含一般欄位更新的範例項目。  例如，如果項目更新通常會修改名為 *lastLogin* 和 *userVisits* 的兩個屬性，請複製範例項目，更新那兩個屬性的值，並上傳複製的項目。
   
    ![在要求單位計算機中輸入輸送量需求][3]
4. 按一下計算，並檢查結果。
   
    ![要求單位計算機結果][4]

> [!NOTE]
> 如果您的項目類型與已編製索引之屬性的大小與數目截然不同，則請將每個一般項目「類型」的範例上傳至工具，然後計算結果。
> 
> 

### <a name="use-the-azure-cosmos-db-request-charge-response-header"></a>使用 Azure Cosmos DB 要求費用回應標頭
Azure Cosmos DB 服務的每個回應都會包括自訂標頭 (`x-ms-request-charge`)，其中包含指定要求所耗用的要求單位。 此標頭也可以透過 Azure Cosmos DB SDK 進行存取。 在 .NET SDK 中，`RequestCharge` 是 `ResourceResponse` 物件的屬性。  針對查詢，Azure 入口網站中的 Azure Cosmos DB 資料總管會提供已執行查詢的要求費用資訊。

有鑑於此，若要估計您的應用程式所需的保留輸送量，其中一個方法為對照應用程式使用的代表性項目，記錄與執行一般作業相關聯的要求單位費用，然後估計您預期每秒會執行的作業數目。  此外，請務必測量並包含一般查詢和 Azure Cosmos DB 指令碼使用方式。

> [!NOTE]
> 如果您的項目類型在大小和已編製索引的屬性數目上差異極大，則請記錄與每個一般項目「類型」關聯的適用作業要求單位費用。
> 
> 

例如︰

1. 記錄建立 (插入) 一般項目的要求單位費用。 
2. 記錄讀取一般項目的要求單位費用。
3. 記錄更新一般項目的要求單位費用。
4. 記錄一般且常見項目查詢的要求單位費用。
5. 記錄應用程式所使用的任何自訂指令碼 (預存程序、觸發程序、使用者定義的函式) 的要求單位費用
6. 根據您預期每秒執行的作業估計數目，來計算必要的要求單位數。

## <a name="a-request-unit-estimate-example"></a>要求單位估計範例
請考量下列 ~1 KB 的文件：

```json
{
 "id": "08259",
  "description": "Cereals ready-to-eat, KELLOGG, KELLOGG'S CRISPIX",
  "tags": [
    {
      "name": "cereals ready-to-eat"
    },
    {
      "name": "kellogg"
    },
    {
      "name": "kellogg's crispix"
    }
  ],
  "version": 1,
  "commonName": "Includes USDA Commodity B855",
  "manufacturerName": "Kellogg, Co.",
  "isFromSurvey": false,
  "foodGroup": "Breakfast Cereals",
  "nutrients": [
    {
      "id": "262",
      "description": "Caffeine",
      "nutritionValue": 0,
      "units": "mg"
    },
    {
      "id": "307",
      "description": "Sodium, Na",
      "nutritionValue": 611,
      "units": "mg"
    },
    {
      "id": "309",
      "description": "Zinc, Zn",
      "nutritionValue": 5.2,
      "units": "mg"
    }
  ],
  "servings": [
    {
      "amount": 1,
      "description": "cup (1 NLEA serving)",
      "weightInGrams": 29
    }
  ]
}
```

> [!NOTE]
> 文件在 Azure Cosmos DB 中會變小，因此系統針對上述文件計算出的大小會稍微小於 1 KB。
> 
> 

下表顯示本項目中一般作業的概略要求單位費用 (概略的要求單位費用假設帳戶一致性層級已設定為 [工作階段]，且所有項目都已自動編製索引)：

| 作業 | 要求單位費用 |
| --- | --- |
| 建立項目 |~15 RU |
| 讀取項目 |~1 RU |
| 依識別碼查詢項目 |~2.5 RU |

此外，下表顯示應用程式中所使用之標準查詢的概略要求單位費用︰

| 查詢 | 要求單位費用 | # 個傳回的項目 |
| --- | --- | --- |
| 依識別碼選取食物 |~2.5 RU |1 |
| 依製造商選取食物 |~7 RU |7 |
| 依食物群組選取並依重量排序 |~70 RU |100 |
| 選取食物群組中的前 10 個食物 |~10 RU |10 |

> [!NOTE]
> RU 費用會根據傳回的項目數目而有所不同。
> 
> 

有了此資訊，您便可以根據所預期每秒的作業數和查詢數，來預估此應用程式的 RU 需求︰

| 作業/查詢 | 每秒估計的數目 | 必要的 RU 數 |
| --- | --- | --- |
| 建立項目 |10 |150 |
| 讀取項目 |100 |100 |
| 依製造商選取食物 |25 |175 |
| 依食物群組選取 |10 |700 |
| 選取前 10 個 |15 |總共 150 個 |

在此情況下，您會預期平均輸送量需求為 1,275 RU/秒。  如果四捨五入至最接近 100 的數目，您就會針對此應用程式的容器 (或一組容器) 佈建 1,300 RU/s。

## <a id="RequestRateTooLarge"></a> 超過 Azure Cosmos DB 中保留的輸送量限制
您應該記得，要求單位耗用量是以每秒的速率來估算。 如果應用程式超過佈建的要求單位速率，要求便會受到速率限制，直到該速率降到佈建的輸送量層級以下。 當要求受到速率限制時，伺服器會使用 `RequestRateTooLargeException` (HTTP 狀態碼 429) 來提前結束要求，並傳回 `x-ms-retry-after-ms` 標頭，以指出使用者重新嘗試要求之前必須等候的時間量 (以毫秒為單位)。

    HTTP Status 429
    Status Line: RequestRateTooLarge
    x-ms-retry-after-ms :100

如果您使用 .NET 用戶端 SDK 和 LINQ 查詢，則在大部分時間都不會需要處理這個例外狀況，因為目前版本的 .NET 用戶端 SDK 會隱含地攔截這個回應，遵守伺服器指定的 retry-after 標頭，然後自動重試要求。 除非有多個用戶端同時存取您的帳戶，否則下次重試將會成功。

如果您有多個用戶端會以高於要求速率的方式累積運作，則預設的重試行為可能會不敷使用，且用戶端將會擲回具有狀態碼 429 的 `DocumentClientException` 到應用程式。 在類似這樣的情況下，您可以考慮在應用程式的錯誤處理常式中處理重試行為和邏輯，或為容器 (或一組容器) 提高佈建的輸送量。

## <a name="next-steps"></a>後續步驟
若要深入了解透過 Azure Cosmos DB 資料庫保留輸送量的方式，請探索下列資源：

* [Azure Cosmos DB 定價](https://azure.microsoft.com/pricing/details/cosmos-db/)
* [在 Azure Cosmos DB 中分割資料](partition-data.md)

若要深入了解 Azure Cosmos DB，請參閱 Azure Cosmos DB [文件](https://azure.microsoft.com/documentation/services/cosmos-db/)。 

若要開始使用 Azure Cosmos DB 的相關規模和效能測試，請參閱 [Azure Cosmos DB 的相關效能和規模測試](performance-testing.md)。

[2]: ./media/request-units/RUEstimatorUpload.png
[3]: ./media/request-units/RUEstimatorDocuments.png
[4]: ./media/request-units/RUEstimatorResults.png
[5]: ./media/request-units/RUCalculator2.png
[6]: ./media/request-units/provisioning_set_containers.png
