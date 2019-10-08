---
title: 使用 REST 建立知識存放區 - Azure 搜尋服務
description: 使用 REST API 和 Postman 建立 Azure 搜尋服務知識存放區，以保存來自認知搜尋管線的擴充。
author: lobrien
services: search
ms.service: search
ms.subservice: cognitive-search
ms.topic: tutorial
ms.date: 10/01/2019
ms.author: laobri
ms.openlocfilehash: e28fa919c4c656b9ceb1d34806c3ef08aec2df2c
ms.sourcegitcommit: a19f4b35a0123256e76f2789cd5083921ac73daf
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/02/2019
ms.locfileid: "71719928"
---
# <a name="create-an-azure-search-knowledge-store-using-rest"></a>使用 REST 建立 Azure 搜尋服務知識存放區

知識存放區是 Azure 搜尋服務中的一項功能，可保存 AI 擴充管線的輸出，以便進行後續分析或其他下游處理。 AI 擴充管線可接受影像檔案或非結構化文字檔案，使用 Azure 搜尋服務編製其所引，從認知服務 (例如影像分析和自然語言處理) 套用 AI 擴充，然後將結果儲存至 Azure 儲存體中的知識存放區。 您可以接著使用 Power BI 或儲存體總管之類的工具來探索知識存放區。

在本文中，您會使用 REST API 介面將 AI 擴充內嵌、編製索引及套用至一組旅館評論。 旅館評論會匯入 Azure Blog 儲存體中，而結果會儲存為 Azure 資料表儲存體中的知識存放區。

建立知識存放區之後，您可以了解如何使用[儲存體總管](knowledge-store-view-storage-explorer.md)或 [Power BI](knowledge-store-connect-power-bi.md) 存取此知識存放區。

## <a name="1---create-services"></a>1 - 建立服務

+ [建立 Azure 搜尋服務](search-create-service-portal.md)，或在您目前的訂用帳戶下方[尋找現有服務](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)。 您可以使用本教學課程的免費服務。

+ [建立 Azure 儲存體帳戶](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)以儲存範例資料和知識存放區。 您的儲存體帳戶必須針對您的 Azure 搜尋服務使用相同的位置 (例如美國西部)。 [儲存體類型]  必須是 [StorageV2 (一般用途 V2)]  (預設值) 或 [儲存體 (一般用途 V1)]  。

+ 建議使用：[Postman 傳統型應用程式](https://www.getpostman.com/)可將要求傳送至 Azure 搜尋服務。 您可以使用 REST API 搭配任何能夠處理 HTTP 要求和回應的工具。 Postman 是探索 REST API 的絕佳選擇，且將會在本文中使用。 此外，本文的[原始程式碼](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/knowledge-store/KnowledgeStore.postman_collection.json)包含 Postman 要求集合。 

## <a name="2---store-the-data"></a>2 - 儲存資料

將旅館評論 CSV 檔案載入 Azure Blob 儲存體中，以便 Azure 搜尋服務索引子存取該檔案，並透過 AI 擴充管線進行饋送。

### <a name="create-an-azure-blob-container-with-the-data"></a>建立含有此資料的 Azure Blob 容器

1. [下載儲存在 CSV 檔案 (HotelReviews_Free.csv) 中的旅館評論資料](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Free.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D)。 此資料源自 Kaggle.com 並包含客戶對於旅館的意見反應。
1. [登入 Azure 入口網站](https://portal.azure.com)，然後瀏覽至您的 Azure 儲存體帳戶。
1. [建立 Blob 容器](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)。 若要建立 Blob 容器，請在您儲存體帳戶的左側導覽列中，按一下 [Blob]  ，然後按一下命令列上的 [+ 容器]  。
1. 針對新的容器 [名稱]  輸入 `hotel-reviews`。
1. 選取任何 [公用存取層級]  。 我們使用預設值。
1. 按一下 [確定]  以建立 Azure Blob 容器。
1. 開啟新的 `hotels-review` 容器，按一下 [上傳]  ，然後選取您在第一個步驟中下載的 **HotelReviews-Free.csv** 檔案。

    ![上傳資料](media/knowledge-store-create-portal/upload-command-bar.png "上傳旅館評論")

1. 按一下 [上傳]  ，將 CSV 檔案匯入 Azure Blob 儲存體中。 新的容器隨即出現。

    ![建立 Azure Blob 容器](media/knowledge-store-create-portal/hotel-reviews-blob-container.png "建立 Azure Blob 容器")

## <a name="3---configure-postman"></a>3 - 設定 Postman

下載 [Postman 集合原始程式碼](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/knowledge-store/KnowledgeStore.postman_collection.json)，然後使用 **File, Import...** 將它匯入 Postman 中。切換至 [集合]  索引標籤並按一下 [...]  按鈕，然後選取 [編輯]  。 

![顯示導覽的 Postman 應用程式](media/knowledge-store-create-rest/postman-edit-menu.png "瀏覽至 Postman 中的 [編輯] 功能表")

在產生的 [編輯] 對話方塊中，瀏覽至 [變數]  索引標籤。 

[變數]  索引標籤可讓您新增 Postman 每次在雙括弧內發現時所將置換掉的值。 例如，Postman 會將符號 `{{admin-key}}` 取代為 `admin-key` 的「目前值」。 Postman 會在 URL、標頭、要求本文等項目中進行這項替代。 

您會在搜尋服務的 [金鑰]  索引標籤中找到 `admin-key` 的值。您必須將 `search-service-name` 和 `storage-account-name` 變更為您在[步驟 1](#1---create-services) 中選擇的值。 請從儲存體帳戶的 [存取金鑰]  索引標籤中的值設定 `storage-connection-string`。您可以讓其他值保持不變。

![Postman 應用程式變數索引標籤](media/knowledge-store-create-rest/postman-variables-window.png "Postman 的變數視窗")


| 變數    | 從哪裡取得 |
|-------------|-----------------|
| `admin-key` | 搜尋服務，**金鑰**索引標籤              |
| `api-version` | 保持為 "2019-05-06-Preview" |
| `datasource-name` | 保持為 "hotel-reviews-ds" | 
| `indexer-name` | 保持為 "hotel-reviews-ixr" | 
| `index-name` | 保持為 "hotel-reviews-ix" | 
| `search-service-name` | 搜尋服務，主要名稱。 URL 為 `https://{{search-service-name}}.search.windows.net` | 
| `skillset-name` | 保持為 "hotel-reviews-ss" | 
| `storage-account-name` | 儲存體帳戶，主要名稱 | 
| `storage-connection-string` | 儲存體帳戶，**存取金鑰**索引標籤，**key1** **連接字串** | 
| `storage-container-name` | 保持為 "hotel-reviews" | 

### <a name="review-the-request-collection-in-postman"></a>檢閱 Postman 中的要求集合

若要建立知識存放區，您必須發出四個 HTTP 要求： 

1. 建立索引的 PUT 要求。 此索引會保存 Azure 搜尋服務所使用和傳回的資料。
1. 建立資料來源的 POST 要求。 此資料來源會將您的 Azure 搜尋服務行為連線至資料和知識存放區的儲存體帳戶。 
1. 建立技能集的 PUT 要求。 技能集會指定套用至資料的擴充，以及知識存放區的結構。
1. 建立索引子的 PUT 要求。 執行索引子會讀取資料、套用技能集，並儲存結果。 您必須最後再執行此要求。

[原始程式碼](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/knowledge-store/KnowledgeStore.postman_collection.json)包含具有這四個要求的 Postman 集合。 若要發出要求，請在 Postman 中切換至要求的索引標籤，然後新增 `api-key` 和 `Content-Type` 要求標頭。 將 `api-key` 的值設定為 `{{admin-key}}`。 將 `Content-type` 值設定為 `application/json`。 

> [!div class="mx-imgBorder"]
> ![顯示 Postman 標頭介面的螢幕擷取畫面](media/knowledge-store-create-rest/postman-headers-ui.png)

> [!Note]
> 您將需要在所有要求中設定 `api-key` 和 `Content-type` 標頭。 如果 Postman 可辨識變數，則會以橙色文字呈現該變數，如同螢幕擷取畫面中的 `{{admin-key}}`。 如果變數拼錯，則會以紅色文字呈現。
>

## <a name="4---create-an-azure-search-index"></a>4 - 建立 Azure 搜尋服務索引

您必須建立 Azure 搜尋服務索引，以代表您想要搜尋、篩選及執行增強功能的資料。 您可以對 `https://{{search-service-name}}.search.windows.net/indexes/{{index-name}}?api-version={{api-version}}` 發出 PUT 要求以建立索引。 Postman 會將以雙括弧括住的符號 (例如 `{{search-service-name}}`、`{{index-name}}` 和 `{{api-version}}`) 取代為[步驟 3](#3---configure-postman) 中指定的值。 如果您使用其他工具來發出 REST 命令，則必須自行替換這些變數。

在要求本文中指定 Azure 搜尋服務索引的結構。 在 Postman 中，設定 `api-key` 和 `Content-type` 標頭之後，請切換至要求的 [本文]  窗格。 您應該會看到下列 JSON，如果沒有，請選擇 **Raw** 和 **JSON (application/json)** ，並貼上下列程式碼作為本文：

```JSON
{
    "name": "{{index-name}}",
    "fields": [
        { "name": "address", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "categories", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "city", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false },
        { "name": "country", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "latitude", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "longitude", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "name", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false },
        { "name": "postalCode", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "province", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "reviews_date", "type": "Edm.DateTimeOffset", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "reviews_dateAdded", "type": "Edm.DateTimeOffset", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "reviews_rating", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "reviews_text", "type": "Edm.String", "filterable": false,  "sortable": false, "facetable": false },
        { "name": "reviews_title", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "reviews_username", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "AzureSearch_DocumentKey", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false, "key": true },
        { "name": "metadata_storage_content_type", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "metadata_storage_size", "type": "Edm.Int64", "searchable": false, "filterable": false, "sortable": false, "facetable": false},
        { "name": "metadata_storage_last_modified", "type": "Edm.DateTimeOffset", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "metadata_storage_name", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "metadata_storage_path", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "Sentiment", "type": "Collection(Edm.Double)", "searchable": false, "filterable": true, "retrievable": true, "sortable": false, "facetable": true },
        { "name": "Language", "type": "Edm.String", "filterable": true, "sortable": false, "facetable": true },
        { "name": "Keyphrases", "type": "Collection(Edm.String)", "filterable": true, "sortable": false, "facetable": true }
    ]
}

```

您會看到此索引定義是由您想要呈現給使用者的資料 (飯店的名稱、評論內容、日期等等)、搜尋中繼資料，以及 AI 增強資料 (情感、Keyphrase 和語言) 所組合而成。

按 [傳送]  按鈕以發出 PUT 要求。 您應該會收到 `201 - Created` 的狀態訊息。 如果您收到不同的狀態，[本文]  窗格將會顯示 JSON 回應，並出現錯誤訊息。 

## <a name="5---create-the-datasource"></a>5 - 建立資料來源

現在，您必須將 Azure 搜尋服務連線至您在[步驟 2](#2---store-the-data) 中儲存的旅館資料。 建立資料來源的作業會藉由將 POST 傳送至 `https://{{search-service-name}}.search.windows.net/datasources?api-version={{api-version}}` 來完成。 同樣地，您必須將 `api-key` 和 `Content-Type` 標頭設定為先前所指定的值。 

在 Postman 中，開啟 [建立資料來源] 要求。 切換至 [本文]  窗格，其中應該會包含下列程式碼：

```json
{
  "name" : "{{datasource-name}}",
  "description" : "Demo files to demonstrate knowledge store capabilities.",
  "type" : "azureblob",
  "credentials" : { "connectionString" : "{{storage-connection-string}}" },
  "container" : { "name" : "{{storage-container-name}}" }
}
```

按 [傳送]  按鈕以發出 POST 要求。 

## <a name="6---create-the-skillset"></a>6 - 建立技能集 

下一步是指定技能集，也就是指定要套用的增強功能，以及要用來儲存結果的知識存放區。 在 Postman 中，開啟 [建立技能集] 索引標籤。此要求會將 PUT 傳送至 `https://{{search-service-name}}.search.windows.net/skillsets/{{skillset-name}}?api-version={{api-version}}`。
依照先前的相同方式，設定 `api-key` 和 `Content-type` 標頭。 

有兩個大型的最上層物件：`"skills"` 和 `"knowledgeStore"`。 `"skills"` 物件中的每個物件都是擴充服務。 每個擴充服務都有 `"inputs"` 和 `"outputs"`。 請留意 `LanguageDetectionSkill` 如何取得 `"Language"` 的輸出 `targetName`。 大部分的其他技能都會使用此節點的值作為輸入，而來源為 `document/Language`。 這種將一個節點的輸出當作另一個節點的輸入使用的功能，在 `ShaperSkill` 中更為明顯，這會指定資料將如何流入知識存放區的資料表中。

`"knowledge_store"` 物件會透過 `{{storage-connection-string}}` Postman 變數連線至儲存體帳戶。 然後，它會包含增強式文件與資料表和資料行之間的一組對應，且這組對應將可在知識存放區本身使用。 

若要產生技能集，請按 Postman 中的 [傳送]  按鈕來推送要求。

```json
{
    "name": "{{skillset-name}}",
    "description": "Skillset to detect language, extract key phrases, and detect sentiment",
    "skills": [ 
        {
            "@odata.type": "#Microsoft.Skills.Text.SplitSkill", 
            "context": "/document/reviews_text", "textSplitMode": "pages", "maximumPageLength": 5000,
            "inputs": [ 
                { "name": "text", "source": "/document/reviews_text" },
                { "name": "languageCode", "source": "/document/Language" }
            ],
            "outputs": [
                { "name": "textItems", "targetName": "pages" }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.SentimentSkill",
            "context": "/document/reviews_text/pages/*",
            "inputs": [
                { "name": "text", "source": "/document/reviews_text/pages/*" },
                { "name": "languageCode", "source": "/document/Language" }
            ],
            "outputs": [
                { "name": "score", "targetName": "Sentiment" }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.LanguageDetectionSkill",
            "context": "/document",
            "inputs": [
                { "name": "text", "source": "/document/reviews_text" }
            ],
            "outputs": [
                { "name": "languageCode", "targetName": "Language" }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.KeyPhraseExtractionSkill",
            "context": "/document/reviews_text/pages/*",
            "inputs": [
                { "name": "text",  "source": "/document/reviews_text/pages/*" },
                { "name": "languageCode",  "source": "/document/Language" }
            ],
            "outputs": [
                { "name": "keyPhrases" , "targetName": "Keyphrases" }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
            "context": "/document",
            "inputs": [
                { "name": "name",  "source": "/document/name" },
                { "name": "reviews_date",  "source": "/document/reviews_date" },
                { "name": "reviews_rating",  "source": "/document/reviews_rating" },
                { "name": "reviews_text",  "source": "/document/reviews_text" },
                { "name": "reviews_title",  "source": "/document/reviews_title" },
                { "name": "AzureSearch_DocumentKey",  "source": "/document/AzureSearch_DocumentKey" },
                { 
                    "name": "pages",
                    "sourceContext": "/document/reviews_text/pages/*",
                    "inputs": [
                        { "name": "SentimentScore", "source": "/document/reviews_text/pages/*/Sentiment" },
                        { "name": "LanguageCode", "source": "/document/Language" },
                        { "name": "Page", "source": "/document/reviews_text/pages/*" },
                        { 
                            "name": "keyphrase", "sourceContext": "/document/reviews_text/pages/*/Keyphrases/*",
                            "inputs": [
                                { "name": "Keyphrases", "source": "/document/reviews_text/pages/*/Keyphrases/*" }
                            ]
                        }
                    ]
                }
            ],
            "outputs": [
                { "name": "output" , "targetName": "tableprojection" }
            ]
        }
    ],
    "knowledgeStore": {
        "storageConnectionString": "{{storage-connection-string}}",
        "projections": [
            {
                "tables": [
                    { "tableName": "hotelReviewsDocument", "generatedKeyName": "Documentid", "source": "/document/tableprojection" },
                    { "tableName": "hotelReviewsPages", "generatedKeyName": "Pagesid", "source": "/document/tableprojection/pages/*" },
                    { "tableName": "hotelReviewsKeyPhrases", "generatedKeyName": "KeyPhrasesid", "source": "/document/tableprojection/pages/*/keyphrase/*" },
                    { "tableName": "hotelReviewsSentiment", "generatedKeyName": "Sentimentid", "source": "/document/tableprojection/pages/*/sentiment/*" }
                ],
                "objects": []
            },
            {
                "tables": [
                    { 
                        "tableName": "hotelReviewsInlineDocument", "generatedKeyName": "Documentid", "sourceContext": "/document",
                        "inputs": [
                            { "name": "name", "source": "/document/name"},
                            { "name": "reviews_date", "source": "/document/reviews_date"},
                            { "name": "reviews_rating", "source": "/document/reviews_rating"},
                            { "name": "reviews_text", "source": "/document/reviews_text"},
                            { "name": "reviews_title", "source": "/document/reviews_title"},
                            { "name": "AzureSearch_DocumentKey", "source": "/document/AzureSearch_DocumentKey" }
                        ]
                    },
                    { 
                        "tableName": "hotelReviewsInlinePages", "generatedKeyName": "Pagesid", "sourceContext": "/document/reviews_text/pages/*",
                        "inputs": [
                            { "name": "SentimentScore", "source": "/document/reviews_text/pages/*/Sentiment"},
                            { "name": "LanguageCode", "source": "/document/Language"},
                            { "name": "Page", "source": "/document/reviews_text/pages/*" }
                        ]
                    },
                    { 
                        "tableName": "hotelReviewsInlineKeyPhrases", "generatedKeyName": "kpidv2", "sourceContext": "/document/reviews_text/pages/*/Keyphrases/*",
                        "inputs": [
                            { "name": "Keyphrases", "source": "/document/reviews_text/pages/*/Keyphrases/*" }
                        ]
                    }
                ],
                "objects": []
            }
        ]
    }
}
```

## <a name="7---create-the-indexer"></a>7 - 建立索引子

最後一個步驟是建立索引子，這會實際讀取資料並啟用技能集。 在 Postman 中，切換至 [建立索引子] 要求，並檢閱本文。 如您所見，索引子的定義是指您已建立的數個其他資源，也就是資料來源、索引和技能集。 

`"parameters/configuration"` 物件可控制索引子內嵌資料的方式。 在此案例中，輸入資料位於具有標頭行和逗點分隔值的單一檔案中。 文件索引鍵是文件的唯一識別碼，這在編碼之前是來源文件的 URL。 最後，技能集輸出值 (例如語言代碼、情感和關鍵片語) 會對應至其在文件中的適當位置。 請注意，雖然 `Language` 具有單一值，但 `Sentiment` 會套用至 `pages` 之陣列中的每個元素。 `Keyphrases` 本身為陣列，而且也會套用至 `pages` 陣列中的每個元素。

在您設定 `api-key` 和 `Content-type` 標頭，並確認要求的本文類似於後續的原始程式碼之後，請在 Postman 中按 [傳送]  。 Postman 會將要求推送至 `https://{{search-service-name}}.search.windows.net/indexers/{{indexer-name}}?api-version={{api-version}}`。 Azure 搜尋服務將會建立並執行索引子。 

```json
{
    "name": "{{indexer-name}}",
    "dataSourceName": "{{datasource-name}}",
    "skillsetName": "{{skillset-name}}",
    "targetIndexName": "{{index-name}}",
    "parameters": {
        "configuration": {
            "dataToExtract": "contentAndMetadata",
            "parsingMode": "delimitedText",
            "firstLineContainsHeaders": true,
            "delimitedTextDelimiter": ","
        }
    },
    "fieldMappings": [
        {
            "sourceFieldName": "AzureSearch_DocumentKey",
            "targetFieldName": "AzureSearch_DocumentKey",
            "mappingFunction": { "name": "base64Encode" }
        }
    ],
    "outputFieldMappings": [
        { "sourceFieldName": "/document/reviews_text/pages/*/Keyphrases/*", "targetFieldName": "Keyphrases" },
        { "sourceFieldName": "/document/Language", "targetFieldName": "Language" },
        { "sourceFieldName": "/document/reviews_text/pages/*/Sentiment", "targetFieldName": "Sentiment" }
    ]
}
```

## <a name="8---run-the-indexer"></a>8 - 執行索引子 

在 Azure 入口網站中，瀏覽至搜尋服務的 [概觀]  ，然後選取 [索引子]  索引標籤。按一下您在先前的步驟中建立的 **hotels-reviews-ixr**。 如果索引子尚未執行，請按 [執行]  按鈕。 索引編製工作可能會引發一些與語言辨識相關的警告，因為資料中包含以認知技能尚未支援的語言撰寫的一些評論。 

## <a name="next-steps"></a>後續步驟

您現在已使用認知服務擴充資料，並將結果投射到知識存放區，接下來即可使用儲存體總管或 Power BI 來探索擴充的資料集。

若要了解如何使用儲存體總管來探索此知識存放區，請參閱下列逐步解說。

> [!div class="nextstepaction"]
> [使用儲存體總管檢視](knowledge-store-view-storage-explorer.md)

若要了解如何將此知識存放區連線到 Power BI，請參閱下列逐步解說。

> [!div class="nextstepaction"]
> [與 Power BI 連線](knowledge-store-connect-power-bi.md)

如果您想要重複此練習，或嘗試不同的 AI 擴充逐步解說，請刪除 hotel-reviews-idxr  索引子。 刪除索引子會將免費的每日交易計數器重設回零。
