---
title: "適用於.NET 的 azure DB Cosmos 效能祕訣 |Microsoft 文件"
description: "了解用以改善 Azure Cosmos DB 資料庫效能的用戶端設定選項"
keywords: "如何改善資料庫效能"
services: cosmos-db
author: mimig1
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: 94ff155e-f9bc-488f-8c7a-5e7037091bb9
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/08/2017
ms.author: mimig
ms.openlocfilehash: 84a1913bd218d512f7f2818291f59d98628a7272
ms.sourcegitcommit: 9ea2edae5dbb4a104322135bef957ba6e9aeecde
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/03/2018
---
> [!div class="op_single_selector"]
> * [Java](performance-tips-java.md)
> * [.NET](performance-tips.md)
> 
> 

# <a name="performance-tips-for-azure-cosmos-db-and-net"></a>Azure Cosmos DB 與.NET 的效能提示

[!INCLUDE [cosmos-db-sql-api](../../includes/cosmos-db-sql-api.md)]

Azure Cosmos DB 是一個既快速又彈性的分散式資料庫，可在獲得延遲與輸送量保證的情況下順暢地調整。 您不必在進行主要架構變更，或是撰寫複雜的程式碼來調整資料庫以 Azure Cosmos DB。 相應增加和減少就像進行單一 API 呼叫或 [SDK 方法呼叫](set-throughput.md#set-throughput-sdk)一樣簡單。 不過，由於 Azure Cosmos DB 透過網路呼叫有您可以對使用時，達到尖峰效能的用戶端最佳化[SQL.NET SDK](documentdb-sdk-dotnet.md)。

如果您詢問「如何改善我的資料庫效能？ 」，請考慮下列選項：

## <a name="networking"></a>網路
<a id="direct-connection"></a>

1. **原則︰使用直接連接模式**

    用戶端如何連接到 Azure Cosmos DB 效能，特別是針對觀察到的用戶端延遲有很重要的影響。 有兩個重要組態設定可用來設定用戶端連接原則 - 連接模式和連接[*通訊協定*](#connection-protocol)。  兩個可用的模式︰

   1. 閘道模式 (預設值)
   2. 直接模式

      所有 SDK 平台都支援閘道模式並設為預設值。  如果您的應用程式在有嚴格防火牆限制的公司網路中執行，則閘道模式會是最佳的選擇，因為它會使用標準 HTTPS 連接埠與單一端點。 不過，對於效能的影響，會是閘道模式涉及額外的網路躍點，每次讀取或寫入至 Azure Cosmos 資料庫資料。 因此，直接模式因為網路躍點較少，所以可提供較佳的效能。
<a id="use-tcp"></a>
2. **連接原則︰使用 TCP 通訊協定**

    使用直接模式時，有兩個可用的通訊協定選項：

   * TCP
   * HTTPS

     Azure Cosmos DB 會提供簡單且開啟 RESTful 程式撰寫模型，透過 HTTPS。 此外，它可提供有效率的 TCP 通訊協定，此 TCP 通訊協定在通訊模型中也符合 REST 限制，並且可以透過 .NET 用戶端 SDK 取得。 直接 TCP 和 HTTPS 皆使用 SSL 來進行初始驗證和加密流量。 為了達到最佳效能，儘可能使用 TCP 通訊協定。

     TCP 連接埠 443 TCP 在使用閘道模式時，為 Azure Cosmos DB 的連接埠，且 10255 MongoDB 應用程式開發介面連接埠。 使用 TCP 中時直接存取模式，除了閘道連接埠，您必須確定連接埠 10000 和 20000 之間的範圍是開啟的因為 Azure Cosmos DB 會使用動態 TCP 連接埠。 如果未開啟這些連接埠而您嘗試使用 TCP，您就會收到「503 服務無法使用」錯誤。

     連接模式設定於使用 ConnectionPolicy 參數建構 DocumentClient 執行個體期間。 如果使用直接模式，也可以在 ConnectionPolicy 參數內設定 Protocol。

    ```C#
    var serviceEndpoint = new Uri("https://contoso.documents.net");
    var authKey = new "your authKey from the Azure portal";
    DocumentClient client = new DocumentClient(serviceEndpoint, authKey,
    new ConnectionPolicy
    {
        ConnectionMode = ConnectionMode.Direct,
        ConnectionProtocol = Protocol.Tcp
    });
    ```

    因為只有直接模式支援 TCP，所以如果使用閘道模式，則 HTTPS 通訊協定一律用來與閘道通訊，並忽略 ConnectionPolicy 中的 Protocol 值。

    ![Azure Cosmos DB 連接原則的圖例](./media/performance-tips/connection-policy.png)

3. **呼叫 OpenAsync 以避免第一次要求的啟動延遲**

    根據預設，第一個要求會因為必須擷取位址路由表而有較高的延遲。 若要避免此第一次要求的啟動延遲，您應該在初始化期間呼叫 OpenAsync() 一次，如下所示。

        await client.OpenAsync();
   <a id="same-region"></a>
4. **為了效能在相同 Azure 區域中共置用戶端**

    可能的話，請將任何呼叫 Azure Cosmos DB Azure Cosmos DB 資料庫位於相同區域中的應用程式。 近似的比較，如在同區域內呼叫 Azure Cosmos DB 內完成，1-2 毫秒但西部和美國的東 coast 之間的延遲為 > 50 毫秒。 視要求所採用的路由而定，各項要求從用戶端傳遞至 Azure 資料中心界限時的這類延遲可能有所不同。 最低的可能延遲達成確保呼叫的應用程式位於已佈建 Azure Cosmos DB 端點位於相同 Azure 區域。 如需可用區域的清單，請參閱 [Azure 區域](https://azure.microsoft.com/regions/#services)。

    ![Azure Cosmos DB 連接原則的圖例](./media/performance-tips/same-region.png)
   <a id="increase-threads"></a>
5. **增加執行緒/工作數目**

    由於對 Azure Cosmos DB 的呼叫要透過網路進行，因此您可能需要改變要求的平行處理原則程度，以便讓用戶端應用程式在不同要求之間只需等待很短的時間。 例如，如果您使用。網路的[工作平行程式庫](https://msdn.microsoft.com//library/dd460717.aspx)，建立以 100 的讀取或寫入至 Azure Cosmos DB 的工作順序。

## <a name="sdk-usage"></a>SDK 的使用方式
1. **安裝最新的 SDK**

    Azure Cosmos DB Sdk 會經常被改良，可提供最佳效能。 請參閱[Azure Cosmos DB SDK](documentdb-sdk-dotnet.md)頁面來判斷最新的 SDK，並檢閱增強功能。
2. **使用單一 Azure Cosmos DB 用戶端應用程式的存留期**

    Efach DocumentClient 執行個體是安全執行緒，並執行有效率的連接管理和操作直接模式中時，快取位址。 若要藉由 DocumentClient 獲得有效率的連接管理和更佳的效能，建議在應用程式存留期內對每個 AppDomain 使用單一 DocumentClient 執行個體。

   <a id="max-connection"></a>
3. **增加使用閘道模式時每部主機的 System.Net MaxConnections**

    Azure Cosmos DB 要求都是透過 HTTPS/REST 中，使用閘道模式時，會進行預設連線限制，每個主機名稱或 IP 位址。 您可能需要設定 MaxConnections 更高的值 (100-1000)，讓用戶端程式庫可以利用 Azure Cosmos DB 的多個同時連線。 在 .NET SDK 1.8.0 和更新版本中，[ServicePointManager.DefaultConnectionLimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit.aspx) 的預設值為 50，而若要變更此值，您可以將 [Documents.Client.ConnectionPolicy.MaxConnectionLimit](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.connectionpolicy.maxconnectionlimit.aspx) 設定為更高的值。   
4. **微調分割之集合的平行查詢**

     SQL.NET SDK 版本 1.9.0 和上方支援平行查詢，可讓您查詢資料分割的集合，以平行方式 (請參閱[使用 Sdk](sql-api-partition-data.md#working-with-the-azure-cosmos-db-sdks)與相關[程式碼範例](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/Queries/Program.cs)如需詳細資訊)。 平行查詢的設計目的是要改善其連續對應項目的查詢延遲和輸送量。 平行查詢提供兩個可供使用者微調以符合其需求的參數：(a) MaxDegreeOfParallelism：用來控制可平行查詢的分割數目上限，以及 (b) MaxBufferedItemCount：用來控制預先擷取的結果數目。

    (a) ***微調 MaxDegreeOfParallelism\:*** 平行查詢的運作方式是以平行方式查詢多個分割。 不過，對於查詢會以循序方式擷取來自個別分割集合的資料。 因此，將 MaxDegreeOfParallelism 設定為分割數目會最有機會達到最高效能的查詢，但前提是其他所有系統條件皆維持不變。 如果您不知道分割數目，您可以將 MaxDegreeOfParallelism 設定為較高的數字，然後系統會選擇最小值 (資料分割數目、使用者提供的輸入值) 做為 MaxDegreeOfParallelism。

    請務必注意，若對於查詢是以平均方式將資料分佈於所有分割，平行查詢便會產生最佳效益。 如果分割之集合的分割方式是查詢所傳回的所有或大多數資料集中在少數幾個分割中 (最差的情況是集中在一個分割)，則這些分割會成為查詢效能的瓶頸。

    (b) ***微調 MaxBufferedItemCount\:*** 平行查詢是設計成可以在用戶端處理目前的結果批次時預先擷取結果。 預先擷取有助於改善查詢的整體延遲。 MaxBufferedItemCount 是用來限制預先擷取之結果數量的參數。 將 MaxBufferedItemCount 設定為預期傳回的結果數目 (或更高的數目)，可讓查詢透過預先擷取獲得最大效益。

    預先擷取的運作方式相同無論 MaxDegreeOfParallelism，而且沒有來自所有分割區資料的單一緩衝區。  
5. **開啟伺服器端 GC**

    在某些情況下，降低廢棄項目收集頻率可能會有幫助。 在 .NET 中，請將 [gcServer](https://msdn.microsoft.com/library/ms229357.aspx) 設定為 true。
6. **在 RetryAfter 間隔實作降速**

    在進行效能測試期間，您應該增加負載，直到系統對小部分要求進行節流處理為止。 如果進行節流處理，用戶端應用程式應該在節流時降速，且持續時間達伺服器指定的重試間隔。 採用降速可確保您在重試之間花費最少的等待時間。 重試原則支援是內含的和更新版本 1.8.0 的 SQL [.NET](sql-api-sdk-dotnet.md)和[Java](sql-api-sdk-java.md)，1.9.0 版本和更新版本的[Node.js](sql-api-sdk-node.md)和[Python](sql-api-sdk-python.md)，所有支援的版本和[.NET Core](sql-api-sdk-dotnet-core.md) Sdk。 如需詳細資訊，請參閱[超過保留的輸送量限制](request-units.md#RequestRateTooLarge)和 [RetryAfter](https://msdn.microsoft.com/library/microsoft.azure.documents.documentclientexception.retryafter.aspx)。
7. **相應放大用戶端工作負載**

    如果您是以高輸送量層級 (> 50,000 RU/秒) 進行測試，用戶端應用程式可能會成為瓶頸，因為電腦對 CPU 或網路的使用率將達到上限。 如果達到這個點之後，您可以繼續擴充用戶端應用程式，在多部伺服器，將推播進一步的 Azure Cosmos DB 帳戶。
8. **快取較低讀取延遲的文件 URI**

    盡可能快取文件 URI 以達到最佳讀取效能。
   <a id="tune-page-size"></a>
9. **調整查詢/讀取摘要的頁面大小以獲得更好的效能**

    執行大量讀取時使用讀取摘要功能 (例如，ReadDocumentFeedAsync) 的文件，或發出 SQL 查詢時，結果會以分割的方式傳回，如果結果集太大。 根據預設，會以 100 個項目或 1 MB 的區塊傳回結果 (以先達到的限制為準)。

    若要減少的網路往返，才能擷取所有適用的結果、 頁面大小使用，您可以增加[x-ms-最大的項目-計數](https://docs.microsoft.com/rest/api/documentdb/common-documentdb-rest-request-headers)最多 1000 個要求標頭。 在您只需要顯示幾個結果的情況下 (例如，您的使用者介面或應用程式 API 一次只傳回 10 筆結果)，您也可以將頁面大小縮小為 10，以降低讀取和查詢所耗用的輸送量。

    您也可以設定使用可用的 Azure Cosmos DB Sdk 的頁面大小。  例如︰

        IQueryable<dynamic> authorResults = client.CreateDocumentQuery(documentCollection.SelfLink, "SELECT p.Author FROM Pages p WHERE p.Title = 'About Seattle'", new FeedOptions { MaxItemCount = 1000 });
10. **增加執行緒/工作數目**

    請參閱＜網路＞一節中的[增加執行緒/工作數目](#increase-threads)。

11. **使用 64 位元主機處理序**

    當您使用 SQL.NET SDK 版本 1.11.4 和更高版本，32 位元主控件程序中適用於 SQL SDK。 不過，若使用跨分割區查詢，建議您使用 64 位元主機處理以獲得改進的效能。 下列的應用程式類型預設使用 32 位元主機處理序，若要將其變更為 64 位元，請根據您的應用程式類型依照下列步驟執行：

    - 針對「可執行檔」應用程式，做法是在 [專案屬性] 視窗中的 [建置] 索引標籤上取消選取 [建議使用 32 位元] 選項。

    - 針對 VSTest 型的測試專案，可以從 [Visual Studio 測試] 功能表選項，選取 [測試]->[測試設定]->[以 X64 做為預設處理器架構] 來完成。

    - 針對本機部署的 ASP.NET Web 應用程式，可以在 [工具]->[選項]->[專案和方案]->[Web 專案] 之下，選取 [將 64 位元版本的 IIS Express 用於網站和專案] 來完成。

    - 針對部署於 Azure 上的 ASP.NET Web 應用程式，可以在 Azure 入口網站上的 [應用程式設定] 中選擇 [以 64 位元做為平台] 來完成。

## <a name="indexing-policy"></a>索引原則
 
1. **從索引編製中排除未使用的路徑以加快寫入速度**

    Cosmos DB 的索引編製原則也可讓您利用檢索路徑 (IndexingPolicy.IncludedPaths 和 IndexingPolicy.ExcludedPaths)，指定要在索引編製中包含或排除的文件路徑。 在事先知道查詢模式的案例中，使用檢索路徑可改善寫入效能並降低索引儲存空間，因為檢索成本與檢索的唯一路徑數目直接相互關聯。  例如，以下程式碼示範如何將文件的整個區段 (也稱為 樹狀子目錄) 自索引編製作業中排除 (透過使用 "*" 萬用字元)。

    ```C#
    var collection = new DocumentCollection { Id = "excludedPathCollection" };
    collection.IndexingPolicy.IncludedPaths.Add(new IncludedPath { Path = "/*" });
    collection.IndexingPolicy.ExcludedPaths.Add(new ExcludedPath { Path = "/nonIndexedContent/*");
    collection = await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), excluded);
    ```

    如需詳細資訊，請參閱 [Azure Cosmos DB 索引編製原則](indexing-policies.md)。

## <a name="throughput"></a>Throughput
<a id="measure-rus"></a>

1. **測量和調整較低的要求單位/秒使用量**

    Azure Cosmos DB 會提供一組豐富的資料庫作業，包括關聯式及階層式查詢 Udf、 預存程序與觸發程序-所有作業的資料庫集合中的文件。 與上述各項作業相關聯的成本，會因為完成作業所需的 CPU、IO 和記憶體而不同。 您不需要考慮和管理硬體資源，您可以將要求單位 (RU) 想成是執行各種資料庫作業以及服務應用程式要求時所需的資源數量。

    根據數目佈建輸送量[要求單位](request-units.md)設定每個容器。 要求單位消耗量是以每秒的速率來計算。 如果應用程式的速率超過為其容器佈建的要求單位速率，便會受到限制，直到該速率降到容器的佈建層級以下。 如果您的應用程式需要較高的輸送量，您可以藉由佈建其他的要求單位來增加輸送量。 

    查詢的複雜性會影響針對作業所耗用的要求單位數量。 述詞數目、述詞性質、UDF 數目，以及來源資料集的大小，全都會影響查詢作業的成本。

    量值的任何作業的額外負荷 （建立、 更新或刪除），檢查[x ms-要求免費](https://docs.microsoft.com/rest/api/documentdb/common-documentdb-rest-response-headers)標頭 (或相等 RequestCharge 屬性中 ResourceResponse<T>或 FeedResponse<T>中。NET SDK) 來測量的這些作業所使用的要求單位數。

    ```C#
    // Measure the performance (request units) of writes
    ResourceResponse<Document> response = await client.CreateDocumentAsync(collectionSelfLink, myDocument);
    Console.WriteLine("Insert of document consumed {0} request units", response.RequestCharge);
    // Measure the performance (request units) of queries
    IDocumentQuery<dynamic> queryable = client.CreateDocumentQuery(collectionSelfLink, queryString).AsDocumentQuery();
    while (queryable.HasMoreResults)
         {
              FeedResponse<dynamic> queryResponse = await queryable.ExecuteNextAsync<dynamic>();
              Console.WriteLine("Query batch consumed {0} request units", queryResponse.RequestCharge);
         }
    ```             

    在此標頭中傳回的要求費用是佈建輸送量的一小部分 (也就是 2000 RU / 秒)。 例如，如果前述查詢傳回 1000 份 1KB 文件，則作業成本會是 1000。 因此在一秒內，伺服器在節流後續要求前，只會接受兩個這類要求。 如需詳細資訊，請參閱[要求單位](request-units.md)和[要求單位計算機](https://www.documentdb.com/capacityplanner)。
<a id="429"></a>
2. **處理速率限制/要求速率太大**

    當用戶端嘗試超過帳戶保留的輸送量時，伺服器的效能不會降低，而且不會使用超過保留層級的輸送量容量。 伺服器會先結束 RequestRateTooLarge （HTTP 狀態碼 429） 的要求，並傳回[x ms-重試-之後-ms](https://docs.microsoft.com/rest/api/documentdb/common-documentdb-rest-response-headers)指出所需的時間 （毫秒），使用者必須等候其標頭要求。

        HTTP Status 429,
        Status Line: RequestRateTooLarge
        x-ms-retry-after-ms :100

    SDK 全都隱含地攔截這個回應，採用伺服器指定的 retry-after 標頭，並重試此要求。 除非有多個用戶端同時存取您的帳戶，否則下次重試將會成功。

    如果您有多個用戶端都以高於要求速率的方式累積運作，則用戶端在內部設定為 9 的預設重試計數可能會不敷使用；在此情況下，用戶端會擲回 DocumentClientException (狀態碼 429) 到應用程式。 在 ConnectionPolicy 執行個體上設定 RetryOptions，即可變更預設重試次數。 根據預設，如果要求繼續以高於要求速率的方式運作，則會在 30 秒的累計等候時間後傳回 DocumentClientException (狀態碼 429)。 即使目前的重試計數小於最大重試計數 (預設值 9 或使用者定義的值)，也會發生這種情況。

    雖然自動重試行為有助於改善大部分應用程式的恢復功能和可用性，但是在進行效能基準測試時可能會有所歧異 (尤其是在測量延遲時)。  如果實驗達到伺服器節流並導致用戶端 SDK 以無訊息模式重試，則用戶端觀察到的延遲將會突然增加。 若要避免效能實驗期間的延遲尖峰，測量每個作業所傳回的費用，並確保要求是以低於保留要求速率的方式運作。 如需詳細資訊，請參閱 [要求單位](request-units.md)。
3. **輸送量較高之少量文件的設計**

    指定之作業的要求費用 (也就是要求處理成本) 與文件大小直接相互關聯。 大型文件的作業成本高於小型文件的作業成本。

## <a name="next-steps"></a>後續步驟
用來評估 Azure Cosmos DB 幾個用戶端電腦上的高效能案例的範例應用程式，請參閱[效能和延展性測試 Azure Cosmos DB](performance-testing.md)。

此外，若要深入了解如何針對規模和高效能設計您的應用程式，請參閱 [Azure Cosmos DB 的資料分割與調整規模](partition-data.md)。
