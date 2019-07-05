---
title: 為 Azure Cosmos DB 資料來源編製索引 - Azure 搜尋服務
description: 搜耙 Azure Cosmos DB 資料來源，並在 Azure 搜尋服務中的全文檢索搜尋索引中內嵌資料。 索引子可為選取的資料來源 (例如 Azure Cosmos DB) 將資料擷取自動化。
ms.date: 05/02/2019
author: mgottein
manager: cgronlun
ms.author: magottei
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: 7f9df42725e41fb514370dbdb828ad5b1305ea78
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/01/2019
ms.locfileid: "67485457"
---
# <a name="how-to-index-cosmos-db-using-an-azure-search-indexer"></a>如何使用 Azure 搜尋服務索引子的 Cosmos DB 編製索引


> [!Note]
> MongoDB API 支援是處於預覽階段，應用程式不是用於生產環境而定。 [REST API 版本 2019-05-06-Preview](search-api-preview.md) 提供此功能。 沒有任何入口網站或.NET SDK 支援此功能。
>
> SQL API 已正式推出。

本文說明如何設定 Azure Cosmos DB [indexer](search-indexer-overview.md)擷取內容，並使其可在 Azure 搜尋服務。 此工作流程建立 Azure 搜尋服務索引，並將其載入與擷取自 Azure Cosmos DB 的現有文字。 

因為詞彙可能會造成混淆，值得注意的是， [Azure Cosmos DB 索引編製](https://docs.microsoft.com/azure/cosmos-db/index-overview)並[Azure 搜尋服務編製索引](search-what-is-an-index.md)是不同的作業，每個服務專用的。 之前啟動 Azure 搜尋服務編製索引，您的 Azure Cosmos DB 資料庫必須已經存在且包含的資料。

您可以使用[入口網站](#cosmos-indexer-portal)，REST Api 或.NET SDK Cosmos 內容索引。 Azure 搜尋服務中的 Cosmos DB 索引子可以搜耙[Azure Cosmos 項目](https://docs.microsoft.com/azure/cosmos-db/databases-containers-items#azure-cosmos-items)透過這些通訊協定存取：

* [SQL API](https://docs.microsoft.com/azure/cosmos-db/sql-api-query-reference) 
* [MongoDB API （預覽）](https://docs.microsoft.com/azure/cosmos-db/mongodb-introduction)

> [!Note]
> User Voice 有其他 API 支援的現有項目。 您可以轉型 Cosmos api 您想要查看支援 Azure 搜尋服務中的投票：[資料表 API](https://feedback.azure.com/forums/263029-azure-search/suggestions/32759746-azure-search-should-be-able-to-index-cosmos-db-tab)，[圖形 API](https://feedback.azure.com/forums/263029-azure-search/suggestions/13285011-add-graph-databases-to-your-data-sources-eg-neo4)， [Apache Cassandra API](https://feedback.azure.com/forums/263029-azure-search/suggestions/32857525-indexer-crawler-for-apache-cassandra-api-in-azu)。
>

<a name="cosmos-indexer-portal"></a>

## <a name="use-the-portal"></a>使用入口網站

Azure Cosmos 項目編製索引的最簡單方法是使用中的精靈[Azure 入口網站](https://portal.azure.com/)。 藉由取樣資料，並讀取容器中繼資料[**匯入資料**](search-import-data-portal.md) Azure 搜尋服務中的精靈可以建立預設的索引、 來源欄位對應至目標索引欄位，並載入在單一索引作業。 根據來源資料的大小和複雜度，只需要幾分鐘的時間，您就可以擁有正常運作的全文檢索搜尋索引。

我們建議使用相同的 Azure 訂用帳戶的 Azure 搜尋服務和 Azure Cosmos DB 中，最好位於相同的區域。

### <a name="1---prepare-source-data"></a>1 - 準備來源資料

您應該有 Cosmos 帳戶，對應至 SQL API 或 MongoDB API 和 JSON 文件容器的 Azure Cosmos 資料庫。 

請確定您的 Cosmos DB 資料庫包含資料。 [匯入資料精靈](search-import-data-portal.md)讀取中繼資料，並會執行資料取樣來推斷索引結構描述，但它也將資料載入 Cosmos DB 中。 如果遺漏的資料，精靈就會停止與此錯誤 」 錯誤偵測索引結構描述資料來源：無法建立原型索引因為資料來源 'emptycollection' 未不傳回任何資料 」。

### <a name="2---start-import-data-wizard"></a>2 - 啟動匯入資料精靈

您可以[啟動精靈](search-import-data-portal.md)從命令列在 Azure 搜尋服務 頁面中，或按一下**新增 Azure 搜尋服務**中**設定**左邊的儲存體帳戶區段瀏覽窗格中。

   ![在入口網站匯入資料命令](./media/search-import-data-portal/import-data-cmd2.png "啟動匯入資料精靈")

### <a name="3---set-the-data-source"></a>3 - 設定資料來源

> [!NOTE] 
> 目前，您無法建立或編輯**MongoDB**使用 Azure 入口網站或.NET SDK 的資料來源。 不過，您**可以**在入口網站中監視 MongoDB 索引子的執行歷程記錄。

在 **資料來源**頁面上，來源必須**Cosmos DB**，使用下列規格：

+ **名稱**是資料來源物件的名稱。 建立之後，您可以針對其他工作負載中選擇它。

+ **Cosmos DB 帳戶**應該是 Cosmos DB 的主要或次要連接字串具有`AccountEndpoint`和`AccountKey`。 帳戶可讓您決定是否要將資料轉換為 SQL API 或 Mongo DB API

+ **資料庫**是從帳戶現有的資料庫。 

+ **集合**是文件的容器。 文件必須存在於匯入成功的順序。 

+ **查詢**可以是空白如果您想要的所有文件，否則您可以輸入選取文件子集的查詢。 

   ![Cosmos DB 資料來源定義](media/search-howto-index-cosmosdb/cosmosdb-datasource.png "Cosmos DB 資料來源定義")

### <a name="4---skip-the-add-cognitive-search-page-in-the-wizard"></a>4 - 略過精靈的 [新增認知搜尋] 頁面

新增認知技術並不需要匯入文件。 除非您有特殊需要，而必須將[認知服務 API 和轉換納入](cognitive-search-concept-intro.md)編製索引管線，否則請略過此步驟。

若要略過此步驟，先移至下一個頁面。

   ![認知搜尋的下一頁按鈕](media/search-get-started-portal/next-button-add-cog-search.png)

從該頁面就可以跳至索引自訂。

   ![跳過認知技術步驟](media/search-get-started-portal/skip-cog-skill-step.png)

### <a name="5---set-index-attributes"></a>5 - 設定索引屬性

在 [索引]  頁面上，您應該會看到欄位清單，其中有資料類型以及一系列用於設定索引屬性的核取方塊。 精靈可以產生根據中繼資料，並藉由取樣來源資料的欄位 清單。 

您可以大量選取屬性，即可在屬性資料行頂端的核取方塊。 選擇**可擷取**並**Searchable**應該傳回至用戶端應用程式，並受限於全文檢索搜尋處理的每一個欄位。 您會注意到整數不是完整的文字或模糊搜尋 （數字會逐字評估及通常可用於篩選）。

檢閱其描述[屬性編製索引](https://docs.microsoft.com/rest/api/searchservice/create-index#bkmk_indexAttrib)並[語言分析器](https://docs.microsoft.com/rest/api/searchservice/language-support)如需詳細資訊。 

請花一點時間檢閱您的選擇。 一旦執行精靈，就會建立實體的資料結構，而且除非您捨棄並重新建立所有物件，否則將無法編輯這些欄位。

   ![Cosmos DB 編製索引的定義](media/search-howto-index-cosmosdb/cosmosdb-index-schema.png "Cosmos DB 索引定義")

### <a name="6---create-indexer"></a>6 - 建立索引子

若完整指定，精靈會在搜尋服務中建立三個不同的物件。 資料來源物件和索引物件會在 Azure 搜尋服務中儲存為具名資源。 最後一個步驟則會建立索引子物件。 為索引子命名可讓索引子以獨立資源的形式存在，索引子可以獨立地排程及管理，而不會受制於索引和資料來源物件 (這兩個物件會在相同的精靈序列中建立)。

如果您不熟悉索引子，「索引子」  是 Azure 搜尋服務中的資源，其會搜耙外部資料來源中的可搜尋內容。 輸出**匯入資料**精靈是可搜耙您 Cosmos DB 資料來源、 擷取可搜尋的內容，並將其匯入到 Azure 搜尋服務索引的索引子。

下列螢幕擷取畫面顯示預設索引子組態。 您可以切換到**一次**如果您想要執行索引子一次。 按一下 **送出**執行精靈並建立所有物件。 編製索引的程序會立即開始。

   ![Cosmos DB 索引子定義](media/search-howto-index-cosmosdb/cosmosdb-indexer.png "Cosmos DB 索引子定義")

您可以在入口網站的頁面中監視資料匯入過程。 進度通知會指出編製索引的狀態，以及所上傳的文件數量。 

編製索引的程序完成後，您可以使用[搜尋總管](search-explorer.md)來查詢索引。

> [!NOTE]
> 如果您沒有看到您預期的資料，您可能需要多個欄位上設定其他屬性。 刪除索引和索引子，您剛建立的並逐步執行精靈一次，修改您在步驟 5 中的索引屬性的選項。 

<a name="cosmosdb-indexer-rest"></a>

## <a name="use-rest-apis"></a>使用 REST API

您可以使用 REST API 來建立 Azure Cosmos DB 的資料，Azure 搜尋服務中的所有索引子來遵循常見的三段式工作流程： 建立資料來源、 建立索引、 建立索引子。 當您建立索引子要求提交，就會發生從 Cosmos 存放區的資料擷取。 完成此要求之後，您必須可供查詢的索引。 

如果您正在評估 MongoDB，您必須使用 REST`api-version=2019-05-06-Preview`來建立資料來源。

在您的 Cosmos DB 帳戶中，您可以選擇是否要讓集合自動編製所有文件的索引。 根據預設，所有文件會自動編制索引，但您可以關閉自動編制索引。 關閉編製索引時，只能透過文件自己的連結或使用文件識別碼透過查詢來存取文件。 「Azure 搜尋服務」會要求在將由「Azure 搜尋服務」編製索引的集合中，必須開啟 Cosmos DB 自動編製索引功能。 

> [!WARNING]
> Azure Cosmos DB 是新一代的 DocumentDB。 先前使用 api-version **2017年-11-11**您可以使用`documentdb`語法。 這表示您可以指定資料來源類型，如此`cosmosdb`或`documentdb`。 從 API 版本開始**2019年-05-06** Azure 搜尋服務 Api 和入口網站都只支援`cosmosdb`語法這篇文章中的指示。 這表示資料來源類型必須`cosmosdb`如果您想要連線到 Cosmos DB 端點。

### <a name="1---assemble-inputs-for-the-request"></a>1-組合要求的輸入

針對每個要求中，您必須提供服務名稱和系統管理金鑰 （POST 標頭中），Azure 搜尋服務和儲存體帳戶名稱和 blob 儲存體金鑰。 您可以使用[Postman](search-get-started-postman.md)將 HTTP 要求傳送至 Azure 搜尋服務。

將下列四項值複製到 記事本，讓您可以將其貼到 要求：

+ Azure 搜尋服務名稱
+ Azure 搜尋服務管理金鑰
+ Cosmos DB 連接字串

您可以在入口網站中找到這些值：

1. 在 Azure 搜尋服務入口網站頁面中，請從 [概觀] 頁面中複製的搜尋服務 URL。

2. 在左側的導覽窗格中，按一下**金鑰**然後複製其中一個主要或次要金鑰 （它們是相等的）。

3. 切換到 Cosmos 儲存體帳戶的入口網站頁面。 在左側的導覽窗格中，在**設定**，按一下**金鑰**。 此頁面提供的 URI，兩組的連接字串和兩組金鑰。 將其中一個連接字串複製到 [記事本]。

### <a name="2---create-a-data-source"></a>2-建立資料來源

**資料來源**指定要編製索引的資料、認證，以及可識別資料是否變更 (例如修改或刪除集合內的文件) 的原則。 資料來源會被定義為獨立的資源，因此可供多個索引子使用。

若要建立資料來源，制訂的 POST 要求：

    POST https://[service name].search.windows.net/datasources?api-version=2019-05-06
    Content-Type: application/json
    api-key: [Search service admin key]

    {
        "name": "mycosmosdbdatasource",
        "type": "cosmosdb",
        "credentials": {
            "connectionString": "AccountEndpoint=https://myCosmosDbEndpoint.documents.azure.com;AccountKey=myCosmosDbAuthKey;Database=myCosmosDbDatabaseId"
        },
        "container": { "name": "myCollection", "query": null },
        "dataChangeDetectionPolicy": {
            "@odata.type": "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
            "highWaterMarkColumnName": "_ts"
        }
    }

要求的主體包含資料來源定義，其中應包含下列欄位：

| 欄位   | 描述 |
|---------|-------------|
| **name** | 必要。 選擇任何名稱，以代表您的資料來源物件。 |
|**type**| 必要。 必須是 `cosmosdb`。 |
|**credentials** | 必要。 必須是 Cosmos DB 連接字串。<br/>對於 SQL 集合，連接字串會以下列格式： `AccountEndpoint=<Cosmos DB endpoint url>;AccountKey=<Cosmos DB auth key>;Database=<Cosmos DB database id>`<br/>針對 MongoDB 集合，新增 **/api 種類 = MongoDb**的連接字串：<br/>`AccountEndpoint=<Cosmos DB endpoint url>;AccountKey=<Cosmos DB auth key>;Database=<Cosmos DB database id>;ApiKind=MongoDb`<br/>請避免在端點 URL 中使用連接埠號碼。 如果您加入連接埠號碼，Azure 搜尋服務將無法為 Azure Cosmos DB 資料庫編製索引。|
| **container** | 包含下列元素： <br/>**名稱**：必要。 指定要編製索引的資料庫集合識別碼。<br/>**查詢**：選用。 您可以指定查詢將任意 JSON 文件簡維成 Azure 搜尋服務可以編製索引的一般結構描述。<br/>針對 MongoDB 集合，不支援查詢。 |
| **dataChangeDetectionPolicy** | 建議使用。 請參閱[索引變更的文件](#DataChangeDetectionPolicy)小節。|
|**dataDeletionDetectionPolicy** | 選用。 請參閱[索引刪除的文件](#DataDeletionDetectionPolicy)小節。|

### <a name="using-queries-to-shape-indexed-data"></a>使用查詢來形塑索引的資料
您可以指定 SQL 查詢來壓平合併巢狀屬性或陣列、投影 JSON 屬性，以及篩選要編製索引的資料。 

> [!WARNING]
> **MongoDB** 集合不支援自訂查詢：`container.query` 參數必須設定為 Null 或省略。 如果您需要使用自訂查詢，請在 [User Voice](https://feedback.azure.com/forums/263029-azure-search) 告訴我們。

範例文件︰

    {
        "userId": 10001,
        "contact": {
            "firstName": "andy",
            "lastName": "hoh"
        },
        "company": "microsoft",
        "tags": ["azure", "cosmosdb", "search"]
    }

篩選查詢：

    SELECT * FROM c WHERE c.company = "microsoft" and c._ts >= @HighWaterMark ORDER BY c._ts

壓平合併查詢︰

    SELECT c.id, c.userId, c.contact.firstName, c.contact.lastName, c.company, c._ts FROM c WHERE c._ts >= @HighWaterMark ORDER BY c._ts
    
    
投影查詢：

    SELECT VALUE { "id":c.id, "Name":c.contact.firstName, "Company":c.company, "_ts":c._ts } FROM c WHERE c._ts >= @HighWaterMark ORDER BY c._ts


陣列壓平合併查詢︰

    SELECT c.id, c.userId, tag, c._ts FROM c JOIN tag IN c.tags WHERE c._ts >= @HighWaterMark ORDER BY c._ts


### <a name="3---create-a-target-search-index"></a>3-建立目標搜尋索引 

[建立目標 Azure 搜尋服務索引](/rest/api/searchservice/create-index)如果您還沒有。 下列範例會建立具有識別碼和描述欄位的索引：

    POST https://[service name].search.windows.net/indexes?api-version=2019-05-06
    Content-Type: application/json
    api-key: [Search service admin key]

    {
       "name": "mysearchindex",
       "fields": [{
         "name": "id",
         "type": "Edm.String",
         "key": true,
         "searchable": false
       }, {
         "name": "description",
         "type": "Edm.String",
         "filterable": false,
         "sortable": false,
         "facetable": false,
         "suggestions": true
       }]
     }

請確定目標索引的結構描述會與來源 JSON 文件的結構描述或自訂查詢投射的輸出相容。

> [!NOTE]
> 針對分割的集合，預設文件索引鍵是 Azure Cosmos DB 的 `_rid` 屬性，Azure 搜尋服務會自動將該屬性重新命名為 `rid`，因為欄位名稱不能以底線字元開頭。 此外，Azure Cosmos DB `_rid` 值包含 Azure 搜尋服務索引鍵中無效的字元。 因此，`_rid` 值採用 Base64 編碼。
> 
> 針對 MongoDB 集合，Azure 搜尋服務會自動將 `_id` 屬性重新命名為 `doc_id`。  

### <a name="mapping-between-json-data-types-and-azure-search-data-types"></a>JSON 資料類型與 Azure 搜尋服務資料類型之間的對應
| JSON 資料類型 | 相容的目標索引欄位類型 |
| --- | --- |
| Bool |Edm.Boolean、Edm.String |
| 看起來像是整數的數字 |Edm.Int32、Edm.Int64、Edm.String |
| 看起來像是浮點的數字 |Edm.Double、Edm.String |
| String |Edm.String |
| 基本類型的陣列，例如 ["a", "b", "c"] |Collection(Edm.String) |
| 看起來像是日期的字串 |Edm.DateTimeOffset、Edm.String |
| GeoJSON 物件，例如 { "type":"Point", "coordinates": [long, lat] } |Edm.GeographyPoint |
| 其他 JSON 物件 |N/A |

### <a name="4---configure-and-run-the-indexer"></a>4-設定及執行索引子

建立索引和資料來源之後，您就可以開始建立索引子︰

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "mycosmosdbindexer",
      "dataSourceName" : "mycosmosdbdatasource",
      "targetIndexName" : "mysearchindex",
      "schedule" : { "interval" : "PT2H" }
    }

這個索引子每隔兩小時就會執行一次 (已將排程間隔設為 "PT2H")。 若每隔 30 分鐘就要執行索引子，可將間隔設為 "PT30M"。 支援的最短間隔為 5 分鐘。 排程為選擇性 - 如果省略，索引子只會在建立時執行一次。 不過，您隨時都可依需求執行索引子。   

如需建立索引子 API 的詳細資訊，請參閱 [建立索引子](https://docs.microsoft.com/rest/api/searchservice/create-indexer)。

如需定義索引子排程的詳細資訊，請參閱[如何排程 Azure 搜尋服務索引子](search-howto-schedule-indexers.md)。

## <a name="use-net"></a>使用 .NET

正式推出的.NET SDK 已正式推出的 REST api 的完全相同。 建議您檢閱先前的 REST API 章節，以了解其概念、工作流程和需求。 然後，您可以參閱下列 .NET API 參考文件，以在受控程式碼中實作 JSON 索引子。

+ [microsoft.azure.search.models.datasource](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasource?view=azure-dotnet)
+ [microsoft.azure.search.models.datasourcetype](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasourcetype?view=azure-dotnet) 
+ [microsoft.azure.search.models.index](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index?view=azure-dotnet) 
+ [microsoft.azure.search.models.indexer](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer?view=azure-dotnet)

<a name="DataChangeDetectionPolicy"></a>

## <a name="indexing-changed-documents"></a>索引已變更的文件

資料變更偵測原則是用來有效識別已變更的資料項目。 目前，唯一支援的原則是使用 Azure Cosmos DB 所提供之 `_ts` (時間戳記) 屬性的 `High Water Mark` 原則，指定方式如下：

    {
        "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
        "highWaterMarkColumnName" : "_ts"
    }

為確保索引子效能良好，強烈建議使用此原則。 

如果您使用自訂查詢，請確定查詢有投射 `_ts` 屬性。

<a name="IncrementalProgress"></a>

### <a name="incremental-progress-and-custom-queries"></a>累加進度與自訂查詢

建立索引期間，採累加進度能確保當索引子執行因暫時性失敗或執行時間限制而中斷時，索引子仍能夠在下次執行時從中斷處繼續建立索引，而不需要重新建立整個集合的索引。 這在建立大型集合的索引時尤其重要。 

若要在使用自訂查詢時啟用累加進度，請確保您的查詢是依 `_ts` 欄排序查詢結果。 如此會啟用定期檢查點設置，出現失敗時，Azure 搜尋服務會以此提供累加進度來應對。   

在某些情況下，即使您的查詢含有 `ORDER BY [collection alias]._ts` 子句，Azure 搜尋服務仍可能無法推斷此查詢是否依 `_ts` 排序。 您可以使用 `assumeOrderByHighWaterMarkColumn` 這個設定屬性讓 Azure 搜尋服務知道查詢結果已經過排序。 若要指定這項提示，請依下列指示更新您的索引子： 

    {
     ... other indexer definition properties
     "parameters" : {
            "configuration" : { "assumeOrderByHighWaterMarkColumn" : true } }
    } 

<a name="DataDeletionDetectionPolicy"></a>

## <a name="indexing-deleted-documents"></a>索引已刪除的文件

從集合中刪除資料列時，通常也會想刪除搜尋索引內的那些資料列。 資料刪除偵測原則可用來有效識別刪除的資料項目。 目前，唯一支援的原則是「 `Soft Delete` 」原則 (刪除會標示為某種形式的旗標)，指定方式如下：

    {
        "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
        "softDeleteColumnName" : "the property that specifies whether a document was deleted",
        "softDeleteMarkerValue" : "the value that identifies a document as deleted"
    }

如果您使用自訂查詢，請確定查詢有投射到 `softDeleteColumnName` 參考的屬性。

下列範例會建立包含虛刪除原則的資料來源：

    POST https://[service name].search.windows.net/datasources?api-version=2019-05-06
    Content-Type: application/json
    api-key: [Search service admin key]

    {
        "name": "mycosmosdbdatasource",
        "type": "cosmosdb",
        "credentials": {
            "connectionString": "AccountEndpoint=https://myCosmosDbEndpoint.documents.azure.com;AccountKey=myCosmosDbAuthKey;Database=myCosmosDbDatabaseId"
        },
        "container": { "name": "myCosmosDbCollectionId" },
        "dataChangeDetectionPolicy": {
            "@odata.type": "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
            "highWaterMarkColumnName": "_ts"
        },
        "dataDeletionDetectionPolicy": {
            "@odata.type": "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
            "softDeleteColumnName": "isDeleted",
            "softDeleteMarkerValue": "true"
        }
    }

## <a name="NextSteps"></a>接續步驟

恭喜！ 您已了解如何使用索引子來整合 Azure Cosmos DB 與 Azure 搜尋服務。

* 若要深入了解 Azure Cosmos DB，請參閱 [Azure Cosmos DB 服務頁面](https://azure.microsoft.com/services/cosmos-db/)。
* 若要深入了解 Azure 搜尋服務，請參閱 [[搜尋服務] 頁面](https://azure.microsoft.com/services/search/)。
