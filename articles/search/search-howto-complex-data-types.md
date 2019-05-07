---
title: 如何將複雜資料類型模型化 - Azure 搜尋服務
description: 巢狀或階層式資料結構都可以使用 ComplexType 及 集合資料類型的 Azure 搜尋服務索引中模型化。
author: brjohnstmsft
manager: jlembicz
ms.author: brjohnst
tags: complex data types; compound data types; aggregate data types
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.custom: seodec2018
ms.openlocfilehash: 00606ed5cbcd8681748241e9404c6e6e5aa95021
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2019
ms.locfileid: "65147306"
---
# <a name="how-to-model-complex-data-types-in-azure-search"></a>如何在 Azure 搜尋服務中模型化複雜資料類型

外部資料集，用來填入 Azure 搜尋服務索引有時會包括階層式或巢狀子結構。 範例可能包含多個位置和電話號碼的單一客戶、 多個色彩和大小單一 SKU、 單一書籍的多位作者等等。 模型化而論，您可能會看到這些結構稱之為*複雜資料型別*，*複合資料類型*，*複合資料型別*，或*彙總資料型別*。 在 Azure 搜尋服務術語中，複雜型別會是包含子系 （子欄位） 的欄位而後者本身可以是簡單或很複雜。 這是類似於程式設計語言中的結構化的資料類型。 複雜的欄位可以是單一欄位，表示文件中的單一物件或集合，表示物件的陣列

Azure 搜尋服務原本就支援複雜型別和集合。 在一起，這些類型可讓您建立 Azure 搜尋服務索引中幾乎任何巢狀的 JSON 結構的模型。 在舊版的 Azure 搜尋服務 Api 中，只有扁平化資料列集可供匯入。 在最新版本中，您的索引可以現在更密切地對應至資料來源。 換句話說，如果您的來源資料具有複雜類型，您的索引可以有複雜型別也。

若要開始，我們建議[旅館資料集](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/README.md)，您可以在載入**匯入資料**精靈在 Azure 入口網站中。 此精靈會在來源中偵測到複雜型別，並建議索引結構描述偵測到的結構為基礎。

> [!Note]
> 複雜類型的支援已正式推出在`api-version=2019-05-06`。 
>
> 如果您的搜尋解決方案建置在較早的因應措施的扁平化的資料集集合中，您應該變更您要包含複雜型別中的最新的 API 版本所支援的索引。 如需有關升級的 API 版本的詳細資訊，請參閱[升級至最新的 REST API 版本](search-api-migration.md)或是[升級至最新的.NET SDK 版本](search-dotnet-sdk-migration-version-9.md)。

## <a name="example-of-a-complex-structure"></a>複雜的結構範例

下列 JSON 文件是由簡單的欄位和複雜的欄位所組成。 複雜的欄位，例如`Address`和`Rooms`，有子欄位。 `Address` 有一組這些子的欄位的值，因為它是文件中的單一物件。 相反地，`Rooms`集合中有多組及其子欄位的值，一個用於每個物件。

```json
{
    "HotelId": "1",
    "HotelName": "Secret Point Motel",
    "Description": "Ideally located on the main commercial artery of the city in the heart of New York.",
    "Address": {
        "StreetAddress": "677 5th Ave",
        "City": "New York",
        "StateProvince": "NY"
    },
    "Rooms": [
        {
            "Description": "Budget Room, 1 Queen Bed (Cityside)",
            "Type": "Budget Room",
            "BaseRate": 96.99,
        },
        {
            "Description": "Deluxe Room, 2 Double Beds (City View)",
            "Type": "Deluxe Room",
            "BaseRate": 150.99,
        },
    ]
}
```

## <a name="creating-complex-fields"></a>建立複雜的欄位

任何索引定義中，您可以使用入口網站中， [REST API](https://docs.microsoft.com/rest/api/searchservice/create-index)，或[.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index?view=azure-dotnet)建立結構描述，其中包含複雜型別。 

下列範例顯示簡單的欄位、 集合和複雜型別與 JSON 索引結構描述。 請注意，在複雜類型，每一個子欄位類型，且可能具有屬性，只為最上層欄位執行。 結構描述會對應至上述範例資料。 `Address` 是複雜的欄位不是集合 （旅館都有一個位址）。 `Rooms` 是 （旅館都有許多室） 的複雜集合欄位。

<!---
For indexes used in a [push-model data import](search-what-is-data-import.md) strategy, where you are pushing a JSON data set to an Azure Search index, you can only have the basic syntax shown here: single complex types like `Address`, or a `Collection(Edm.ComplexType)` like `Rooms`. You cannot have complex types nested inside other complex types in an index used for push-model data ingestion.

Indexers are a different story. When defining an indexer, in particular one used to build a knowledge store, your index can have nested complex types. An indexer is able to hold a chain of complex data structures in-memory, and when it includes a skillset, it can support highly complex data forms. For more information and an example, see [How to get started with Knowledge Store](knowledge-store-howto.md).
-->

```json
{
    "name": "hotels",
    "fields": [
        {   "name": "HotelId", "type": "Edm.String", "key": true, "filterable": true    },
        {   "name": "HotelName", "type": "Edm.String", "searchable": true, "filterable": false },
        { "name": "Description", "type": "Edm.String", "searchable": true, "analyzer": "en.lucene" },
        {   "name": "Address", "type": "Edm.ComplexType",
            "fields": [{
                    "name": "StreetAddress",
                    "type": "Edm.String",
                    "filterable": false,
                    "sortable": false,
                    "facetable": false,
                    "searchable": true  },
                {
                    "name": "City",
                    "type": "Edm.String",
                    "searchable": true,
                    "filterable": true,
                    "sortable": true,
                    "facetable": true
                },
                {
                    "name": "StateProvince",
                    "type": "Edm.String",
                    "searchable": true,
                    "filterable": true,
                    "sortable": true,
                    "facetable": true
                }
            ]
        },
        {
            "name": "Rooms",
            "type": "Collection(Edm.ComplexType)",
            "fields": [{
                    "name": "Description",
                    "type": "Edm.String",
                    "searchable": true,
                    "analyzer": "en.lucene"
                },
                {
                    "name": "Type",
                    "type": "Edm.String",
                    "searchable": true
                },
                {
                    "name": "BaseRate",
                    "type": "Edm.Double",
                    "filterable": true,
                    "facetable": true
                },
            ]
        }
    ]
}
```
## <a name="updating-complex-fields"></a>正在更新複雜的欄位

所有[重新編製索引規則](search-howto-reindex.md)套用到欄位通常仍會套用至複雜的欄位。 重新描述幾個主要的規則，以在這裡，將欄位加入不需要重建的索引，但大部分的修改執行。

### <a name="structural-updates-to-the-definition"></a>定義的結構化更新

您可以到複雜的欄位中加入新的子欄位，在任何時間，而不需要重建索引。 例如，將"ZipCode"新增至`Address`或 「 便利設施 」 到`Rooms`允許，就像將最上層欄位加入至索引。 現有文件會在新欄位的 null 值，直到您明確地藉由更新您的資料填入這些欄位。

請注意，在複雜類型，每一個子欄位類型，且可能具有屬性，只為最上層欄位執行

### <a name="data-updates"></a>資料更新

上傳動作以更新現有的文件索引中的運作方式相同複雜和簡單的欄位，會取代所有欄位。 不過，合併 （或 mergeOrUpload 時套用到現有的文件） 無法運作相同跨所有欄位。 具體來說，合併並沒有合併項目集合內的能力。 這是適用於基本型別的集合，以及複雜的集合。 若要更新的集合，您需要擷取完整的集合值，變更應用程式，然後索引 API 要求中包含新的集合。


## <a name="searching-complex-fields"></a>搜尋複雜的欄位

自由格式的搜尋運算式的運作如預期般與複雜類型。 如果任何可搜尋的欄位或子欄位任意處的文件中符合，文件本身是相符項目。 

查詢取得多個當您具有多個名詞和運算子，而某些詞彙有指定，欄位名稱做為可透過細微[Lucene 語法](query-lucene-syntax.md)。 例如，此查詢嘗試比對兩個詞彙，「 波特蘭"及"OR"，針對 [位址] 欄位的兩個子欄位：

```json
search=Address/City:Portland AND Address/State:OR
```

這類查詢沒有相互關聯的全文檢索搜尋 (不同於篩選，可以使用任何關聯的複雜集合的子欄位上的查詢或，例如在 SQL 中的相互關聯子查詢)。 這表示上述 Lucene 查詢會傳回包含"波特蘭，Maine"的文件，以及 「 波特蘭奧勒岡州 」 和其他城市奧勒岡州。 這是欄位的因為每個子句評估指定，在整個文件中的所有值，因此沒有 「 目前子文件 」 的概念。 

 

## <a name="selecting-complex-fields"></a>選取複雜的欄位

`$select`參數用來選擇在搜尋結果中傳回哪些欄位。 若要使用這個參數來選取特定的子欄位的複雜的欄位，包含以斜線分隔的子欄位的父欄位 (`/`)。

```json
$select=HotelName, Address/City, Rooms/BaseRate
```

在索引中必須欄位標示為可擷取，如果您想在搜尋結果中。 只有標示為可擷取的欄位可用於`$select`陳述式。 


## <a name="filter-facet-and-sort-complex-fields"></a>篩選、 facet 和排序複雜欄位

相同[OData 路徑語法](query-odata-filter-orderby-syntax.md)用於篩選和回覆搜尋也可以用於面向設定、 排序和搜尋要求中選取欄位。 對於複雜型別，規則適用於控管哪些子欄位可標示為可排序或面向化。 

### <a name="faceting-sub-fields"></a>多面向子欄位 

任何子欄位可標示為 可面向化，除非它是類型`Edm.GeographyPoint`或`Collection(Edm.GeographyPoint)`。 

當文件計數會傳回多面向導覽結構中時，計數會相對於父代文件 (hotel) 不複雜的集合 （聊天室） 內的巢狀文件。 例如，假設旅館都有 20 聊天室的型別 「 套件 」。 提供此 facet 參數`facet=Rooms/Type`，facet 計數會是其中一個父代文件 （旅館） 並不具有中等子文件 （聊天室）。 

### <a name="sorting-complex-fields"></a>複雜的排序欄位

排序作業套用至文件 （旅館） 和非子文件 （聊天室）。 當您有複雜型別集合，例如室時，務必了解，您無法排序聊天室完全。 事實上，您無法排序任何集合。 

排序作業運作時的欄位是單一值是否為簡單的欄位，或在複雜類型中的子欄位方式。 比方說，`$orderby=Address/ZipCode`複雜型別是否可排序，因為只有一個每旅館的郵遞區號。 

重新排序規則的描述，在索引欄位必須標示為可篩選項和可排序用於`$orderby`陳述式。 

## <a name="next-steps"></a>後續步驟

 請嘗試[旅館資料集](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/README.md)中**匯入資料**精靈。 您必須提供存取資料的讀我檔案中的 Cosmos DB 連線資訊。 
 
 此資訊，在精靈的第一個步驟是建立新的 Azure Cosmos DB 資料來源。 進一步在精靈中，當您來到 [目標索引] 頁面中，您會看到索引具有複雜類型。 建立和載入此索引，然後執行查詢，以了解新的結構。

> [!div class="nextstepaction"]
> [匯入、 編製索引和查詢的快速入門： 入口網站精靈](search-get-started-portal.md)