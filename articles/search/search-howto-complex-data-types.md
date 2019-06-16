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
ms.date: 06/13/2019
ms.custom: seodec2018
ms.openlocfilehash: 61f2094449995e26c3d321aaf35deb3d60ff250c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67056599"
---
# <a name="how-to-model-complex-data-types-in-azure-search"></a>如何在 Azure 搜尋服務中模型化複雜資料類型

用來填入 Azure 搜尋服務索引的外部資料集可以有各種形狀。 有時，它們會包括階層式或巢狀子結構。 範例可能包含多個位址的單一客戶、 多個色彩和大小單一 SKU、 單一書籍的多位作者等等。 模型化而論，您可能會看到這些結構稱之為*複雜*，*複合*，*複合*，或*彙總*資料型別。 Azure 搜尋服務會使用此概念的詞彙是**複雜型別**。 在 Azure 搜尋服務中，複雜型別會使用建立模型**複雜欄位**。 複雜的欄位是包含子系 （子欄位） 可以是任何資料類型，包括其他複雜類型的欄位。 這與程式設計語言中的結構化的資料型別運作方式類似的方式。

複雜的欄位代表的是，文件中的單一物件或物件，根據資料類型的陣列。 類型的欄位`Edm.ComplexType`代表單一的物件，而類型的欄位`Collection(Edm.ComplexType)`代表物件的陣列。

Azure 搜尋服務原本就支援複雜型別和集合。 這些類型可讓您建立模型幾乎所有的 Azure 搜尋服務索引中的 JSON 結構。 在舊版的 Azure 搜尋服務 Api 中，只有扁平化資料列集可供匯入。 在最新版本中，您的索引可以現在更密切地對應至資料來源。 換句話說，如果您的來源資料具有複雜類型，您的索引可以有複雜型別也。

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

Indexers are a different story. When defining an indexer, in particular one used to build a knowledge store, your index can have nested complex types. An indexer is able to hold a chain of complex data structures in-memory, and when it includes a skillset, it can support highly complex data forms. For more information and an example, see [How to get started with knowledge store](knowledge-store-howto.md).
-->

```json
{
  "name": "hotels",
  "fields": [
    { "name": "HotelId", "type": "Edm.String", "key": true, "filterable": true },
    { "name": "HotelName", "type": "Edm.String", "searchable": true, "filterable": false },
    { "name": "Description", "type": "Edm.String", "searchable": true, "analyzer": "en.lucene" },
    { "name": "Address", "type": "Edm.ComplexType",
      "fields": [
        { "name": "StreetAddress", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "searchable": true },
        { "name": "City", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
        { "name": "StateProvince", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true }
      ]
    },
    { "name": "Rooms", "type": "Collection(Edm.ComplexType)",
      "fields": [
        { "name": "Description", "type": "Edm.String", "searchable": true, "analyzer": "en.lucene" },
        { "name": "Type", "type": "Edm.String", "searchable": true },
        { "name": "BaseRate", "type": "Edm.Double", "filterable": true, "facetable": true }
      ]
    }
  ]
}
```

## <a name="updating-complex-fields"></a>正在更新複雜的欄位

所有[重新編製索引規則](search-howto-reindex.md)套用到欄位通常仍會套用至複雜的欄位。 重新描述幾個這裡的主要規則加入欄位並不需要重建的索引，但大部分的修改。

### <a name="structural-updates-to-the-definition"></a>定義的結構化更新

您可以到複雜的欄位中加入新的子欄位，在任何時間，而不需要重建索引。 例如，將"ZipCode"新增至`Address`或 「 便利設施 」 到`Rooms`允許，就像將最上層欄位加入至索引。 現有文件會在新欄位的 null 值，直到您明確地藉由更新您的資料填入這些欄位。

請注意，在複雜類型，每一個子欄位類型，且可能具有屬性，只為最上層欄位執行

### <a name="data-updates"></a>資料更新

更新現有的文件中的索引`upload`動作適用於複雜和簡單的欄位相同的方式，會取代所有欄位。 不過， `merge` (或`mergeOrUpload`時套用到現有的文件) 無法運作相同跨所有欄位。 具體來說，`merge`不支援合併的項目集合內。 針對集合的基本型別和複雜的集合中有這項限制。 若要更新的集合，您將需要擷取完整的集合值，進行變更，然後索引 API 要求中包含新的集合。

## <a name="searching-complex-fields"></a>搜尋複雜的欄位

自由格式的搜尋運算式的運作如預期般與複雜類型。 如果任何可搜尋的欄位或子欄位任意處的文件中符合，文件本身是相符項目。

查詢取得多個當您具有多個名詞和運算子，而某些詞彙有指定，欄位名稱做為可透過細微[Lucene 語法](query-lucene-syntax.md)。 例如，此查詢嘗試比對兩個詞彙，「 波特蘭"及"OR"，針對 [位址] 欄位的兩個子欄位：

    search=Address/City:Portland AND Address/State:OR

查詢喜歡此*它*全文檢索搜尋與篩選不同。 在 篩選器，透過複雜集合的子欄位的查詢相互關聯使用中的範圍變數[`any`或是`all` ](search-query-odata-collection-operators.md)。 上述的 Lucene 查詢會傳回包含"波特蘭，Maine"和"波特蘭奧勒岡州 」，以及其他城市奧勒岡州的文件。 會發生這種情況是因為每個子句會套用至整個文件，其欄位的所有值，因此沒有 「 目前子文件 」 的概念。 如需詳細資訊，請參閱[了解 OData 集合篩選器，在 Azure 搜尋服務](search-query-understand-collection-filters.md)。

## <a name="selecting-complex-fields"></a>選取複雜的欄位

`$select`參數用來選擇在搜尋結果中傳回哪些欄位。 若要使用這個參數來選取特定的子欄位的複雜的欄位，包含以斜線分隔的子欄位的父欄位 (`/`)。

    $select=HotelName, Address/City, Rooms/BaseRate

在索引中必須欄位標示為可擷取，如果您想在搜尋結果中。 只有標示為可擷取的欄位可用於`$select`陳述式。

## <a name="filter-facet-and-sort-complex-fields"></a>篩選、 facet 和排序複雜欄位

相同[OData 路徑語法](query-odata-filter-orderby-syntax.md)用於篩選和回覆搜尋也可以用於面向設定、 排序和搜尋要求中選取欄位。 對於複雜型別，規則適用於控管哪些子欄位可標示為可排序或面向化。 如需有關這些規則的詳細資訊，請參閱 <<c0> [ 建立索引 API 參考](https://docs.microsoft.com/rest/api/searchservice/create-index#request)。

### <a name="faceting-sub-fields"></a>多面向子欄位

任何子欄位可標示為 可面向化，除非它是類型`Edm.GeographyPoint`或`Collection(Edm.GeographyPoint)`。

父代文件 (hotel) 非子文件中複雜的集合 （聊天室） 會計算在面向結果所傳回的文件計數。 例如，假設旅館都有 20 聊天室的型別 「 套件 」。 提供此 facet 參數`facet=Rooms/Type`，則 facet 計數會是另一個用於 hotel 不 20 聊天室。

### <a name="sorting-complex-fields"></a>複雜的排序欄位

排序作業套用至文件 （旅館） 和非子文件 （聊天室）。 當您有複雜型別集合，例如室時，務必了解，您無法排序聊天室完全。 事實上，您無法排序任何集合。

排序作業時使用欄位的每個文件，單一值欄位是一個簡單的欄位或在複雜類型中的子欄位。 例如，`Address/City`允許可排序，因為只有一個的位址，每旅館，因此`$orderby=Address/City`會排序依城市的旅館。

### <a name="filtering-on-complex-fields"></a>根據複雜的欄位進行篩選

您可以參考複雜欄位的篩選條件運算式的子欄位。 只要使用相同[OData 路徑語法](query-odata-filter-orderby-syntax.md)所用的面向設定、 排序和選取的欄位。 比方說，下列的篩選條件會傳回在加拿大的所有旅館：

    $filter=Address/Country eq 'Canada'

若要篩選的複雜集合欄位，您可以使用**lambda 運算式**具有[`any`並`all`運算子](search-query-odata-collection-operators.md)。 在此情況下，**範圍變數**的 lambda 運算式會是具有子欄位的物件。 您可以參考給這些子欄位，使用標準 OData 路徑的語法。 例如，下列的篩選條件會傳回至少一個豪華聊天室的所有旅館和所有非的-吸煙聊天室：

    $filter=Rooms/any(room: room/Type eq 'Deluxe Room') and Rooms/all(room: not room/SmokingAllowed)

因為最上層的簡單欄位的複雜欄位的簡單子欄位只能包含在篩選中有**filterable**屬性設為`true`索引定義中。 如需詳細資訊，請參閱 <<c0> [ 建立索引 API 參考](https://docs.microsoft.com/rest/api/searchservice/create-index#request)。

## <a name="next-steps"></a>後續步驟

請嘗試[旅館資料集](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/README.md)中**匯入資料**精靈。 您必須提供存取資料的讀我檔案中的 Cosmos DB 連線資訊。

此資訊，在精靈的第一個步驟是建立新的 Azure Cosmos DB 資料來源。 進一步在精靈中，當您來到 [目標索引] 頁面中，您會看到索引具有複雜類型。 建立和載入此索引，然後執行查詢，以了解新的結構。

> [!div class="nextstepaction"]
> [匯入、 編製索引和查詢的快速入門： 入口網站精靈](search-get-started-portal.md)