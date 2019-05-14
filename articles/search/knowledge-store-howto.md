---
title: 如何開始使用知識存放區 (預覽) - Azure 搜尋服務
description: 了解將 Azure 搜尋服務中的 AI 索引管線所建立的擴充文件傳送至您 Azure 儲存體帳戶中知識存放區的步驟。 您可以從該處檢視、調整及取用 Azure 搜尋服務和其他應用程式中的擴充文件。
manager: cgronlun
author: HeidiSteen
services: search
ms.service: search
ms.topic: quickstart
ms.date: 05/08/2019
ms.author: heidist
ms.openlocfilehash: d9006e3fcfc9691b9f3eec4b86c545fd3fea9f8a
ms.sourcegitcommit: 399db0671f58c879c1a729230254f12bc4ebff59
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/09/2019
ms.locfileid: "65471747"
---
# <a name="how-to-get-started-with-knowledge-store"></a>如何開始使用知識存放區

[知識存放區](knowledge-store-concept-intro.md)是 Azure 搜尋服務中的新預覽功能，可儲存在索引管線中建立的 AI 擴充資料，以供使用其他應用程式進行知識採礦。 您也可以使用已儲存的擴充資料，以了解 Azure 搜尋服務索引管線並縮小其範圍。

知識存放區是由技能集定義的。 對於一般的 Azure 搜尋服務全文檢索搜尋案例，技能集的目的在於提供 AI 擴充資料，提高內容的可搜尋性。 在知識採礦的情況下，技能集的角色會在其他應用程式和處理程序中建立、填入和儲存多個用於分析或模型化的資料結構。

這個練習會從範例資料、服務和工具開始，逐步了解建立和使用您第一個知識存放區的基本工作流程，並將重點放在技能集定義。

## <a name="prerequisites"></a>必要條件

本快速入門會使用下列服務、工具和資料。 

+ [建立 Azure 搜尋服務](search-create-service-portal.md)，或在您目前的訂用帳戶下方[尋找現有服務](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)。 您可以使用本教學課程的免費服務。 

+ [建立 Azure 儲存體帳戶](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)，以儲存範例資料。 您的知識存放區將存在於 Azure 儲存體中。 

+ 在 S0 預付型方案層[建立認知服務資源](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) \(機器翻譯\)，以廣泛取得在 AI 擴充資料中使用的完整技能。 此資訊和伙 Azure 搜尋服務都必須位於相同區域。

+ [Postman 傳統型應用程式](https://www.getpostman.com/)可將要求傳送至 Azure 搜尋服務。

+ [Postman 集合](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/Caselaw)包含預先準備的要求，可用於建立資料來源、索引、技能集和索引子。 有幾個物件定義因為太長而無法包含在此文章中。 您必須取得此集合，才能查看完整的索引和技能集定義。

+ [Caselaw 範例資料](https://github.com/Azure-Samples/azure-search-sample-data/tree/master/caselaw) \(英文\) 源自 [Caselaw 存取專案](https://case.law/bulk/download/) \(英文\) Public Bulk Data 下載頁面。 具體來說，此練習會使用第一個下載項目 (Arkansas) 的前 10 個文件。 我們為此練習上傳了包含 10 個文件的範例至 GitHub。

## <a name="get-a-key-and-url"></a>取得金鑰和 URL

REST 呼叫需要服務 URL 和每個要求的存取金鑰。 搜尋服務是同時建立，因此如果您將 Azure 搜尋服務新增至您的訂用帳戶，請遵循下列步驟來取得必要的資訊：

1. [登入 Azure 入口網站](https://portal.azure.com/)，並在搜尋服務的 [概觀] 頁面上取得 URL。 範例端點看起來會像是 `https://mydemo.search.windows.net`。

1. 在 [設定]  >  [金鑰] 中，取得服務上完整權限的管理金鑰。 可互換的管理金鑰有兩個，可在您需要變換金鑰時提供商務持續性。 您可以在新增、修改及刪除物件的要求上使用主要或次要金鑰。

    ![取得 HTTP 端點和存取金鑰](media/search-fiddler/get-url-key.png "取得 HTTP 端點和存取金鑰")

所有要求均都需要在傳送至您服務上的每個要求上使用 API 金鑰。

## <a name="prepare-sample-data"></a>準備範例資料

1. [登入 Azure 入口網站](https://portal.azure.com)瀏覽至您的 Azure 儲存體帳戶、按一下 [Blob]，然後按一下 [+ 容器]。

1. [建立 Blob 容器](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)以容納範例資料。 使用容器名稱 "caselaw-test"。 您可以將公用存取層級設定為任何有效值。

1. 建立容器之後，請加以開啟，然後選取命令列的 [上傳]。

   ![命令列上的 上傳](media/search-semi-structured-data/upload-command-bar.png "命令列上的 上傳")

1. 瀏覽至包含 **caselaw-sample.json** 範例檔案的資料夾。 選取檔案，然後按一下 [上傳]。


## <a name="set-up-postman"></a>設定 Postman

啟動 Postman 並匯入 Caselaw Postman 集合。 或者，設定一系列的 HTTP 要求。 如果您不熟悉此工具，請參閱[使用 Postman 探索 Azure 搜尋服務 REST API](search-fiddler.md)。

+ 此逐步解說中每個呼叫的要求方法都是 **PUT** 或 **POST**。
+ 要求標頭 (2) 包括：分別將 "Content-type" 設定為 "application/json"，將 "api-key" 設為您的「管理金鑰」(管理金鑰是您搜尋主索引鍵的預留位置)。 
+ 要求本文是您放置呼叫實際內容的地方。 

  ![半結構化搜尋](media/search-semi-structured-data/postmanoverview.png)

我們使用 Postman 對您的搜尋服務進行四個 API 呼叫，並按此順序建立資料來源、索引、技能集及索引子。 資料來源包括指向您 Azure 儲存體帳戶和 JSON 資料的指標。 您的搜尋服務會在匯入資料時進行連線。

[建立技能集](#create-skillset)是此逐步解說的重點：它會指定擴充步驟和在知識存放區中保存資料的方式。

URL 端點必須指定 api-version，且每個呼叫都應傳回 **201 Created**。 用於建立支援知識存放區之技能集的預覽 api-version 為 `2019-05-06-Preview` (區分大小寫)。

從您的 REST 用戶端執行下列 API 呼叫。

## <a name="create-a-data-source"></a>建立資料來源

[建立資料來源 API](https://docs.microsoft.com/rest/api/searchservice/create-data-source) 會建立一個 Azure 搜尋服務物件，以指定要編製索引的資料。

此呼叫的端點為 `https://[service name].search.windows.net/datasources?api-version=2019-05-06-Preview` 

1. 使用您的搜尋服務名稱來取代 `[service name]`。 

2. 對於此呼叫，要求本文必須包含您的儲存體帳戶連接字串和 Blob 容器名稱。 此連線可以在 Azure 入口網站中，您儲存體帳戶的**存取金鑰**內部找到。 

   請務必先取代要求本文中的連接字串和 Blob 容器名稱，再執行呼叫。

    ```json
    {
        "name": "caselaw-ds",
        "description": null,
        "type": "azureblob",
        "subtype": null,
        "credentials": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<YOUR-STORAGE-ACCOUNT>;AccountKey=<YOUR-STORAGE-KEY>;EndpointSuffix=core.windows.net"
        },
        "container": {
            "name": "<YOUR-BLOB-CONTAINER-NAME>",
            "query": null
        },
        "dataChangeDetectionPolicy": null,
        "dataDeletionDetectionPolicy": null
    }
    ```

3. 傳送要求。 回應應為 **201**，且回應本文看起來應該和您所提供的要求承載幾乎一模一樣。

    ```json
    {
        "name": "caselaw-ds",
        "description": null,
        "type": "azureblob",
        "subtype": null,
        "credentials": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<your storage account>;AccountKey=<your storage key>;EndpointSuffix=core.windows.net"
        },
        "container": {
            "name": "<your blob container name>",
            "query": null
        },
        "dataChangeDetectionPolicy": null,
        "dataDeletionDetectionPolicy": null
    }
    ```

## <a name="create-an-index"></a>建立索引
    
第二次呼叫為[建立索引 API](https://docs.microsoft.com/rest/api/searchservice/create-data-source)，進而建立可儲存所有可搜尋資料的 Azure 搜尋服務索引。 索引會指定所有欄位、參數及屬性。

您不一定需要索引來進行知識採礦，但必須提供索引才能執行索引子。 

此呼叫的 URL 為 `https://[service name].search.windows.net/indexes?api-version=2019-05-06-Preview`

1. 使用您的搜尋服務名稱來取代 `[service name]`。

2. 將索引定義從 Postman 集合中的「建立索引」要求複製到要求本文中。 索引定義包含數百行，因為太長而無法在這裡列印。 

   索引的外殼由下列元素組成。 

   ```json
   {
      "name": "caselaw",
      "defaultScoringProfile": null,
      "fields": [],
      "scoringProfiles": [],
      "corsOptions": null,
      "suggesters": [],
      "analyzers": [],
      "tokenizers": [],
      "tokenFilters": [],
      "charFilters": [],
      "encryptionKey": null
   }
   ```

3. `fields` 集合包含大量索引定義。 它包含簡單欄位、具巢狀結構的[複雜欄位](search-howto-complex-data-types.md)，以及集合。

   檢閱第 302-384 行的欄位定義 `casebody`。 請注意，需使用階層式表示法時，複雜欄位可包含其他複雜欄位。

   ```json
   {
    "name": "casebody",
    "type": "Edm.ComplexType",
    "fields": [
        {
            "name": "status",
            "type": "Edm.String",
            "searchable": true,
            "filterable": true,
            "retrievable": true,
            "sortable": true,
            "facetable": true,
            "key": false,
            "indexAnalyzer": null,
            "searchAnalyzer": null,
            "analyzer": null,
            "synonymMaps": []
        },
        {
            "name": "data",
            "type": "Edm.ComplexType",
            "fields": [
                {
                    "name": "head_matter",
                    "type": "Edm.String",
                    "searchable": true,
                    "filterable": false,
                    "retrievable": true,
                    "sortable": false,
                    "facetable": false,
                    "key": false,
                    "indexAnalyzer": null,
                    "searchAnalyzer": null,
                    "analyzer": null,
                    "synonymMaps": []
                },
                {
                    "name": "opinions",
                    "type": "Collection(Edm.ComplexType)",
                    "fields": [
                        {
                            "name": "author",
                            "type": "Edm.String",
                            "searchable": true,
                            "filterable": true,
                            "retrievable": true,
                            "sortable": false,
                            "facetable": true,
                            "key": false,
                            "indexAnalyzer": null,
                            "searchAnalyzer": null,
                            "analyzer": null,
                            "synonymMaps": []
                        },
                        {
                            "name": "text",
                            "type": "Edm.String",
                            "searchable": true,
                            "filterable": false,
                            "retrievable": true,
                            "sortable": false,
                            "facetable": false,
                            "key": false,
                            "indexAnalyzer": null,
                            "searchAnalyzer": null,
                            "analyzer": null,
                            "synonymMaps": []
                        },
                        {
                            "name": "type",
                            "type": "Edm.String",
                            "searchable": true,
                            "filterable": true,
                            "retrievable": true,
                            "sortable": false,
                            "facetable": true,
                            "key": false,
                            "indexAnalyzer": null,
                            "searchAnalyzer": null,
                            "analyzer": null,
                            "synonymMaps": []
                        }
                    ]
                },
    . . .
   ```

4. 傳送要求。 

   回應應為 **201**，且看起來應該類似以下範例 (僅顯示前幾個欄位)：

    ```json
    {
        "name": "caselaw",
        "defaultScoringProfile": null,
        "fields": [
            {
                "name": "id",
                "type": "Edm.String",
                "searchable": true,
                "filterable": true,
                "retrievable": true,
                "sortable": true,
                "facetable": true,
                "key": true,
                "indexAnalyzer": null,
                "searchAnalyzer": null,
                "analyzer": null,
                "synonymMaps": []
            },
            {
                "name": "name",
                "type": "Edm.String",
                "searchable": true,
                "filterable": true,
                "retrievable": true,
                "sortable": true,
                "facetable": true,
                "key": false,
                "indexAnalyzer": null,
                "searchAnalyzer": null,
                "analyzer": null,
                "synonymMaps": []
            },
      . . .
    ```

<a name="create-skillset"></a>

## <a name="create-a-skillset-and-knowledge-store"></a>建立技能集和知識存放區

[建立技能集 API](https://docs.microsoft.com/rest/api/searchservice/create-skillset) \(英文\) 可建立 Azure 搜尋服務物件，以指定要呼叫哪些認知技術、如何鏈結技術，以及如何指定知識存放區 (此逐步解說最重要的重點)。

此呼叫的端點為 `https://[service name].search.windows.net/skillsets?api-version=2019-05-06-Preview`

1. 使用您的搜尋服務名稱來取代 `[service name]`。

2. 將技能集定義從 Postman 集合中的「建立技能集」要求複製到要求本文中。 技能集定義包含數百行，因為太長而無法在這裡列印，但它是本逐步解說的重點。

   技能集的外殼由下列元素組成。 `skills` 集合會定義記憶體內部的擴充資料，但 `knowledgeStore` 定義會指定輸出的儲存方式。 `cognitiveServices` 定義是您與 AI 擴充引擎的連線。

   ```json
   {
    "name": "caselaw-ss",
    "description": null,
    "skills": [],
    "cognitiveServices": [],
    "knowledgeStore": []
   }
   ```

3. 請先設定 `cognitiveServices` 和 `knowledgeStore` 金鑰與連接字串。 在範例中，這些字串位於技能集定義後方，靠近要求本文的結尾處。 使用認知服務資源，在位於與 Azure 搜尋服務相同區域的 S0 服務層級中佈建。

    ```json
    "cognitiveServices": {
        "@odata.type": "#Microsoft.Azure.Search.CognitiveServicesByKey",
        "description": "YOUR-SAME-REGION-S0-COGNITIVE-SERVICES-RESOURCE",
        "key": "YOUR-COGNITIVE-SERVICES-KEY"
    },
    "knowledgeStore": {
        "storageConnectionString": "YOUR-STORAGE-ACCOUNT-CONNECTION-STRING",
    ```

3. 檢閱技能集合，特別是分別位於第 85 和 170 行的 Shaper 技能。 Shaper 技能很重要，因為它會組合您進行知識採礦時所需的資料結構。 技能集在執行期間，這些結構只會存在於記憶體內部，但當您移至下一個步驟時，就會了解此輸出如何儲存至知識存放區以供進一步探索。

   下列程式碼片段來自第 217 行。 

    ```json
    "name": "Opinions",
    "source": null,
    "sourceContext": "/document/casebody/data/opinions/*",
    "inputs": [
        {
            "name": "Text",
            "source": "/document/casebody/data/opinions/*/text"
        },
        {
            "name": "Author",
            "source": "/document/casebody/data/opinions/*/author"
        },
        {
            "name": "Entities",
            "source": null,
            "sourceContext": "/document/casebody/data/opinions/*/text/pages/*/entities/*",
            "inputs": [
                {
                    "name": "Entity",
                    "source": "/document/casebody/data/opinions/*/text/pages/*/entities/*/value"
                },
                {
                    "name": "EntityType",
                    "source": "/document/casebody/data/opinions/*/text/pages/*/entities/*/category"
                }
            ]
        }
    ]
   . . .
   ```

3. 檢閱 `knowledgeStore` 中的 `projections` 元素 (從第 262 行開始)。 投影會指定知識存放區組合。 投影是在資料表-物件組中指定的，但目前有時只有一個。 如您在第一個投影中所見，已指定 `tables` 但未指定 `objects`。 在第二個中則是相反的。

   在 Azure 儲存體中，您建立的每個資料表都會在資料表儲存體中建立，而且每個物件都會在 Blob 儲存體中取得一個容器。

   Blob 物件通常包含擴充資料的完整運算式。 資料表通常包含您針對特定用途所安排組合中的部分擴充資料。 此範例顯示一個 Cases 資料表及 Opinions 資料表，但沒有顯示其他資料表，例如 Entities、Attorneys、Judges 和 Parties。

    ```json
    "projections": [
        {
            "tables": [
                {
                    "tableName": "Cases",
                    "generatedKeyName": "CaseId",
                    "source": "/document/Case"
                },
                {
                    "tableName": "Opinions",
                    "generatedKeyName": "OpinionId",
                    "source": "/document/Case/OpinionsSnippets/*"
                }
            ],
            "objects": []
        },
        {
            "tables": [],
            "objects": [
                {
                    "storageContainer": "enrichedcases",
                    
                    "source": "/document/CaseFull"
                }
            ]
        }
    ]
    ```

5. 傳送要求。 回應應為 **201**，且看起來應該類似以下範例 (僅顯示回應的前面部分)。

    ```json
    {
    "name": "caselaw-ss",
    "description": null,
    "skills": [
        {
            "@odata.type": "#Microsoft.Skills.Text.SplitSkill",
            "name": "SplitSkill#1",
            "description": null,
            "context": "/document/casebody/data/opinions/*/text",
            "defaultLanguageCode": "en",
            "textSplitMode": "pages",
            "maximumPageLength": 5000,
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/casebody/data/opinions/*/text
                }
            ],
            "outputs": [
                {
                    "name": "textItems",
                    "targetName": "pages"
                }
            ]
        },
        . . .
    ```

## <a name="create-and-run-an-indexer"></a>建立及執行索引子

[建立索引子 API](https://docs.microsoft.com/rest/api/searchservice/create-indexer) \(英文\) 會建立並立即執行索引子。 您到目前為止已經建立的所有定義都將在此步驟中發揮效用。 索引子會立即執行，因為它不存在服務中。 當它存在之後，對現有索引子進行的 POST 呼叫就會是一個更新作業。

此呼叫的端點為 `https://[service name].search.windows.net/indexers?api-version=2019-05-06-Preview`

1. 使用您的搜尋服務名稱來取代 `[service name]`。 

2. 對於此呼叫，要求本文是指定索引子名稱。 索引子需要資料來源和索引。 技能集對索引子而言是選擇性的，但對 AI 擴充資料來說是必要的。

    ```json
    {
        "name": "caselaw-idxr",
        "description": null,
        "dataSourceName": "caselaw-ds",
        "skillsetName": "caselaw-ss",
        "targetIndexName": "caselaw",
        "disabled": null,
        "schedule": null,
        "parameters": {
            "batchSize": 1,
            "maxFailedItems": null,
            "maxFailedItemsPerBatch": null,
            "base64EncodeKeys": null,
            "configuration": {
                "parsingMode": "jsonLines"
            }
        },
        "fieldMappings": [],
        "outputFieldMappings": [
            {
                "sourceFieldName": "/document/casebody/data/opinions/*/text/pages/*/people/*",
                "targetFieldName": "people",
                "mappingFunction": null
            },
            {
                "sourceFieldName": "/document/casebody/data/opinions/*/text/pages/*/organizations/*",
                "targetFieldName": "orginizations",
                "mappingFunction": null
            },
            {
                "sourceFieldName": "/document/casebody/data/opinions/*/text/pages/*/locations/*",
                "targetFieldName": "locations",
                "mappingFunction": null
            },
            {
                "sourceFieldName": "/document/Case/OpinionsSnippets/*/Entities/*",
                "targetFieldName": "entities",
                "mappingFunction": null
            },
            {
                "sourceFieldName": "/document/casebody/data/opinions/*/text/pages/*/keyPhrases/*",
                "targetFieldName": "keyPhrases",
                "mappingFunction": null
            }
        ]
    }
    ```

3. 傳送要求。 回應應為 **201**，且回應本文看起來應該和您所提供的要求承載幾乎一模一樣 (為了簡潔起見已修剪)。

    ```json
    {
        "name": "caselaw-idxr",
        "description": null,
        "dataSourceName": "caselaw-ds",
        "skillsetName": "caselaw-ss",
        "targetIndexName": "caselaw",
        "disabled": null,
        "schedule": null,
        "parameters": {
            "batchSize": 1,
            "maxFailedItems": null,
            "maxFailedItemsPerBatch": null,
            "base64EncodeKeys": null,
            "configuration": {
                "parsingMode": "jsonLines"
            }
        },
        "fieldMappings": [],
        "outputFieldMappings": [
            {
                "sourceFieldName": "/document/casebody/data/opinions/*/text/pages/*/people/*",
                "targetFieldName": "people",
                "mappingFunction": null
            }
        ]
    }
    ```

## <a name="explore-knowledge-store"></a>探索知識存放區

第一個文件匯入後，您就可以開始探索。 針對此工作，請在入口網站中使用[**儲存體總管**](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-storage-explorer)。

請務必了解一點，那就是知識存放區與 Azure 搜尋服務是完全分離的。 Azure 搜尋服務索引和知識存放區都包含資料表示和內容，但從那裡開始就分道揚鑣了。 請針對全文檢索搜尋、篩選搜尋與 Azure 搜尋服務中支援的所有案例使用索引。 或者，您也可以只使用知識存放區，然後附加其他工具來分析內容。

## <a name="takeaways"></a>重要心得

您現在已經在 Azure 儲存體中建立第一個知識存放區，並使用儲存體總管檢視擴充資料。 這是使用預存擴充資料的基本體驗。 

## <a name="next-steps"></a>後續步驟

Shaper 技能會負責執行繁重工作，建立可合併成新形式的細微資料表單。 下一個步驟即是檢閱這個技能的參考頁面，了解其使用方式詳細資料。

> [!div class="nextstepaction"]
> [Shaper 技能參考](cognitive-search-skill-shaper.md)


<!---
## Keep This

How to convert unformatted JSON into an indented JSON document structure that allows you to quickly identify nested structures. Useful for creating an index that includes complex types.

1. Use Visual Studio Code.
2. Open data.jsonl
--->