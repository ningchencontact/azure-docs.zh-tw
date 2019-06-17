---
title: OData 參考排序依據-Azure 搜尋服務
description: 如需在 Azure 搜尋服務查詢中的排序依據語法的 OData 語言參考。
ms.date: 06/13/2019
services: search
ms.service: search
ms.topic: conceptual
author: Brjohnstmsft
ms.author: brjohnst
ms.manager: cgronlun
translation.priority.mt:
- de-de
- es-es
- fr-fr
- it-it
- ja-jp
- ko-kr
- pt-br
- ru-ru
- zh-cn
- zh-tw
ms.openlocfilehash: 1ced35dc73e6d596fbeda32590ab0b69df396c5c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079752"
---
# <a name="odata-orderby-syntax-in-azure-search"></a>Azure 搜尋服務中的 OData $orderby 語法

 您可以使用[OData **$orderby**參數](query-odata-filter-orderby-syntax.md)套用 Azure 搜尋服務中的搜尋結果的自訂排序次序。 這篇文章描述的語法 **$orderby**在詳細資料。 如需有關如何使用更多一般資訊 **$orderby**時顯示搜尋結果，請參閱[如何使用搜尋在 Azure 搜尋服務中結果](search-pagination-page-layout.md)。

## <a name="syntax"></a>語法

**$Orderby**參數接受以逗號分隔的清單設定為 32 **order by 子句**。 Order by 子句的語法由下列 EBNF 描述 ([擴充式 Backus-naur 形式](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)):

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
order_by_clause ::= (field_path | sortable_function) ('asc' | 'desc')?

sortable_function ::= geo_distance_call | 'search.score()'
```

互動式的語法圖表也會提供：

> [!div class="nextstepaction"]
> [Azure 搜尋服務的 OData 語法圖表](https://azuresearch.github.io/odata-syntax-diagram/#order_by_clause)

> [!NOTE]
> 請參閱[Azure 搜尋服務的 OData 運算式語法參考](search-query-odata-syntax-reference.md)的完整 EBNF。

每個子句已排序準則，並且選擇性地加排序方向 (`asc`遞增或`desc`表示遞減)。 如果您未指定方向，則預設值為遞增。 排序條件可以是路徑`sortable`欄位或呼叫[ `geo.distance` ](search-query-odata-geo-spatial-functions.md)或[ `search.score` ](search-query-odata-search-score-function.md)函式。

如果多個文件有相同的排序準則和`search.score`函式不會使用 (例如，如果您依數值排序時，才`Rating`欄位，而且三份文件都有 4 的評等)，繫結將無法使用，依照文件分數以遞減順序。 文件分數相同時 （例如，當沒有任何要求中指定的全文檢索搜尋查詢），然後繫結的文件的相對順序為不定。

您可以指定多個排序準則。 運算式的順序會決定最終的排序順序。 例如，若要依照分數評等，遞減排序語法為`$orderby=search.score() desc,Rating desc`。

`geo.distance` 在 **$orderby** 中的語法與其在 **$filter** 中的語法相同。 在 **$orderby** 中使用 `geo.distance` 時，加以套用的欄位必須屬於 `Edm.GeographyPoint` 類型，而且也必須是 `sortable`。

`search.score` 在 **$orderby** 中的語法為 `search.score()`。 此函式`search.score`未採用任何參數。

## <a name="examples"></a>範例

依基本費率將飯店遞增排序：

    $orderby=BaseRate asc

先依評等將飯店遞減排序，再依基本費率遞增排序 (請記住，遞增是預設值)：

    $orderby=Rating desc,BaseRate

排序評等，然後遞增的距離，從指定的座標，依遞減的旅館：

    $orderby=Rating desc,geo.distance(Location, geography'POINT(-122.131577 47.678581)') asc

排序 hotels 以遞減順序 search.score 和評等，然後以遞增順序距離，從指定的座標。 之間使用相同的相關性分數兩間旅館和評等，會先列出最接近的其中一個：

    $orderby=search.score() desc,Rating desc,geo.distance(Location, geography'POINT(-122.131577 47.678581)') asc

## <a name="next-steps"></a>後續步驟  

- [如何使用 搜尋結果，在 Azure 搜尋服務](search-pagination-page-layout.md)
- [Azure 搜尋服務的 OData 運算式語言概觀](query-odata-filter-orderby-syntax.md)
- [Azure 搜尋服務的 OData 運算式語法參考](search-query-odata-syntax-reference.md)
- [搜尋文件 (Azure 搜尋服務 REST API)](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
