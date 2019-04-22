---
title: 教學課程：在索引管線中呼叫認知服務 REST API - Azure 搜尋服務
description: 逐步說明在使用 Postman 和 REST API 透過 JSON Blob 擷取和轉換資料的 Azure 搜尋服務索引中，進行資料擷取、自然語言和影像 AI 處理的範例。
manager: pablocas
author: luiscabrer
services: search
ms.service: search
ms.devlang: NA
ms.topic: tutorial
ms.date: 04/08/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: b6e3335ba78d29896c8a253ac710e6ec0da1829a
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/12/2019
ms.locfileid: "59528368"
---
# <a name="rest-tutorial-call-cognitive-services-apis-in-an-azure-search-indexing-pipeline-preview"></a>REST 教學課程：在 Azure 搜尋服務索引管線中呼叫認知服務 API (預覽)

在本教學課程中，您將了解在 Azure 搜尋服務中使用*認知技能*進行資料擴充程式設計的機制。 技能會受到自然語言處理 (NLP) 與認知服務中的映像分析功能所支援。 透過技能組合和設定，您可以擷取文字，以及映像或所掃描文件檔案的文字表示法。 您也可以偵測語言、實體、關鍵片語等。 其最終結果是，AI 支援的索引管線會在 Azure 搜尋服務索引中建立豐富的額外內容。 

在本教學課程中，您會發出 REST API 呼叫以執行下列工作：

> [!div class="checklist"]
> * 建立對索引路由中的範例資料進行擴充的索引管線
> * 套用內建的技能：實體辨識、語言偵測、文字操作和關鍵片語擷取
> * 了解如何藉由將技能集的輸入對應至輸出，將多項技術串聯在一起
> * 執行要求並檢閱結果
> * 重設索引和索引子以進行進一步開發

Azure 搜尋服務的輸出是全文檢索的可搜尋索引。 您可以使用其他標準功能來強化索引，例如[同義字](search-synonyms.md)、[評分設定檔](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index)、[分析器](search-analyzers.md)和[篩選](search-filters.md)。

本教學課程雖然在免費服務上執行，但可用的交易數目限制為每日 20 份文件。 如果您想要在同一天中多次執行本教學課程，請使用較小的檔案集，如此才能在限制內完成多次執行。

> [!NOTE]
> 當您藉由增加處理次數、新增更多文件或新增更多 AI 演算法來擴展範圍時，您必須連結可計費的認知服務資源。 在認知服務中呼叫 API，以及在 Azure 搜尋服務的文件萃取階段中擷取影像時，都會產生費用。 從文件中擷取文字不會產生費用。
>
> 內建技能的執行會依現有的[認知服務隨用隨附價格](https://azure.microsoft.com/pricing/details/cognitive-services/)收費。 影像擷取定價會依預覽定價收費，如 [Azure 搜尋服務定價頁面](https://go.microsoft.com/fwlink/?linkid=2042400)所述。 [深入](cognitive-search-attach-cognitive-services.md)了解。

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

## <a name="prerequisites"></a>必要條件

本教學課程會使用下列服務、工具和資料。 

[建立 Azure 搜尋服務](search-create-service-portal.md)，或在您目前的訂用帳戶下方[尋找現有服務](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)。 您可以使用本教學課程的免費服務。

[建立 Azure 儲存體帳戶](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)，以儲存範例資料。

[Postman 傳統型應用程式](https://www.getpostman.com/)可用來對 Azure 搜尋服務發出 REST 呼叫。

下載由不同類型小型檔案集組成的[範例資料](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4)。 

## <a name="get-a-key-and-url"></a>取得金鑰和 URL

REST 呼叫需要服務 URL 和每個要求的存取金鑰。 搜尋服務是同時建立，因此如果您將 Azure 搜尋服務新增至您的訂用帳戶，請遵循下列步驟來取得必要的資訊：

1. [登入 Azure 入口網站](https://portal.azure.com/)，並在搜尋服務的 [概觀] 頁面上取得 URL。 範例端點看起來會像是 `https://mydemo.search.windows.net`。

1. 在 [設定]  >  [金鑰] 中，取得服務上完整權限的管理金鑰。 可互換的管理金鑰有兩個，可在您需要變換金鑰時提供商務持續性。 您可以在新增、修改及刪除物件的要求上使用主要或次要金鑰。

![取得 HTTP 端點和存取金鑰](media/search-fiddler/get-url-key.png "取得 HTTP 端點和存取金鑰")

所有要求均都需要在傳送至您服務上的每個要求上使用 API 金鑰。 擁有有效的金鑰就能為每個要求在傳送要求之應用程式與處理要求之服務間建立信任。

## <a name="prepare-sample-data"></a>準備範例資料

擴充管線會從 Azure 資料來源中提取資料。 來源資料必須來自 [Azure 搜尋服務索引子](search-indexer-overview.md)支援的資料來源類型。 Azure 表格儲存體不支援認知搜尋。 針對此練習，我們會使用 Blob 儲存體來展現多個內容類型。

1. [登入 Azure 入口網站](https://portal.azure.com)瀏覽至您的 Azure 儲存體帳戶、按一下 [Blob]，然後按一下 [+ 容器]。

1. [建立 Blob 容器](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)以容納範例資料。 您可以將公用存取層級設定為任何有效值。

1. 建立容器之後，將其開啟，然後在命令列上選取 [上傳]，將您在上一個步驟中下載的範例檔案上傳。

   ![Azure Blob 儲存體中的來源檔案](./media/cognitive-search-quickstart-blob/sample-data.png)

1. 範例檔案載入之後，請取得 Blob 儲存體的容器名稱和連接字串。 您可以瀏覽至 Azure 入口網站中的儲存體帳戶，以執行此動作。 在 [存取金鑰] 上，複製 [連接字串] 欄位。

   連接字串應為類似於下列範例的 URL：

      ```http
      DefaultEndpointsProtocol=https;AccountName=cogsrchdemostorage;AccountKey=<your account key>;EndpointSuffix=core.windows.net
      ```

此外也有其他方式可指定連接字串，例如提供共用存取簽章。 若要深入了解資料來源認證，請參閱[編製 Azure Blob 儲存體的索引](search-howto-indexing-azure-blob-storage.md#Credentials)。

## <a name="set-up-postman"></a>設定 Postman

啟動 Postman 及設定 HTTP 要求。 如果您不熟悉此工具，請參閱[使用 Postman 探索 Azure 搜尋服務 REST API](search-fiddler.md)。

本教學課程中使用的要求方法為 **POST**、**PUT** 和 **GET**。 標題金鑰是設定為 "application/json" 的「內容類型」和設定為您 Azure 搜尋服務管理員金鑰的「API 金鑰」。 主體是您放置呼叫實際內容的地方。 

  ![半結構化搜尋](media/search-semi-structured-data/postmanoverview.png)

我們使用 Postman 來對您的搜尋服務進行四個 API 呼叫，以建立資料來源、技能集、索引及索引子。 資料來源包括指向您儲存體帳戶和您 JSON 資料的指標。 您的搜尋服務會在載入資料時進行連線。


## <a name="create-a-data-source"></a>建立資料來源

現在，您的服務和來源檔案皆已備妥，您可以開始組合索引管線的元件。 首先請從[資料來源物件](https://docs.microsoft.com/rest/api/searchservice/create-data-source)開始；此物件會指示 Azure 搜尋服務如何擷取外部來源資料。

在要求標頭中，提供您在建立 Azure 搜尋服務時所使用的服務名稱，以及為您的搜尋服務產生的 API 金鑰。 在要求本文中，指定 Blob 容器名稱和連接字串。

### <a name="sample-request"></a>範例要求
```http
POST https://[service name].search.windows.net/datasources?api-version=2017-11-11-Preview
Content-Type: application/json
api-key: [admin key]
```
#### <a name="request-body-syntax"></a>要求本文的語法
```json
{
  "name" : "demodata",
  "description" : "Demo files to demonstrate cognitive search capabilities.",
  "type" : "azureblob",
  "credentials" :
  { "connectionString" :
    "DefaultEndpointsProtocol=https;AccountName=<your account name>;AccountKey=<your account key>;"
  },
  "container" : { "name" : "<your blob container name>" }
}
```
傳送要求。 Web 測試工具應會傳回確認成功的狀態碼 201。 

由於這是您第一次的要求，請查看 Azure 入口網站，以確認已在 Azure 搜尋服務中建立資料來源。 在搜尋服務儀表板頁面上，確認 [資料來源] 清單有新的項目。 您可能需要等候幾分鐘，讓入口網站重新整理頁面。 

  ![入口網站中的資料來源圖格](./media/cognitive-search-tutorial-blob/data-source-tile.png "入口網站中的資料來源圖格")

如果發生 403 或 404 錯誤，請檢查要求建構：`api-version=2017-11-11-Preview` 位於端點上，`api-key` 應位於標頭中的 `Content-Type` 後面，且其值必須是適用於搜尋服務的值。 您可以在本教學課程的其餘步驟中重複使用該標頭。

## <a name="create-a-skillset"></a>建立技能集

在此步驟中，您會定義一組要套用至資料的擴充步驟。 我們將每個擴充步驟稱為*技能*，一組擴充步驟則稱之為*技能集*。 本教學課程會使用為技能集[內建的認知技能](cognitive-search-predefined-skills.md)：

+ [語言偵測](cognitive-search-skill-language-detection.md)，用以識別內容的語言。

+ [文字分割](cognitive-search-skill-textsplit.md)，用以在呼叫關鍵片語擷取技能之前，將大型內容分成較小的區塊。 關鍵片語擷取可接受不超過 50,000 個字元的輸入。 有些範例檔案需要進行分割，以符合這項限制。

+ [實體辨識](cognitive-search-skill-entity-recognition.md)，用以從 Blob 容器中的內容擷取組織名稱。

+ [關鍵片語擷取](cognitive-search-skill-keyphrases.md)，用以提取最高排名的關鍵片語。 

### <a name="sample-request"></a>範例要求
在您進行此 REST 呼叫前，如果您的工具不會在呼叫間保留要求標頭，請記得在下方的要求中取代服務名稱和管理金鑰。 

此要求會建立技能集。 在本教學課程的其餘部分請參考技能集名稱 ```demoskillset```。

```http
PUT https://[servicename].search.windows.net/skillsets/demoskillset?api-version=2017-11-11-Preview
api-key: [admin key]
Content-Type: application/json
```
#### <a name="request-body-syntax"></a>要求本文的語法
```json
{
  "description":
  "Extract entities, detect language and extract key-phrases",
  "skills":
  [
    {
      "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
      "categories": [ "Organization" ],
      "defaultLanguageCode": "en",
      "inputs": [
        {
          "name": "text", "source": "/document/content"
        }
      ],
      "outputs": [
        {
          "name": "organizations", "targetName": "organizations"
        }
      ]
    },
    {
      "@odata.type": "#Microsoft.Skills.Text.LanguageDetectionSkill",
      "inputs": [
        {
          "name": "text", "source": "/document/content"
        }
      ],
      "outputs": [
        {
          "name": "languageCode",
          "targetName": "languageCode"
        }
      ]
    },
    {
      "@odata.type": "#Microsoft.Skills.Text.SplitSkill",
      "textSplitMode" : "pages",
      "maximumPageLength": 4000,
      "inputs": [
        {
          "name": "text",
          "source": "/document/content"
        },
        {
          "name": "languageCode",
          "source": "/document/languageCode"
        }
      ],
      "outputs": [
        {
          "name": "textItems",
          "targetName": "pages"
        }
      ]
    },
    {
      "@odata.type": "#Microsoft.Skills.Text.KeyPhraseExtractionSkill",
      "context": "/document/pages/*",
      "inputs": [
        {
          "name": "text", "source": "/document/pages/*"
        },
        {
          "name":"languageCode", "source": "/document/languageCode"
        }
      ],
      "outputs": [
        {
          "name": "keyPhrases",
          "targetName": "keyPhrases"
        }
      ]
    }
  ]
}
```

傳送要求。 Web 測試工具應會傳回確認成功的狀態碼 201。 

#### <a name="explore-the-request-body"></a>探索要求本文

請留意每個頁面套用關鍵片語擷取技能的情形。 藉由將內容設定為 ```"document/pages/*"```，將可擴充每個文件/頁面陣列成員 (文件中的每個頁面) 的這項執行。

每項技術會分別對文件的內容執行。 在處理期間，Azure 搜尋服務會萃取每份文件，以讀取不同檔案格式的內容。 找到來自來源檔案的文字時，會將文字放入產生的 ```content``` 欄位中，每份文件一個欄位。 據此，請將輸入設定為 ```"/document/content"```。

以下顯示技能集的圖形化表示法。 

![了解技能集](media/cognitive-search-tutorial-blob/skillset.png "了解技能集")

輸出可以對應至索引、作為下游技能的輸入，或在使用語言代碼時同時作為對應和輸入。 在索引中，語言代碼可用於篩選。 作為輸入時，文字分析技能會使用語言代碼指出斷字方面的語言規則。

如需技能集基本概念的詳細資訊，請參閱[如何定義技能集](cognitive-search-defining-skillset.md)。

## <a name="create-an-index"></a>建立索引

在本節中，您會藉由指定要在可搜尋索引中包含哪些欄位以及每個欄位的搜尋屬性，來定義索引結構描述。 欄位具有類型，並且可取用決定如何使用欄位 (可搜尋、可排序等等) 的屬性。 索引中的欄位名稱不需要完全符合來源中的欄位名稱。 在後續步驟中，您可以在索引子中新增欄位對應以連接來源-目的地欄位。 針對此步驟，請使用與搜尋應用程式相關的欄位命名慣例來定義索引。

此練習會使用下列欄位和欄位類型：

| 欄位名稱： | `id`       | 內容   | languageCode | keyPhrases         | 組織     |
|--------------|----------|-------|----------|--------------------|-------------------|
| 欄位類型： | Edm.String|Edm.String| Edm.String| List<Edm.String>  | List<Edm.String>  |


### <a name="sample-request"></a>範例要求
在您進行此 REST 呼叫前，如果您的工具不會在呼叫間保留要求標頭，請記得在下方的要求中取代服務名稱和管理金鑰。 

此要求會建立索引。 在本教學課程的其餘部分請使用索引名稱 ```demoindex```。

```http
PUT https://[servicename].search.windows.net/indexes/demoindex?api-version=2017-11-11-Preview
api-key: [api-key]
Content-Type: application/json
```
#### <a name="request-body-syntax"></a>要求本文的語法

```json
{
  "fields": [
    {
      "name": "id",
      "type": "Edm.String",
      "key": true,
      "searchable": true,
      "filterable": false,
      "facetable": false,
      "sortable": true
    },
    {
      "name": "content",
      "type": "Edm.String",
      "sortable": false,
      "searchable": true,
      "filterable": false,
      "facetable": false
    },
    {
      "name": "languageCode",
      "type": "Edm.String",
      "searchable": true,
      "filterable": false,
      "facetable": false
    },
    {
      "name": "keyPhrases",
      "type": "Collection(Edm.String)",
      "searchable": true,
      "filterable": false,
      "facetable": false
    },
    {
      "name": "organizations",
      "type": "Collection(Edm.String)",
      "searchable": true,
      "sortable": false,
      "filterable": false,
      "facetable": false
    }
  ]
}
```
傳送要求。 Web 測試工具應會傳回確認成功的狀態碼 201。 

若要深入了解如何定義索引，請參閱[建立索引 (Azure 搜尋服務 REST API)](https://docs.microsoft.com/rest/api/searchservice/create-index)。


## <a name="create-an-indexer-map-fields-and-execute-transformations"></a>建立索引子、對應欄位，並執行轉換

至此，您已建立資料來源、技能集和索引。 這三項元件構成了[索引子](search-indexer-overview.md)的一部分，可將各項資料一併提取到多階段的單一作業中。 若要在索引子中結合這些項目，您必須定義欄位對應。 

+ fieldMappings 會在技能集之前進行處理，用來將資料來源中的來源欄位對應到索引中的目標欄位。 如果兩端上的欄位名稱和類型都相同，則不需要任何對應。

+ outputFieldMappings 會在技能集之後進行處理，用來參考文件萃取或擴充後才建立的 sourceFieldNames。 targetFieldName 是索引中的欄位。

除了將輸入連結至輸出外，您也可以使用欄位對應來壓平合併資料結構。 如需詳細資訊，請參閱[如何將擴充的欄位對應至可搜尋的索引](cognitive-search-output-field-mapping.md)。

### <a name="sample-request"></a>範例要求

在您進行此 REST 呼叫前，如果您的工具不會在呼叫間保留要求標頭，請記得在下方的要求中取代服務名稱和管理金鑰。 

此外也請提供索引子的名稱。 在本教學課程的其餘部分，您可以將其參照為 ```demoindexer```。

```http
PUT https://[servicename].search.windows.net/indexers/demoindexer?api-version=2017-11-11-Preview
api-key: [api-key]
Content-Type: application/json
```
#### <a name="request-body-syntax"></a>要求本文的語法

```json
{
  "name":"demoindexer", 
  "dataSourceName" : "demodata",
  "targetIndexName" : "demoindex",
  "skillsetName" : "demoskillset",
  "fieldMappings" : [
    {
      "sourceFieldName" : "metadata_storage_path",
      "targetFieldName" : "id",
      "mappingFunction" :
        { "name" : "base64Encode" }
    },
    {
      "sourceFieldName" : "content",
      "targetFieldName" : "content"
    }
  ],
  "outputFieldMappings" :
  [
    {
      "sourceFieldName" : "/document/organizations",
      "targetFieldName" : "organizations"
    },
    {
      "sourceFieldName" : "/document/pages/*/keyPhrases/*",
      "targetFieldName" : "keyPhrases"
    },
    {
      "sourceFieldName": "/document/languageCode",
      "targetFieldName": "languageCode"
    }
  ],
  "parameters":
  {
    "maxFailedItems":-1,
    "maxFailedItemsPerBatch":-1,
    "configuration":
    {
      "dataToExtract": "contentAndMetadata",
      "imageAction": "generateNormalizedImages"
    }
  }
}
```

傳送要求。 Web 測試工具應會傳回確認處理成功的狀態碼 201。 

預期此步驟需要幾分鐘的時間才能完成。 即使資料集很小，分析技能仍需要大量計算。 某些技能 (例如影像分析) 需要長時間執行。

> [!TIP]
> 建立索引子時會叫用管線。 資料的存取、輸入和輸出的對應或作業順序若有問題，都會在這個階段中出現。 若要透過程式碼或指令碼的變更重新執行管線，您可能需要先卸除物件。 如需詳細資訊，請參閱[重設並重新執行](#reset)。

#### <a name="explore-the-request-body"></a>探索要求本文

指令碼會將 ```"maxFailedItems"``` 設定為 -1，這會指示索引引擎在資料匯入期間忽略錯誤。 此設定有其用處，因為示範資料來源中只有少量文件。 若要有較大的資料來源，您應將值設定為大於 0。

同時也請注意組態參數中的 ```"dataToExtract":"contentAndMetadata"``` 陳述式。 此陳述式會指示索引子自動擷取不同檔案格式的內容，以及每個檔案的相關中繼資料。 

在擷取內容時，您可以設定 ```imageAction```，以從在資料來源中找到的影像擷取文字。 ```"imageAction":"generateNormalizedImages"``` 組態可與 OCR 技術和文字合併技術結合，指示索引子從影像中擷取文字 (例如，從「停」交通號誌中擷取「停」這個字)，並將其內嵌為內容欄位的一部分。 此行為適用於內嵌在文件中的影像 (例如 PDF 內的影像)，以及在資料來源中找到的影像 (例如 JPG 檔案)。

## <a name="check-indexer-status"></a>檢查索引子狀態

索引子經定義後，將會在您提交要求時自動執行。 根據您所定義的認知技能，索引編製所需的時間可能會超出您的預期。 若要確認索引子是否仍在執行，請傳送下列要求以檢查索引子狀態。

```http
GET https://[servicename].search.windows.net/indexers/demoindexer/status?api-version=2017-11-11-Preview
api-key: [api-key]
Content-Type: application/json
```

回應會指出索引子是否正在執行。 索引編製完成後，請對 STATUS 端點使用另一個 HTTP GET (如上所列)，以查看在擴充期間是否發生任何錯誤和警告的報告。  

某些來源檔案和技能的組合常會出現警告，這並不一定表示有問題。 在本教學課程中，警告是良性的 (例如，沒有來自 JPEG 檔案的文字輸入)。 您可以檢閱狀態回應，以取得在索引編製期間所發出警告的詳細資訊。
 
## <a name="verify-content"></a>驗證內容

索引編製完成後，請執行會傳回個別欄位內容的查詢。 根據預設，Azure 搜尋服務會傳回前 50 項結果。 範例資料很小，因此預設值即足堪使用。 不過，在使用較大的資料集時，您可能需要在查詢字串中加上參數，以傳回較多結果。 如需相關指示，請參閱[如何在 Azure 搜尋服務中對結果分頁](search-pagination-page-layout.md)。

在驗證步驟中，您會查詢所有欄位的索引。

```http
GET https://[servicename].search.windows.net/indexes/demoindex?api-version=2017-11-11-Preview
api-key: [api-key]
Content-Type: application/json
```

輸出將是索引結構描述，附有每個欄位的名稱、類型和屬性。

提交第二個 `"*"` 查詢，以傳回單一欄位的所有內容，例如 `organizations`。

```http
GET https://[servicename].search.windows.net/indexes/demoindex/docs?search=*&$select=organizations&api-version=2017-11-11-Preview
api-key: [api-key]
Content-Type: application/json
```

對其他欄位重複前述步驟：此練習中的內容、語言程式碼、關鍵片語和組織。 您可以透過使用逗號分隔清單的 `$select` 傳回多個欄位。

您可以使用 GET 或 POST，視查詢字串的複雜度和長度而定。 如需詳細資訊，請參閱[使用 REST API 進行查詢](https://docs.microsoft.com/rest/api/searchservice/search-documents)。

<a name="access-enriched-document"></a>

## <a name="accessing-the-enriched-document"></a>存取擴充的文件

認知搜尋可讓您查看擴充文件的結構。 擴充的文件是在擴充期間建立的暫時性結構，在程序完成後即會刪除。

若要擷取在索引編製期間建立之擴充文件的快照集，請將名為 ```enriched``` 的欄位新增至您的索引。 索引子會自動在該欄位中傾印該文件所有擴充項目的字串表示法。

```enriched``` 欄位將會包含一個字串，作為記憶體內部的擴充文件在 JSON 中的邏輯表示法。  不過，該欄位的值是有效的 JSON 文件。 引號會逸出，因此您必須將 `\"` 取代為 `"`，才能以格式化 JSON 的形式檢視文件。  

```enriched``` 欄位的用途是偵錯，只是為了幫助您了解以運算式進行評估之內容的邏輯圖形。 它可以作為了解和偵錯技能集的實用工具。

重複前述練習 (包括 `enriched` 欄位)，以擷取擴充文件的內容：

### <a name="request-body-syntax"></a>要求本文的語法
```json
{
  "fields": [
    {
      "name": "id",
      "type": "Edm.String",
      "key": true,
      "searchable": true,
      "filterable": false,
      "facetable": false,
      "sortable": true
    },
    {
      "name": "content",
      "type": "Edm.String",
      "sortable": false,
      "searchable": true,
      "filterable": false,
      "facetable": false
    },
    {
      "name": "languageCode",
      "type": "Edm.String",
      "searchable": true,
      "filterable": false,
      "facetable": false
    },
    {
      "name": "keyPhrases",
      "type": "Collection(Edm.String)",
      "searchable": true,
      "filterable": false,
      "facetable": false
    },
    {
      "name": "organizations",
      "type": "Collection(Edm.String)",
      "searchable": true,
      "sortable": false,
      "filterable": false,
      "facetable": false
    },
    {
      "name": "enriched",
      "type": "Edm.String",
      "searchable": false,
      "sortable": false,
      "filterable": false,
      "facetable": false
    }
  ]
}
```
<a name="reset"></a>

## <a name="reset-and-rerun"></a>重設並重新執行

在管線開發的早期實驗階段中，設計反覆項目最實用的方法是從 Azure 搜尋服務中刪除物件，並讓您的程式碼加以重建。 資源名稱是唯一的。 刪除物件可讓您使用相同的名稱加以重新建立。

若要使用新的定義為您的文件重新編製索引：

1. 刪除索引以移除保存的資料。 刪除索引子以在服務上重新加以建立。
2. 修改技能集和索引定義。
3. 在服務上重新建立索引和索引子以執行管線。 

您可以使用入口網站來刪除索引、索引子和技能集。

```http
DELETE https://[servicename].search.windows.net/skillsets/demoskillset?api-version=2017-11-11-Preview
api-key: [api-key]
Content-Type: application/json
```

成功刪除時會傳回狀態碼 204。

當您的程式碼成熟時，您可以精簡重建策略。 如需詳細資訊，請參閱[如何重建索引](search-howto-reindex.md)。

## <a name="takeaways"></a>重要心得

本教學課程示範了藉由建立下列元件組件來建置擴充索引管線的基本步驟：資料來源、技能集、索引和索引子。

[預先定義的技能](cognitive-search-predefined-skills.md)已透過輸入和輸出連同技能集定義和鏈結技能的機制一起導入。 您也已了解在將管線中的擴充值路由至 Azure 搜尋服務上的可搜尋索引時，索引子定義中必須要有 `outputFieldMappings`。

最後，您了解到如何測試結果並重設系統，以進行進一步的反覆運算。 您已了解對索引發出查詢，會傳回由擴充的索引管線建立的輸出。 此版本提供了檢視內部建構 (由系統建立的擴充文件) 的機制。 您也已了解如何檢查索引子狀態，以及在重新執行管線之前應刪除哪些物件。

## <a name="clean-up-resources"></a>清除資源

在完成教學課程後，最快速的清除方式是刪除包含 Azure 搜尋服務和 Azure Blob 服務的資源群組。 如果您將這兩項服務放在相同群組中，此時刪除資源群組，將會永久刪除其中的所有內容，包括服務與您為此教學課程建立的任何已儲存內容。 在入口網站中，資源群組名稱位在每個服務的 [概觀] 頁面上。

## <a name="next-steps"></a>後續步驟

以自訂技能自訂或擴充管線。 建立自訂技能並將其新增至技能集，以便讓您自行撰寫的文字或影像分析上線。 

> [!div class="nextstepaction"]
> [範例：建立自訂技能](cognitive-search-create-custom-skill-example.md)
