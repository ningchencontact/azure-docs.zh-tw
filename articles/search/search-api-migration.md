---
title: 升級至最新的 Azure 搜尋服務 REST API 版本 - Azure 搜尋服務
description: 檢閱 API 版本間的差異，並了解需要進行哪些動作，才能將現有的程式碼移轉至最新的 Azure 搜尋服務 REST API 版本。
author: brjohnstmsft
manager: jlembicz
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: brjohnst
ms.openlocfilehash: 85a8ddf4ce87d7ac8ce460c0aff56311a2ea4578
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "65540699"
---
# <a name="upgrade-to-the-latest-azure-search-service-rest-api-version"></a>升級至最新的 Azure 搜尋服務 REST API 版本
如果您使用舊版[Azure 搜尋服務 REST API](https://docs.microsoft.com/rest/api/searchservice/)，本文將協助您升級應用程式以使用最新的正式 API 版本，2019年-05-06。

版本 2019年-05-06 的 REST api 包含一些較早版本的變更。 這些是大部分具有回溯相容性，因此變更程式碼應該只需要最少的工作 (視您之前使用的版本而定)。 [若要升級的步驟](#UpgradeSteps)概述使用新功能所需的程式碼變更。

> [!NOTE]
> Azure 搜尋服務執行個體支援範圍的 REST API 版本，包括舊的。 您可以繼續使用這些 API 版本，但建議您將您的程式碼移轉至最新版本，讓您可以存取新功能。

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-2019-05-06"></a>什麼是 2019年-05-06 版的新功能
版本 2019年-05-06 是 Azure 搜尋服務 REST API 的最新正式運作版本。 已轉換至正式運作的狀態，這個 API 版本的功能包括：

* [自動完成](index-add-suggesters.md)是完成的部分指定的詞彙輸入自動提示功能。

* [複雜型別](search-howto-complex-data-types.md)提供原生支援的 Azure 搜尋服務索引中的結構化的物件資料。

* [剖析模式的 JsonLines](search-howto-index-json-blobs.md)、 組件的 Azure Blob 編製索引，會建立每個會以新行字元分隔的 JSON 實體的一個搜尋服務文件。

* [認知搜尋](cognitive-search-concept-intro.md)提供編製索引，會利用認知服務的 AI 擴充引擎。

預覽功能的數個版本符合此正式推出的更新。 若要檢閱新的預覽功能的清單，請參閱[搜尋服務 REST api 版本 2019年-05-06-Preview](search-api-preview.md)。

## <a name="breaking-changes"></a>重大變更

包含下列功能的現有程式碼會中斷 api-version = 2019年-05-06。

### <a name="indexer-for-azure-cosmos-db---datasource-is-now-type-cosmosdb"></a>Azure Cosmos DB 索引子資料來源是現在"type":"cosmosdb 」

如果您使用[Cosmos DB 索引子](search-howto-index-cosmosdb.md )，您必須變更`"type": "documentdb"`至`"type": "cosmosdb"`。

### <a name="indexer-execution-result-errors-no-longer-have-status"></a>索引子執行結果錯誤不再需要的狀態

索引子執行的錯誤結構先前`status`項目。 這個項目已移除，因為它不提供有用的資訊。

### <a name="indexer-data-source-api-no-longer-returns-connection-strings"></a>索引子資料來源 API 不會再傳回連接字串

從 API 2019-05-06 和 2019年-05-06-Preview 開始，資料來源 API 的版本不會再傳回連接字串中任何 REST 作業的回應。 在先前的 API 版本，使用 POST 建立的資料來源傳回 Azure 搜尋服務**201**後面 OData 回應，其中包含純文字中的連接字串。

### <a name="named-entity-recognition-cognitive-skill-is-now-discontinued"></a>具名實體辨識，認知技能現已停用

如果您呼叫[名稱實體辨識](cognitive-search-skill-named-entity-recognition.md)在您的程式碼的技巧，呼叫會失敗。 取代的功能[實體辨識](cognitive-search-skill-entity-recognition.md)。 您應該能夠技能參考取代為任何其他變更。 API 簽章是這兩個版本相同。 

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>升級步驟
如果您要從先前的 GA 版本升級，2017年-11-11 或 2016年-09-01，您可能就沒有變更的版本號碼而不進行任何變更您的程式碼。 您可能需要變更程式碼的唯一情況是︰

* 您的程式碼在 API 回應中傳回無法辨認的屬性時失敗。 您的應用程式預設應該會略過不了解的屬性。

* 您的程式碼仍然存在 API 要求，並嘗試將它們重新傳送給新的 API 版本。 例如，發生原因可能是您的應用程式持續保存搜尋服務 API 所傳回的接續 Token (如需詳細資訊，請在[搜尋服務 API 參考](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)) 中查找 `@search.nextPageParameters`。

如果下列其中一種情況適用您的情況，您可能需要據此變更程式碼。 否則，除非您想要開始使用任何變更應該是必要[新功能](#WhatsNew)2019年-05-06 目前安裝版本。

如果您要從預覽 API 版本升級，也會套用上述，但您也必須知道某些預覽功能不會用於版本 2019年-05-06:

* ["More like this"查詢](search-more-like-this.md)
* [CSV blob 編製索引](search-howto-index-csv-blobs.md)
* [Cosmos DB 索引子的 MongoDB API 支援](search-howto-index-cosmosdb.md)

如果您的程式碼會使用這些功能，您將無法升級至 API 版本 2019年-05-06，而不移除它們的使用方式。

> [!IMPORTANT]
> 預覽版 API 是針對測試與評估，不應該用於生產環境。
> 

### <a name="upgrading-complex-types"></a>升級的複雜型別

如果您的程式碼會使用複雜型別與較舊的預覽 API 版本 2017年-11-11-Preview 或 2016年-09-01-Preview，有一些新的和變更的限制版本 2019年-05-06 都必須要注意：

+ 子欄位和複雜的集合，每個索引數目的深度限制已降低。 如果您建立超過這些限制，使用預覽 api 版本的索引時，嘗試更新或重新建立它們使用 API 版本 2019年-05-06 將會失敗。 如果這適用於您，您必須重新設計您的結構描述，以符合新的限制範圍內，然後重新建置您的索引。

+ 在 api-version 2019-05-06 上的每個文件的複雜集合的項目數沒有新的限制。 如果您超過這些限制，使用預覽 api 版本的文件建立索引，則任何嘗試重新索引使用 api-version 2019-05-06 該資料將會失敗。 如果這適用於您，您必須減少複雜集合項目，每個文件之前您的資料重新編製索引。

如需詳細資訊，請參閱 < [Azure 搜尋服務的服務限制](search-limits-quotas-capacity.md)。

### <a name="how-to-upgrade-an-old-complex-type-structure"></a>如何升級舊的複雜型別結構

如果您的程式碼使用複雜型別與其中一個較舊的預覽 API 版本，您可能使用一種索引定義格式，看起來像這樣：

```json
{
  "name": "hotels",  
  "fields": [
    { "name": "HotelId", "type": "Edm.String", "key": true, "filterable": true },
    { "name": "HotelName", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": true, "facetable": false },
    { "name": "Description", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "en.microsoft" },
    { "name": "Description_fr", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.microsoft" },
    { "name": "Category", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
    { "name": "Tags", "type": "Collection(Edm.String)", "searchable": true, "filterable": true, "sortable": false, "facetable": true, "analyzer": "tagsAnalyzer" },
    { "name": "ParkingIncluded", "type": "Edm.Boolean", "filterable": true, "sortable": true, "facetable": true },
    { "name": "LastRenovationDate", "type": "Edm.DateTimeOffset", "filterable": true, "sortable": true, "facetable": true },
    { "name": "Rating", "type": "Edm.Double", "filterable": true, "sortable": true, "facetable": true },
    { "name": "Address", "type": "Edm.ComplexType" },
    { "name": "Address/StreetAddress", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "searchable": true },
    { "name": "Address/City", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
    { "name": "Address/StateProvince", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
    { "name": "Address/PostalCode", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
    { "name": "Address/Country", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
    { "name": "Location", "type": "Edm.GeographyPoint", "filterable": true, "sortable": true },
    { "name": "Rooms", "type": "Collection(Edm.ComplexType)" }, 
    { "name": "Rooms/Description", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "en.lucene" },
    { "name": "Rooms/Description_fr", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene" },
    { "name": "Rooms/Type", "type": "Edm.String", "searchable": true },
    { "name": "Rooms/BaseRate", "type": "Edm.Double", "filterable": true, "facetable": true },
    { "name": "Rooms/BedOptions", "type": "Edm.String", "searchable": true },
    { "name": "Rooms/SleepsCount", "type": "Edm.Int32", "filterable": true, "facetable": true },
    { "name": "Rooms/SmokingAllowed", "type": "Edm.Boolean", "filterable": true, "facetable": true },
    { "name": "Rooms/Tags", "type": "Collection(Edm.String)", "searchable": true, "filterable": true, "facetable": true, "analyzer": "tagsAnalyzer" }
  ]
}  
```

API 版本 2017年-11-11-Preview 中引進了較新的樹狀結構類似的格式來定義索引欄位。 新的格式，複雜的每個欄位就有其子欄位定義的其中欄位的集合。 在 API 版本 2019年-05-06，以獨佔方式使用這個新的格式，並嘗試建立或更新索引使用舊的格式將會失敗。 如果您有使用舊的格式建立的索引，您必須將其更新為新的格式，他們可以使用管理 API 版本 2019年-05-06 之前使用 API 版本 2017年-11-11-Preview。

您可以使用下列步驟中使用 API 版本 2017年-11-11-Preview 來更新成新格式的 「 一般 」 的索引：

1. 執行 GET 要求來擷取您的索引。 如果它已在新的格式中，您已完成。

2. 轉換成新格式的索引從 「 一般 」 的格式。 您必須為這撰寫程式碼，因為沒有任何範例程式碼可在撰寫本文時。

3. 執行索引更新為新格式的 PUT 要求。 請確定未變更的欄位，搜尋能力/filterability 類似的索引的任何其他詳細資料，因為這不允許更新索引 api。

> [!NOTE]
> 您不可以管理使用舊的 「 一般 」 格式，從 Azure 入口網站建立的索引。 請升級的 「 一般 」 表示 「 樹木 」 表示您的方便盡早在您的索引。

## <a name="next-steps"></a>後續步驟

檢閱 Azure 搜尋服務 REST API 參考文件。 如果您遇到問題，說明上尋求[StackOverflow](https://stackoverflow.com/)或是[連絡支援人員](https://azure.microsoft.com/support/community/?product=search)。

> [!div class="nextstepaction"]
> [搜尋服務 REST API 參考](https://docs.microsoft.com/rest/api/searchservice/)

