---
title: "For Java 的 azure DB Cosmos 效能祕訣 |Microsoft 文件"
description: "了解用以改善 Azure Cosmos DB 資料庫效能的用戶端設定選項"
keywords: "如何改善資料庫效能"
services: cosmos-db
author: mimig1
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: dfe8f426-3c98-4edc-8094-092d41f2795e
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/02/2018
ms.author: mimig
ms.openlocfilehash: ca16a7fe424e9c50ce87b150442dd18ff0d6ce91
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

# <a name="performance-tips-for-azure-cosmos-db-and-java"></a>有關 Azure Cosmos DB 和 Java 的效能提示
Azure Cosmos DB 是一個既快速又彈性的分散式資料庫，可在獲得延遲與輸送量保證的情況下順暢地調整。 您不必在進行主要架構變更，或是撰寫複雜的程式碼來調整資料庫以 Azure Cosmos DB。 相應增加和減少就像進行單一 API 呼叫或 [SDK 方法呼叫](set-throughput.md#set-throughput-java)一樣簡單。 不過，由於 Azure Cosmos DB 透過網路呼叫有您可以對使用時，達到尖峰效能的用戶端最佳化[SQL Java SDK](documentdb-sdk-java.md)。

如果您詢問「如何改善我的資料庫效能？ 」，請考慮下列選項：

## <a name="networking"></a>網路
<a id="direct-connection"></a>

1. **連接模式： 使用 DirectHttps**

    用戶端如何連接到 Azure Cosmos DB 效能，特別是針對觀察到的用戶端延遲有很重要的影響。 還有一個索引鍵的組態設定可以用來設定用戶端[ConnectionPolicy](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._connection_policy) – [ConnectionMode](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._connection_mode)。  兩個可用 ConnectionModes 如下：

   1. [閘道 （預設值）](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._connection_mode.gateway)
   2. [DirectHttps](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._connection_mode.directhttps)

    閘道模式支援所有 SDK 平台上，而且是已設定的預設值。  如果嚴格防火牆限制在公司網路中執行您的應用程式，閘道會是最佳選擇，因為它會使用標準 HTTPS 連接埠與單一端點。 不過，對於效能的影響，會是閘道模式涉及額外的網路躍點，每次讀取或寫入至 Azure Cosmos 資料庫資料。 因為這個緣故，DirectHttps 模式提供更佳的效能，因為較少的網路躍點。 

    Java SDK 會使用 HTTPS 做為傳輸通訊協定。 HTTPS 會使用 SSL 來初始驗證與加密流量。 當使用 Java SDK 時，只有 HTTPS 連接埠 443 必須能夠開啟。 

    ConnectionMode DocumentClient 執行個體與 ConnectionPolicy 參數的建構期間設定。 

    ```Java
    public ConnectionPolicy getConnectionPolicy() {
        ConnectionPolicy policy = new ConnectionPolicy();
        policy.setConnectionMode(ConnectionMode.DirectHttps);
        policy.setMaxPoolSize(1000);
        return policy;
    }
        
    ConnectionPolicy connectionPolicy = new ConnectionPolicy();
    DocumentClient client = new DocumentClient(HOST, MASTER_KEY, connectionPolicy, null);
    ```

    ![Azure Cosmos DB 連接原則的圖例](./media/performance-tips-java/connection-policy.png)

   <a id="same-region"></a>
2. **為了效能在相同 Azure 區域中共置用戶端**

    可能的話，請將任何呼叫 Azure Cosmos DB Azure Cosmos DB 資料庫位於相同區域中的應用程式。 近似的比較，如在同區域內呼叫 Azure Cosmos DB 內完成，1-2 毫秒但西部和美國的東 coast 之間的延遲為 > 50 毫秒。 視要求所採用的路由而定，各項要求從用戶端傳遞至 Azure 資料中心界限時的這類延遲可能有所不同。 最低的可能延遲達成確保呼叫的應用程式位於已佈建 Azure Cosmos DB 端點位於相同 Azure 區域。 如需可用區域的清單，請參閱 [Azure 區域](https://azure.microsoft.com/regions/#services)。

    ![Azure Cosmos DB 連接原則的圖例](./media/performance-tips/same-region.png)
   
## <a name="sdk-usage"></a>SDK 的使用方式
1. **安裝最新的 SDK**

    Azure Cosmos DB Sdk 會經常被改良，可提供最佳效能。 請參閱[Azure Cosmos DB SDK](documentdb-sdk-java.md)頁面來判斷最新的 SDK，並檢閱增強功能。
2. **使用單一 Azure Cosmos DB 用戶端應用程式的存留期**

    每個[DocumentClient](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client)執行個體是安全執行緒，且會執行有效率的連接管理和操作直接模式中時，快取位址。 若要藉由 DocumentClient 獲得有效率的連接管理和更佳的效能，建議在應用程式存留期內對每個 AppDomain 使用單一 DocumentClient 執行個體。

   <a id="max-connection"></a>
3. **使用閘道模式時，增加 Sqlserversink 每部主機**

    Azure Cosmos DB 要求都是透過 HTTPS/REST 中，使用閘道模式時，會進行預設連線限制，每個主機名稱或 IP 位址。 您可能需要設定 Sqlserversink 更高的值 (200-1000)，讓用戶端程式庫可以利用 Azure Cosmos DB 的多個同時連線。 在 Java SDK 中，預設值為[ConnectionPolicy.getMaxPoolSize](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.documentdb._connection_policy.gsetmaxpoolsize)為 100。 使用[setMaxPoolSize]( https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.documentdb._connection_policy.setmaxpoolsize)來變更值。

4. **微調分割之集合的平行查詢**

    Azure Cosmos DB SQL Java SDK 版本 1.9.0 和上方支援平行查詢，可讓您查詢資料分割的集合，以平行方式 (請參閱[使用 Sdk](sql-api-partition-data.md#working-with-the-azure-cosmos-db-sdks)與相關[程式碼範例](https://github.com/Azure/azure-documentdb-java/tree/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples)的詳細的資訊）。 平行查詢的設計目的是要改善其連續對應項目的查詢延遲和輸送量。

    （a)***微調 setMaxDegreeOfParallelism\:*** 平行查詢多個資料分割，以平行方式來查詢工作。 不過，相對於查詢時循序提取資料的個別資料分割集合。 因此，使用[setMaxDegreeOfParallelism](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._feed_options.setmaxdegreeofparallelism)設定資料分割數目已達到最高效能查詢最大的機會，提供所有其他的系統條件維持不變。 如果您不知道的資料分割數目，您可用來設定數字，setMaxDegreeOfParallelism，系統會選擇最小值 （資料分割，提供使用者輸入的數字），作為平行處理原則的最大程度。 

    請務必注意，若對於查詢是以平均方式將資料分佈於所有分割，平行查詢便會產生最佳效益。 如果分割之集合的分割方式是查詢所傳回的所有或大多數資料集中在少數幾個分割中 (最差的情況是集中在一個分割)，則這些分割會成為查詢效能的瓶頸。

    （b)***微調 setMaxBufferedItemCount\:*** 平行查詢都設計成用戶端在處理目前的批次的結果時，預先提取結果。 預先擷取有助於改善查詢的整體延遲。 setMaxBufferedItemCount 限制預先擷取的結果數目。 藉由設定[setMaxBufferedItemCount](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._feed_options.setmaxbuffereditemcount)預期傳回的結果數目 （或更高的數字），這可讓查詢以從預先讀取的最大好處。

    預先擷取的運作方式相同無論 MaxDegreeOfParallelism，而且沒有來自所有分割區資料的單一緩衝區。  

5. **實作撤退間隔 getRetryAfterInMilliseconds**

    在進行效能測試期間，您應該增加負載，直到系統對小部分要求進行節流處理為止。 如果進行節流處理，用戶端應用程式應該在節流時降速，且持續時間達伺服器指定的重試間隔。 採用降速可確保您在重試之間花費最少的等待時間。 重試原則支援是內含的和更新版本 1.8.0 的[Java SDK](documentdb-sdk-java.md)。 如需詳細資訊，請參閱[Exceeding 保留輸送量限制](request-units.md#RequestRateTooLarge)和[getRetryAfterInMilliseconds](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.documentdb._document_client_exception.getretryafterinmilliseconds)。
6. **相應放大用戶端工作負載**

    如果您要測試高輸送量層級 (> 50000 RU/秒)，用戶端應用程式可能會變成瓶頸，因為電腦須出上 CPU 或網路使用率。 如果達到這個點之後，您可以繼續擴充用戶端應用程式，在多部伺服器，將推播進一步的 Azure Cosmos DB 帳戶。

7. **使用名稱為基礎的定址**

    使用名稱型定址連結具有格式，其中`dbs/MyDatabaseId/colls/MyCollectionId/docs/MyDocumentId`，而不是 SelfLinks (_self)，其具有格式`dbs/<database_rid>/colls/<collection_rid>/docs/<document_rid>`以避免擷取用來建構連結的所有資源的資源識別碼。 此外，因為這些資源取得重新建立 （可能具有相同的名稱），這些快取可能無法幫助。

   <a id="tune-page-size"></a>
8. **調整查詢/讀取摘要的頁面大小以獲得更好的效能**

    當執行大量讀取的文件所使用的讀取摘要功能 (例如， [readDocuments]( https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client.readdocuments#com_microsoft_azure_documentdb__document_client_readDocuments_String_FeedOptions_c)或如果結果集太大時發出 SQL 查詢，結果會傳回以分割方式。 根據預設，會以 100 個項目或 1 MB 的區塊傳回結果 (以先達到的限制為準)。

    若要減少的網路往返，才能擷取所有適用的結果、 頁面大小使用，您可以增加[x-ms-最大的項目-計數](https://docs.microsoft.com/rest/api/documentdb/common-documentdb-rest-request-headers)最多 1000 個要求標頭。 在您只需要顯示幾個結果的情況下 (例如，您的使用者介面或應用程式 API 一次只傳回 10 筆結果)，您也可以將頁面大小縮小為 10，以降低讀取和查詢所耗用的輸送量。

    您也可以設定頁面大小使用[setPageSize 方法](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.documentdb._feed_options_base.setpagesize#com_microsoft_azure_documentdb__feed_options_base_setPageSize_Integer)。

## <a name="indexing-policy"></a>索引原則
 
1. **從索引編製中排除未使用的路徑以加快寫入速度**

    Azure Cosmos DB 的編製索引原則可讓您指定要包含或排除的索引編製索引的路徑，利用哪些文件路徑 ([setIncludedPaths](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._indexing_policy.setincludedpaths)和[setExcludedPaths](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._indexing_policy.setexcludedpaths))。 在事先知道查詢模式的案例中，使用檢索路徑可改善寫入效能並降低索引儲存空間，因為檢索成本與檢索的唯一路徑數目直接相互關聯。  例如，以下程式碼示範如何將文件的整個區段 (也稱為 樹狀子目錄) 自索引編製作業中排除 (透過使用 "*" 萬用字元)。

    ```Java
    Index numberIndex = Index.Range(DataType.Number);
    numberIndex.set("precision", -1);
    indexes.add(numberIndex);
    includedPath.setIndexes(indexes);
    includedPaths.add(includedPath);
    indexingPolicy.setIncludedPaths(includedPaths);
    collectionDefinition.setIndexingPolicy(indexingPolicy);
    ```

    如需詳細資訊，請參閱 [Azure Cosmos DB 索引編製原則](indexing-policies.md)。

## <a name="throughput"></a>Throughput
<a id="measure-rus"></a>

1. **測量和調整較低的要求單位/秒使用量**

    Azure Cosmos DB 會提供一組豐富的資料庫作業，包括關聯式及階層式查詢 Udf、 預存程序與觸發程序-所有作業的資料庫集合中的文件。 與上述各項作業相關聯的成本，會因為完成作業所需的 CPU、IO 和記憶體而不同。 您不需要考慮和管理硬體資源，您可以將要求單位 (RU) 想成是執行各種資料庫作業以及服務應用程式要求時所需的資源數量。

    根據數目佈建輸送量[要求單位](request-units.md)設定每個容器。 要求單位消耗量是以每秒的速率來計算。 如果應用程式的速率超過為其容器佈建的要求單位速率，便會受到限制，直到該速率降到容器的佈建層級以下。 如果您的應用程式需要較高的輸送量，您可以藉由佈建其他的要求單位來增加輸送量。 

    查詢的複雜性會影響作業耗用多少要求單位。 述詞數目、述詞性質、UDF 數目，以及來源資料集的大小，全都會影響查詢作業的成本。

    量值的任何作業的額外負荷 （建立、 更新或刪除），檢查[x ms-要求免費](https://docs.microsoft.com/rest/api/documentdb/common-documentdb-rest-response-headers)標頭 (或相等 RequestCharge 屬性中的[ResourceResponse<T> ](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._resource_response)或[FeedResponse<T> ](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.documentdb._feed_response)來測量的這些作業所使用的要求單位數。

    ```Java
    ResourceResponse<Document> response = client.createDocument(collectionLink, documentDefinition, null, false);

    response.getRequestCharge();
    ```             

    傳回此標頭的要求需要付費的是您佈建輸送量的分數。 例如，如果有 2000 RU/秒佈建，而且如果上述查詢會傳回 1000年 1 KB 文件的操作成本 1000年。 因此在一秒內，伺服器在節流後續要求前，只會接受兩個這類要求。 如需詳細資訊，請參閱[要求單位](request-units.md)和[要求單位計算機](https://www.documentdb.com/capacityplanner)。
<a id="429"></a>
2. **處理速率限制/要求速率太大**

    當用戶端嘗試超過帳戶保留的輸送量時，伺服器的效能不會降低，而且不會使用超過保留層級的輸送量容量。 伺服器會先結束 RequestRateTooLarge （HTTP 狀態碼 429） 的要求，並傳回[x ms-重試-之後-ms](https://docs.microsoft.com/rest/api/documentdb/common-documentdb-rest-response-headers)指出所需的時間 （毫秒），使用者必須等候其標頭要求。

        HTTP Status 429,
        Status Line: RequestRateTooLarge
        x-ms-retry-after-ms :100

    SDK 全都隱含地攔截這個回應，採用伺服器指定的 retry-after 標頭，並重試此要求。 除非有多個用戶端同時存取您的帳戶，否則下次重試將會成功。

    如果您有多個要求速率高於累積一致的方式運作的用戶端，可能不敷使用目前設為 9 在內部用戶端的預設重試計數;在此情況下，用戶端會擲回[DocumentClientException](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client_exception) ，狀態碼為 429 應用程式。 可以透過變更預設重試次數[setRetryOptions](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.documentdb._connection_policy.setretryoptions)上[ConnectionPolicy](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._connection_policy)執行個體。 根據預設，如果要求繼續以高於要求速率的方式運作，則會在 30 秒的累計等候時間後傳回 DocumentClientException (狀態碼 429)。 即使目前的重試計數小於最大重試計數 (預設值 9 或使用者定義的值)，也會發生這種情況。

    雖然自動重試行為有助於改善大部分應用程式的恢復功能和可用性，但是在進行效能基準測試時可能會有所歧異 (尤其是在測量延遲時)。  如果實驗達到伺服器節流並導致用戶端 SDK 以無訊息模式重試，則用戶端觀察到的延遲將會突然增加。 若要避免效能實驗期間的延遲尖峰，測量每個作業所傳回的費用，並確保要求是以低於保留要求速率的方式運作。 如需詳細資訊，請參閱 [要求單位](request-units.md)。
3. **輸送量較高之少量文件的設計**

    指定的作業則要求費用 （要求處理成本） 直接相互關聯的文件大小。 大型文件的作業成本高於小型文件的作業成本。

## <a name="next-steps"></a>後續步驟
若要了解有關設計的小數位數和高效能的應用程式的詳細資訊，請參閱[資料分割和 Azure Cosmos DB 中的縮放比例](partition-data.md)。
