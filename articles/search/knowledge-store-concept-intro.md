---
title: 知識存放區簡介和概觀 (預覽) - Azure 搜尋服務
description: 將擴充的文件傳送到 Azure 搜尋服務，您可以從該處檢視、調整及取用 Azure 搜尋服務和其他應用程式中的擴充文件。
manager: cgronlun
author: HeidiSteen
services: search
ms.service: search
ms.devlang: NA
ms.topic: overview
ms.date: 05/02/2019
ms.author: heidist
ms.openlocfilehash: 4a27e4d8f2fbaafe6d27a3e3cabd31aa715b9d80
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/11/2019
ms.locfileid: "65540741"
---
# <a name="what-is-knowledge-store-in-azure-search"></a>什麼是 Azure 搜尋服務中的知識存放區？

> [!Note]
> 知識存放區處於預覽狀態，不適合用於生產環境。 [REST API 版本 2019-05-06-Preview](search-api-preview.md) 提供此功能。 目前 .NET SDK 入口網站並不支援此功能。
>

知識存放區是 Azure 搜尋服務的一個選用功能，可儲存以 AI 為基礎的索引管線 [(認知搜尋)](cognitive-search-concept-intro.md) 所建立的擴充文件與中繼資料。 知識存放區是由您設定為管線之一部分的 Azure 儲存體帳戶提供支援。 啟用時，搜尋服務會使用此儲存體帳戶快取每個擴充文件的表示法。 

如果您過去使用過認知搜尋，您已經知道該技能集可用來透過一連串的擴充移動文件。 結果可能是 Azure 搜尋服務索引，或 (此預覽版中的新功能) 知識存放區中的投影。

投影是一種機制，可建構資料以供下游應用程式取用。 您可以使用針對 Azure 儲存體或連線至 Azure 儲存體的任何應用程式建置的 [[儲存體總管](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows)]，為取用擴充的文件開闢了新的可能性。 部分範例包括資料科學管線和自訂分析。

![管線圖中的知識儲存區](./media/knowledge-store-concept-intro/annotationstore_sans_internalcache.png "管線圖中的知識存放區")

若要使用知識存放區，請將 `knowledgeStore` 元素新增至索引管線中逐步定義作業的技能集。 在執行期間，Azure 搜尋服務會在 Azure 儲存體帳戶中建立一個空間，並填入管線建立的定義和內容。

## <a name="benefits-of-knowledge-store"></a>知識存放區的優點

知識存放區可從非結構化及半結構化資料檔案提供您結構、情境和實際的內容，如 Blob、經過分析的映像檔，甚至是重整成新格式的結構化資料。 在此預覽版本的[逐步解說](knowledge-store-howto.md)中，您可以率先看到密集的 JSON 文件如何分割成子結構，重新組成新的結構，並提供給下游處理程序 (例如機器學習服務和資料科學工作負載) 使用。

雖然在查看以 AI 為基礎的索引管線可以產生的內容方面很實用，但是知識存放區的真正威力在於能夠重新調整資料。 您可以從基本的技能集開始，然後逐一查看以便逐漸新增結構層級，然後再結合到新的結構中，就可在 Azure 搜尋服務以外的其他應用程式中取用。

列舉的知識存放區優點包括：

+ 使用搜尋以外的[分析和報告工具](#tools-and-apps)取用擴充文件。 Power BI 搭配 Power Query 是相當具吸引力的選擇，但可以連線到 Azure 儲存體的任何工具或應用程式都能夠從您所建立的知識存放區提取。

+ 為步驟和技能集定義偵錯的同時，精簡 AI 索引管線。 知識存放區會在 AI 索引管線中為您顯示技能集定義的產品。 您可以使用這些結果來設計更好的技能集，因為您可以看到擴充實際的樣子。 您可以使用 Azure 儲存體中的 [[儲存體總管](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows)]，檢視知識存放區的內容。

+ 將資料塑造成新的形式。 重整是在技能集中編寫的，但重點是技能集現在可以提供這項功能。 Azure 搜尋服務中的[塑形器技能](cognitive-search-skill-shaper.md)已經擴充，可因應這項工作。 重整可讓您定義與資料預定用途相符的投影，同時保留關聯性。

> [!Note]
> 不熟悉使用認知服務的以 AI 為基礎的索引？ Azure 搜尋服務會與認知服務視覺和語言功能整合在一起，以便使用光學字元辨識 (OCR)，透過影像檔、實體辨識以及文字檔的關鍵片語擷取等等，擷取並豐富來源資料。 如需詳細資訊，請參閱[何謂認知搜尋？](cognitive-search-concept-intro.md)。

## <a name="create-a-knowledge-store"></a>建立知識存放區

知識存放區是技能集定義的一部分。 在此預覽版本中，建立知識存放區需要有 REST API，並在入口網站中使用 `api-version=2019-05-06-Preview` 或[匯入資料]  精靈。

下列 JSON 會指定 `knowledgeStore`，這是技能集的一部分，由索引子所叫用 (未顯示)。 `knowledgeStore` 中的投影規格可判斷在 Azure 儲存體中建立的是資料表還是物件。

如果您已經熟悉以 AI 為基礎的索引，則技能集定義會決定每個擴充文件的建立、組織和本質。

```json
{
  "name": "my-new-skillset",
  "description": 
  "Example showing knowledgeStore placement, supported in api-version=2019-05-06-Preview. You need at least one skill, most likely a Shaper skill if you are modulating data structures.",
  "skills":
  [
    {
    "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
    "context": "/document/content/phrases/*",
    "inputs": [
        {
        "name": "text",
        "source": "/document/content/phrases/*"
        },
        {
        "name": "sentiment",
        "source": "/document/content/phrases/*/sentiment"
        }
    ],
    "outputs": [
        {
        "name": "output",
        "targetName": "analyzedText"
        }
    ]
    },
  ],
  "cognitiveServices": 
    {
    "@odata.type": "#Microsoft.Azure.Search.CognitiveServicesByKey",
    "description": "mycogsvcs resource in West US 2",
    "key": "<your key goes here>"
    },
  "knowledgeStore": { 
    "storageConnectionString": "<your connection string goes here>", 
    "projections": [ 
        { 
            "tables": [  
            { "tableName": "Reviews", "generatedKeyName": "ReviewId", "source": "/document/Review" , "sourceContext": null, "inputs": []}, 
            { "tableName": "Sentences", "generatedKeyName": "SentenceId", "source": "/document/Review/Sentences/*", "sourceContext": null, "inputs": []}, 
            { "tableName": "KeyPhrases", "generatedKeyName": "KeyPhraseId", "source": "/document/Review/Sentences/*/KeyPhrases", "sourceContext": null, "inputs": []}, 
            { "tableName": "Entities", "generatedKeyName": "EntityId", "source": "/document/Review/Sentences/*/Entities/*" ,"sourceContext": null, "inputs": []} 

            ], 
            "objects": [ 
                { 
                "storageContainer": "Reviews", 
                "format": "json", 
                "source": "/document/Review", 
                "key": "/document/Review/Id" 
                } 
            ]      
        }    
    ]     
    } 
}
```

## <a name="components-backing-a-knowledge-store"></a>備份知識存放區的元件

若要建立知識存放區，您需要下列服務和成品。

### <a name="1---source-data"></a>1 - 來源資料

您想要擴充的資料或文件必須存在於 Azure 搜尋服務索引子支援的 Azure 資料來源： 

* [Azure SQL](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)

* [Azure Cosmos DB](search-howto-index-cosmosdb.md)

* [Azure Blob 儲存體](search-howto-indexing-azure-blob-storage.md)

[Azure 資料表儲存體](search-howto-indexing-azure-tables.md)可用於知識存放區中的輸出資料，但無法當作以 AI 為基礎之索引管線的輸入資料資源使用。

### <a name="2---azure-search-service"></a>2 - Azure 搜尋服務

您也需要 Azure 搜尋服務和 REST API 來建立及設定用於資料擴充的物件。 建立知識存放區的 REST API 為 `api-version=2019-05-06-Preview`。

Azure 搜尋服務可提供索引子功能，而索引子用來端對端地驅動整個程序，進而導致在 Azure 儲存體中產生永續性的擴充文件。 索引子可使用資料來源、索引和技能集，這些全部都是建立和填入知識存放區所必需。

| Object | REST API | 說明 |
|--------|----------|-------------|
| 資料來源 | [建立資料來源](https://docs.microsoft.com/rest/api/searchservice/create-data-source)  | 一個資源，用以識別提供來源資料以建立擴充文件的外部 Azure 資料來源。  |
| 技能集 | [建立技能集 (api-version=2019-05-06)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)  | 一個資源，在索引編製期間負責對擴充管線中使用的[內建技能](cognitive-search-predefined-skills.md)和[自訂認知技能](cognitive-search-custom-skill-interface.md)協調用法。 |
| index | [建立索引](https://docs.microsoft.com/rest/api/searchservice/create-index)  | 表示 Azure 搜尋服務索引的結構描述。 索引中與來源資料中的欄位或在擴充階段產生的欄位 (例如，實體辨識所建立之組織名稱的欄位) 相對應的欄位。 |
| 索引子 | [建立索引子 (api-version=2019-05-06)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)  | 一項資源，用以定義在索引編製期間所使用的元件：包括資料來源、技能集、來源和中繼資料結構與目標索引的欄位關聯性，以及索引本身。 執行索引子是擷取和擴充資料的觸發程序。 輸出是以索引結構描述為基礎、以來源資料填入，並透過技能集進行擴充的搜尋索引。  |

### <a name="3---cognitive-services"></a>3 - 認知服務

技能集內指定的擴充是以認知服務中的電腦視覺和語言功能為基礎。 系統會在索引編製期間，透過技能集使用認知服務功能。 技能集是技能的組合，而技能則會繫結至特定的電腦視覺和語言功能。 若要整合認知服務，您將會[附加認知服務資源](cognitive-search-attach-cognitive-services.md)到技能集。

### <a name="4---storage-account"></a>4 - 儲存體帳戶

在您的 Azure 儲存體帳戶底下，Azure 搜尋服務會建立一個 Blob 容器或資料表，端視您設定技能集的方式而定。 如果您的資料是來自 Azure Blob 或資料表儲存體，則表示您已經設定。 否則，您將需要建立一個 Azure 儲存體帳戶。 Azure 儲存體中的資料表和物件包含以 AI 為基礎的索引管線所建立的擴充文件。

儲存體帳戶是在技能集中指定的。 在 `api-version=2019-05-06-Preview` 中，技能集定義包含知識存放區定義，讓您可以提供帳戶資訊。

<a name="tools-and-apps"></a>

### <a name="5---access-and-consume"></a>5 - 存取和取用

一旦擴充存在於儲存體之後，連線到 Azure Blob 或資料表儲存體的任何工具或技術都可以用來探索、分析或取用內容。 下列清單是一個起點：

+ [儲存體總管](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows)，可檢視擴充的文件結構及內容。 您可以將此視為基準工具來檢視知識存放區內容。

+ [Power BI 搭配 Power Query](https://support.office.com/article/connect-to-microsoft-azure-blob-storage-power-query-f8165faa-4589-47b1-86b6-7015b330d13e)，用於自然語言查詢，如果您有數值資料，則使用報告和分析工具。

+ [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/)，用於進一步操作。

+ Azure 搜尋服務索引，可使用[認知搜尋](cognitive-search-concept-intro.md)，對您已經編製索引的內容進行全文檢索搜尋。

## <a name="document-persistence"></a>文件持續性

在儲存體帳戶中，可以將擴充表示為 Azure 資料表儲存體中的資料表，或 Azure Blob 儲存體中的物件。 請記住，擴充一旦儲存之後，就可以當作來源使用，將資料載入到其他資料庫和工具，

+ 當您想要以表格式格式表示結構描述感知的資料時，資料表儲存體很實用。 如果您想要以新方式重整或重新組合元素，資料表儲存體可提供您所需的細微性。

+ Blob 儲存體會針對每個文件建立一個全部包含 JSON 表示法。 您可以在一個技能集中使用兩個儲存體選項，以取得各式各樣的運算式。

+ Azure 搜尋服務會在索引中保存內容。 如果您的案例與搜尋無關 (例如，如果您的目標是在另一個工具中分析)，您可以刪除管線所建立的索引。 但是您也可以保留索引，並使用 [[搜尋總管](search-explorer.md)] 之類的內建工具作為第三個媒體 ([儲存體總管] 和分析應用程式之外)，以便與內容互動。

除了文件內容之外，擴充的文件還包含產生擴充之技能集版本的中繼資料。  

## <a name="inside-a-knowledge-store"></a>在知識存放區內

知識存放區是由註釋快取和投影所組成。 服務會在內部使用*快取*，來快取技能的結果並追蹤變更。 「投影」  可定義符合預定用途之擴充的結構描述和結構。 每個知識存放區都有一個快取，但是有多個投影。 

快取一律是 Blob 容器，但是投影可以清楚表達為資料表或物件：

+ 若是物件，投影會對應至 Blob 儲存體，其中投影會儲存到容器中，在諸如資料科學管線的案例中，JSON 的容器內會有物件或階層式表示法。

+ 若是資料表，投影則會對應至資料表儲存體。 表格式表示法會保留資料分析或匯出等案例的關聯性，作為機器學習的資料框架。 擴充的投影之後就可以輕鬆地匯入到其他資料存放區中。 

您可以在一個知識存放區中建立多個投影，以便在組織中容納各種組成單位。 雖然資料科學家或分析師可能需要由您的技能集所組合的細微或模組化資料結構，但是開發人員可能需要存取擴充文件的完整 JSON 表示法。

例如，如果其中一個擴充程序的目標是也要建立用來定型模型的資料集，將資料投射至物件存放區將會是一種在資料科學管線中使用資料的方法。 或者，如果您想要根據擴充的文件建立快速 Power BI 儀表板，表格式投影將非常適合。

<!---
## Data lifecycle and billing

Each time you run the indexer, the cache in Azure storage is updated if the skillset definition or underlying source data has changed. As input documents are edited or deleted, changes are propagated through the annotation cache to the projections, ensuring that your projected data is a current representation of your inputs at the end of the indexer run. 

Generally speaking, pipeline processing can be an all-or-nothing operation, but Azure Search can process incremental changes, which saves you time and money.

If a document is new or updated, all skills are run. If only the skillset changes, reprocessing is scoped to just those skills and documents affected by your edit.

### Changes to a skillset
Suppose that you have a pipeline composed of multiple skills, operating over a large body of static data (for example, scanned documents), that takes 8 hours and costs $200 to create the knowledge store. Now suppose you need to tweak one of the skills in the skillset. Rather than starting over, Azure Search can determine which skill is affected, and reprocess only that skill. Cached data and projections that are unaffected by the change remain intact in the knowledge store.

### Changes in the data
Scenarios can vary considerably, but let's suppose instead of static data, you have volatile data that changes between indexer invocations. Given no changes to the skillset, you are charged for processing the delta of new and modified document. The timestamp information varies by data source, but for illustration, in a Blob container, Azure Search looks at the `lastmodified` date to determine which blobs need to be ingested.

> [!Note]
> While you can edit the data in the projections, any edits will be overwritten on the next pipeline invocation, assuming the document in source data is updated. 

### Deletions

Although Azure Search creates and updates structures and content in Azure storage, it does not delete them. Projections and cached documents continue to exist even when the skillset is deleted. As the owner of the storage account, you should delete a projection if it is no longer needed. 

### Tips for development

+ Start small with a representative sample of your data as you make significant changes to skillset composition. As your design finalizes, you can slowly add more data during later-stage development, and then roll in the entire data set when you are comfortable with the pipeline composition.

+ Retain control over indexer invocation. Indexers can run on a schedule, which is helpful for solutions that are rolled into production, but less helpful if you are actively developing your pipeline. During development, avoid schedules so that you don’t lose track of cache or projection state. Once your solution is in production and skillset composition is static, you can put the indexer on a schedule to pick up routine changes in the external source data. 

-->

## <a name="where-do-i-start"></a>我該從哪裡開始？

基於學習目的，我們會建議使用免費服務，但請注意，可用的交易數目限制為每天每個訂用帳戶 20 份文件。

使用多個服務時，請在相同的區域建立所有的服務，以獲得最佳效能，並將成本降至最低。 對於移至相同區域中另一個服務的輸入資料或輸出資料的頻寬，將不會向您收取任何費用。

**步驟 1：[建立 Azure 搜尋服務索引](search-create-service-portal.md)** 

**步驟 2：[建立 Azure 儲存體帳戶](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal)** 

**步驟 3：[建立認知服務資源](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)** 

**步驟 4：[開始使用入口網站](cognitive-search-quickstart-blob.md) - 或 - [開始使用 REST 和 Postman 的範例資料](knowledge-store-howto.md)** 

您可以使用 REST `api-version=2019-05-06-Preview` 來建構以 AI 為基礎的管線，其中包含知識存放區。 在最新預覽版 API 中，技能集物件會提供 `knowledgeStore` 定義。

## <a name="takeaways"></a>重要心得

知識存放區提供各式各樣的優點，包括但不限於在搜尋以外的案例中啟用擴充文件、成本控制，以及管理擴充程序中的漂移。 這些功能都可供使用，只要將儲存體帳戶新增至您的技能集，然後使用更新的運算式語言，如[如何開始使用知識存放區](knowledge-store-howto.md)中所述。 

## <a name="next-steps"></a>後續步驟

建立擴充文件最簡單的方法，是透過 [匯入資料]  精靈。

> [!div class="nextstepaction"]
> [快速入門：在入口網站中逐步試用認知搜尋](cognitive-search-quickstart-blob.md)
