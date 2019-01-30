---
title: 為 Azure Blob 索引子的 JSON Blob 編製索引以用於全文檢索搜尋 - Azure 搜尋服務
description: 使用 Azure 搜尋服務 Blob 索引子，搜耙文字內容的 Azure JSON Blob。 索引子可為選取的資料來源 (例如 Azure Blob 儲存體) 將資料擷取自動化。
ms.date: 12/21/2018
author: HeidiSteen
manager: cgronlun
ms.author: heidist
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: cafb48f28e38794ce0757d50a5d87432b237e17c
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2019
ms.locfileid: "54467157"
---
# <a name="indexing-json-blobs-with-azure-search-blob-indexer"></a>使用 Azure 搜尋服務 Blob 索引子為 JSON Blob 編製索引
本文說明如何設定 Azure 搜尋服務 Blob 索引子，從 Azure Blob 儲存體中的 JSON blob 擷取結構化的內容。

您可以使用[入口網站](#json-indexer-portal)、[REST API](#json-indexer-rest) 或 [.NET SDK](#json-indexer-dotnet) 為 JSON 內容編製索引。 這些方法的共通點是 JSON 文件，其位於 Azure 儲存體帳戶中的 Blob 容器內。 如需如何從其他非 Azure 平台推送 JSON 文件的指引，請參閱 [Azure 搜尋服務中的資料匯入](search-what-is-data-import.md)。

Azure Blob 儲存體中的 JSON blob 通常是單一 JSON 文件或 JSON 陣列。 Azure 搜尋服務中的 Blob 索引子可以剖析其中一種結構，取決於您如何設定在要求上的 **parsingMode** 參數。

> [!IMPORTANT]
> JSON Blob 編製索引已正式推出，但 JsonArray 剖析仍處於公開預覽狀態，且不應用於生產環境。 如需詳細資訊，請參閱 [REST api-version=2017-11-11-Preview](search-api-2017-11-11-preview.md)。 

<a name="json-indexer-portal"></a>

## <a name="use-the-portal"></a>使用入口網站

為 JSON 文件索引編製時，最簡單的方法是使用 [Azure 入口網站](https://portal.azure.com/)中的精靈。 藉由剖析 Azure Blob 容器中的中繼資料，[**匯入資料**](search-import-data-portal.md)精靈可以建立預設索引、將來源欄位對應至目標索引欄位，並在單一作業中載入索引。 根據來源資料的大小和複雜度，只需要幾分鐘的時間，您就可以擁有正常運作的全文檢索搜尋索引。

### <a name="1---prepare-source-data"></a>1 - 準備來源資料

請準備 Azure 儲存體帳戶，此帳戶必須有 Blob 儲存體和 JSON 文件容器。 如果您不熟悉上述任何一項工作，請檢閱[認知搜尋 - 快速入門](cognitive-search-quickstart-blob.md#set-up-azure-blob-service-and-load-sample-data)中的＜設定 Azure Blob 服務並載入範例資料＞必要條件。

### <a name="2---start-import-data-wizard"></a>2 - 啟動匯入資料精靈

您可以從 Azure 搜尋服務頁面中的命令列[啟動精靈](search-import-data-portal.md)，也可以藉由在儲存體帳戶左側瀏覽窗格的 [Blob 服務] 區段中按一下 [新增 Azure 搜尋服務] 來啟動。

### <a name="3---set-the-data-source"></a>3 - 設定資料來源

在 [資料來源] 頁面中，來源必須是 **Azure Blob 儲存體**，並具有下列規格：

+ [要擷取的資料] 應該是 [內容和中繼資料]。 選擇此選項可讓精靈推斷索引結構描述，並對應要匯入的欄位。
   
+ [剖析模式] 應該設定為 [JSON] 或 [JSON 陣列]。 

  [JSON] 會將每個 Blob 清楚表達為單一搜尋文件，而在搜尋結果中顯示為獨立項目。 

  [JSON 陣列] 則用於由多個元素組成的 Blob，您希望其中的每個元素都清楚表達為獨立的搜尋文件。 如果 Blob 很複雜，而且您未選擇 [JSON 陣列]，則整個 Blob 會擷取為單一文件。
   
+ [儲存體容器] 必須指定儲存體帳戶和容器，或是會解析為容器的連接字串。 您可以在 Blob 服務的入口網站頁面上取得連接字串。

   ![Blob 資料來源定義](media/search-howto-index-json/import-wizard-json-data-source.png)

### <a name="4---skip-the-add-cognitive-search-page-in-the-wizard"></a>4 - 略過精靈的 [新增認知搜尋] 頁面

您並不需要新增認知技術就能匯入 JSON 文件。 除非您有特殊需要，而必須將[認知服務 API 和轉換納入](cognitive-search-concept-intro.md)編製索引管線，否則請略過此步驟。

若要略過此步驟，請按下一個頁面 [自訂目標索引]。

### <a name="5---set-index-attributes"></a>5 - 設定索引屬性

在 [索引] 頁面上，您應該會看到欄位清單，其中有資料類型以及一系列用於設定索引屬性的核取方塊。 精靈可以根據中繼資料以及藉由對來源資料取樣來產生預設索引。 

預設值通常會產生可行的解決方案：選取某個欄位 (轉換為字串) 來作為索引鍵或文件識別碼以便唯一識別每個文件，並選取適合作為全文檢索搜尋候選項目並可在結果集內擷取的欄位。 若為 Blob，`content` 是最適合作為可搜尋內容候選項目的欄位。

您可以接受預設值，也可以檢閱[索引屬性](https://docs.microsoft.com/rest/api/searchservice/create-index#bkmk_indexAttrib)和[語言分析器](https://docs.microsoft.com/rest/api/searchservice/language-support)的描述來覆寫或補充初始值。 

請花一點時間檢閱您的選擇。 一旦執行精靈，就會建立實體的資料結構，而且除非您捨棄並重新建立所有物件，否則將無法編輯這些欄位。

   ![Blob 索引定義](media/search-howto-index-json/import-wizard-json-index.png)

### <a name="6---create-indexer"></a>6 - 建立索引子

若完整指定，精靈會在搜尋服務中建立三個不同的物件。 資料來源物件和索引物件會在 Azure 搜尋服務中儲存為具名資源。 最後一個步驟則會建立索引子物件。 為索引子命名可讓索引子以獨立資源的形式存在，索引子可以獨立地排程及管理，而不會受制於索引和資料來源物件 (這兩個物件會在相同的精靈序列中建立)。

如果您不熟悉索引子，「索引子」是 Azure 搜尋服務中的資源，其會搜耙外部資料來源中的可搜尋內容。 **匯入資料**精靈的輸出是一個索引子，其會搜耙 JSON 資料來源、擷取可搜尋的內容，並將該內容匯入到 Azure 搜尋服務上的索引。

   ![Blob 索引子定義](media/search-howto-index-json/import-wizard-json-indexer.png)

按一下 [確定] 來執行精靈並建立所有物件。 編製索引的程序會立即開始。

您可以在入口網站的頁面中監視資料匯入過程。 進度通知會指出編製索引的狀態，以及所上傳的文件數量。 編製索引的程序完成後，您可以使用[搜尋總管](search-explorer.md)來查詢索引。

<a name="json-indexer-rest"></a>

## <a name="use-rest-apis"></a>使用 REST API

為 JSON Blob 編製索引的程序，與 Azure 搜尋服務中所有索引子通用的三步驟工作流程中的一般文件擷取程序類似。

針對程式碼型 JSON 的編製索引，您可以使用 REST API 來搭配[索引子](https://docs.microsoft.com/rest/api/searchservice/create-indexer)、[資料來源](https://docs.microsoft.com/rest/api/searchservice/create-data-source)和[索引](https://docs.microsoft.com/rest/api/searchservice/create-index)的 API。 相較於入口網站的精靈，如果是利用程式碼的方法，您需要備有索引，且索引必須已準備好可在您傳送**建立索引子**要求時接受 JSON 文件。

Azure Blob 儲存體中的 JSON blob 通常是單一 JSON 文件或 JSON 陣列。 Azure 搜尋服務中的 Blob 索引子可以剖析其中一種結構，取決於您如何設定在要求上的 **parsingMode** 參數。

| JSON 文件 | parsingMode | 說明 | 可用性 |
|--------------|-------------|--------------|--------------|
| 一個 blob 一個 | `json` | 將 JSON blob 當作單一文字區塊來剖析。 每一個 JSON blob 會變成單一 Azure 搜尋服務文件。 | 通常可以在 [REST](https://docs.microsoft.com/rest/api/searchservice/indexer-operations) 和 [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer) API 中使用。 |
| 一個 blob 多個 | `jsonArray` | 剖析 blob 中的 JSON 陣列，陣列的每個元素會變成不同的 Azure 搜尋服務文件。  | 通常可以在 [REST](https://docs.microsoft.com/rest/api/searchservice/indexer-operations) 和 [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer) API 中使用。 |


### <a name="step-1-create-a-data-source"></a>步驟 1：建立資料來源

第一個步驟是提供索引子使用的資料來源連線資訊。 資料來源類型 (在此指定為 `azureblob`) 決定了索引子會叫用哪一些資料擷取行為。 針對編製 JSON blob 索引，JSON 文件和陣列的資料來源定義是相同的。 

    POST https://[service name].search.windows.net/datasources?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "optional, my-folder" }
    }   

### <a name="step-2-create-a-target-search-index"></a>步驟 2：建立目標搜尋索引 

索引子要搭配索引結構描述。 如果您使用 API (而不是入口網站)，請事先準備好索引，以便在索引子作業中指定它。

索引會將可搜尋的內容儲存在 Azure 搜尋服務中。 若要建立索引，請提供結構描述來指定文件中的欄位、屬性和其他可形塑搜尋體驗的建構。 如果您建立的索引具有與來源相同的欄位名稱和資料類型，索引子便會比對來源和目的地的欄位，讓您不必明確地對應欄位。

下列範例說明[建立索引](https://docs.microsoft.com/rest/api/searchservice/create-index)要求。 索引會有可搜尋的 `content` 欄位，以供儲存從 Blob 擷取到的文字︰   

    POST https://[service name].search.windows.net/indexes?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
          ]
    }


### <a name="step-3-configure-and-run-the-indexer"></a>步驟 3：設定和執行索引子

到目前為止，資料來源和索引的定義都符合不限定 parsingMode 的情況。 不過，在索引子設定的第 3 個步驟，做法將分道揚鑣，取決於您在 Azure 搜尋服務索引中如何剖析及結構化 JSON blob 內容。 選項包括 `json` 或 `jsonArray`：

+ 將 **parsingMode** 設定為 `json` 可將每個 Blob 當作單一文件來編製索引。

+ 將 **parsingMode** 設定為 `jsonArray` 則可使用由 JSON 陣列構成的 Blob，但您需要讓陣列的每個元素都變成 Azure 搜尋服務中的個別文件。 您可以將文件視為搜尋結果中的單一項目。 如果您希望陣列中的每個元素在搜尋結果中皆顯示為獨立項目，則請使用 `jsonArray` 選項。

在索引子定義中，您可以選擇性地使用**欄位對應**，以選擇用來填入目標搜尋索引的來源 JSON 文件屬性。 若採用 JSON 陣列，如果陣列為較低層級屬性，您可以設定文件根目錄，指出陣列放在 blob 中的位置。

> [!IMPORTANT]
> 當您使用 `json` 或 `jsonArray` 剖析模式時，Azure 搜尋服務會假設資料來源中的所有 blob 都包含 JSON。 如果您需要支援在相同的資料來源中混用 JSON 和非 JSON Blob，請在 [UserVoice 網站](https://feedback.azure.com/forums/263029-azure-search)上讓我們知道。


### <a name="how-to-parse-single-json-blobs"></a>如何剖析單一 JSON blob

根據預設， [Azure 搜尋服務 Blob 索引子](search-howto-indexing-azure-blob-storage.md) 會將 JSON Blob 剖析為單一的文字區塊。 通常，您會想要保留 JSON 文件的結構。 例如，假設您在 Azure Blob 儲存體中有下列 JSON 文件：

    {
        "article" : {
            "text" : "A hopefully useful article explaining how to parse JSON blobs",
            "datePublished" : "2016-04-13",
            "tags" : [ "search", "storage", "howto" ]    
        }
    }

Blob 索引子會將 JSON 文件剖析成單一的 Azure 搜尋服務文件。 索引子會比對來源中的 "text"、"datePublished"、"tags"，找出與目標索引欄位具有相同名稱和類型的索引，然後載入索引。

設定則在索引子作業的主體中提供。 前文提過預先定義的資料來源物件，，會指定資料來源類型和連線資訊。 此外，目標索引在您的服務中也必須是空的容器。 排程和參數為選擇性，如果省略它們，索引子會立即執行，使用 `json` 作為剖析模式。

完整指定的要求看起來如下：

    POST https://[service name].search.windows.net/indexers?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "json" } }
    }

如前所述，不一定要使用欄位對應。 指定索引的 "text"、"datePublished"、"tags" 欄位，blob 索引子可以推斷正確的對應，故要求中不需要欄位對應。

### <a name="how-to-parse-json-arrays"></a>如何剖析 JSON 陣列

或者，您可以選擇 JSON 陣列功能。 當 blob 包含「JSON 物件的陣列」，且您希望陣列的每個元素成為個別的 Azure 搜尋服務文件，這個功能特別實用。 以下列 JSON blob 為例，您可以將 Azure 搜尋服務索引填入三個不同的文件，每個都具有 "id" 和 "text" 欄位。  

    [
        { "id" : "1", "text" : "example 1" },
        { "id" : "2", "text" : "example 2" },
        { "id" : "3", "text" : "example 3" }
    ]

若為 JSON 陣列，索引子定義看起來應該像下列範例。 請注意，parsingMode 參數會指定 `jsonArray` 剖析器。 指定正確的剖析器並輸入正確的資料，是為 JSON Blob 編製索引時的唯二陣列特有需求。

    POST https://[service name].search.windows.net/indexers?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "jsonArray" } }
    }

同樣地，請注意您可以省略欄位對應。 採用 "id" 和 "text" 欄位名稱相同的索引，Blob 索引子便可以推斷正確的對應，而不需要明確的欄位對應清單。

<a name="nested-json-arrays"></a>

### <a name="nested-json-arrays"></a>巢狀的 JSON 陣列
如果您想要為 JSON 物件陣列編製索引，但是該陣列以巢狀方式位於文件中的某處？ 您可以使用 `documentRoot` 組態屬性選擇哪一個屬性包含陣列。 例如，如果您的 Blob 看起來像這樣︰

    {
        "level1" : {
            "level2" : [
                { "id" : "1", "text" : "Use the documentRoot property" },
                { "id" : "2", "text" : "to pluck the array you want to index" },
                { "id" : "3", "text" : "even if it's nested inside the document" }  
            ]
        }
    }

使用這個設定來索引 `level2` 屬性中包含的陣列：

    {
        "name" : "my-json-array-indexer",
        ... other indexer properties
        "parameters" : { "configuration" : { "parsingMode" : "jsonArray", "documentRoot" : "/level1/level2" } }
    }

### <a name="using-field-mappings-to-build-search-documents"></a>使用欄位對應建置搜尋文件

當來源和目標的欄位不能完全對齊時，您可以在要求主體中定義欄位對應區段，明確指定欄位對欄位的關聯。

目前，Azure 搜尋服務無法直接編製索引任意 JSON 文件，因為它只支援基本資料類型、字串陣列和 GeoJSON 點。 不過，您可以使用 **欄位對應** 挑選 JSON 文件的幾個部分，並將它們「上移」到搜尋文件的最上層欄位。 如需了解欄位對應的基本知識，請參閱 [Azure 搜尋服務索引子中的欄位對應](search-indexer-field-mappings.md)。

回到我們的範例 JSON 文件︰

    {
        "article" : {
            "text" : "A hopefully useful article explaining how to parse JSON blobs",
            "datePublished" : "2016-04-13"
            "tags" : [ "search", "storage", "howto" ]    
        }
    }

假設搜尋索引有下列欄位︰`Edm.String` 類型的 `text`、`Edm.DateTimeOffset` 類型的 `date`、`Collection(Edm.String)` 類型的 `tags`。 請注意來源中的 "datePublished" 與索引中的 `date` 欄位之間的差異。 若要將 JSON 對應到所需形狀，請使用下列欄位對應︰

    "fieldMappings" : [
        { "sourceFieldName" : "/article/text", "targetFieldName" : "text" },
        { "sourceFieldName" : "/article/datePublished", "targetFieldName" : "date" },
        { "sourceFieldName" : "/article/tags", "targetFieldName" : "tags" }
      ]

對應中的來源欄位名稱是使用 [JSON 指標](https://tools.ietf.org/html/rfc6901) 標記法指定。 以正斜線開始參考 JSON 文件的根目錄，然後使用正斜線分隔的路徑挑選所需的屬性 (使用任意層級的巢狀結構)。

您也可以使用以零為起始的索引來參考個別陣列元素。 比方說，若要從上述範例挑選 "tags" 陣列的第一個元素，請如下所示使用欄位對應︰

    { "sourceFieldName" : "/article/tags/0", "targetFieldName" : "firstTag" }

> [!NOTE]
> 如果欄位對應路徑中的來源欄位名稱參考在 JSON 中不存在的屬性，則會略過該對應且不會產生錯誤。 這麼做是為了讓我們可支援具有不同結構描述 (這是常見的使用案例) 的文件。 因為沒有任何驗證，您必須小心避免在欄位對應規格中出現錯字。
>
>

### <a name="rest-example-indexer-request-with-field-mappings"></a>REST 範例：索引子要求搭配欄位對應

下列範例是完整指定的索引子內容，包括欄位對應：

    POST https://[service name].search.windows.net/indexers?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "json" } },
      "fieldMappings" : [
        { "sourceFieldName" : "/article/text", "targetFieldName" : "text" },
        { "sourceFieldName" : "/article/datePublished", "targetFieldName" : "date" },
        { "sourceFieldName" : "/article/tags", "targetFieldName" : "tags" }
        ]
    }

<a name="json-indexer-dotnet"></a>

## <a name="use-net-sdk"></a>使用 .NET SDK

.NET SDK 與 REST API 完全對等。 建議您檢閱先前的 REST API 章節，以了解其概念、工作流程和需求。 然後，您可以參閱下列 .NET API 參考文件，以在受控程式碼中實作 JSON 索引子。

+ [microsoft.azure.search.models.datasource](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasource?view=azure-dotnet)
+ [microsoft.azure.search.models.datasourcetype](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasourcetype?view=azure-dotnet) 
+ [microsoft.azure.search.models.index](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index?view=azure-dotnet) 
+ [microsoft.azure.search.models.indexer](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer?view=azure-dotnet)

## <a name="see-also"></a>另請參閱

+ [Azure 搜尋服務中的索引子](search-indexer-overview.md)
+ [使用 Azure 搜尋服務編製 Azure Blob 儲存體的索引](search-howto-index-json-blobs.md)
+ [使用 Azure 搜尋服務 blob 索引子編製 CSV blob 的索引](search-howto-index-csv-blobs.md)
+ [教學課程：搜尋 Azure Blob 儲存體中的半結構化資料](search-semi-structured-data.md)
