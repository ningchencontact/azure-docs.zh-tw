---
title: OData 篩選參考-Azure 搜尋服務
description: OData 篩選語法，在 Azure 搜尋服務查詢中的語言參考。
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
ms.openlocfilehash: c44645ebcbf8808cc929bfaa0c3cb0d3ee9c90cd
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079908"
---
# <a name="odata-filter-syntax-in-azure-search"></a>Azure 搜尋服務中的 OData $filter 語法

Azure 搜尋服務會使用[OData 篩選條件運算式](query-odata-filter-orderby-syntax.md)除了全文檢索搜尋字詞的搜尋查詢套用額外的準則。 這篇文章描述的詳細資料中的篩選條件的語法。 更多一般篩選器為何，以及如何使用它們來了解特定的查詢案例的詳細資訊，請參閱[Azure 搜尋服務中的篩選器](search-filters.md)。

## <a name="syntax"></a>語法

OData 語言篩選條件是布林運算式，接著可以是其中一種數種類型的運算式，如下列 EBNF 所示 ([擴充式 Backus-naur 形式](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)):

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
boolean_expression ::=
    collection_filter_expression
    | logical_expression
    | comparison_expression
    | boolean_literal
    | boolean_function_call
    | '(' boolean_expression ')'
    | variable

/* This can be a range variable in the case of a lambda, or a field path. */
variable ::= identifier | field_path
```

互動式的語法圖表也會提供：

> [!div class="nextstepaction"]
> [Azure 搜尋服務的 OData 語法圖表](https://azuresearch.github.io/odata-syntax-diagram/#boolean_expression)

> [!NOTE]
> 請參閱[Azure 搜尋服務的 OData 運算式語法參考](search-query-odata-syntax-reference.md)的完整 EBNF。

布林運算式的類型包括：

- 使用的集合篩選條件運算式`any`或`all`。 這些適用於集合欄位篩選準則。 如需詳細資訊，請參閱 < [Azure 搜尋服務中的 OData 集合運算子](search-query-odata-collection-operators.md)。
- 邏輯結合其他使用運算子的布林運算式的運算式`and`， `or`，和`not`。 如需詳細資訊，請參閱 < [OData Azure 搜尋服務中的邏輯運算子](search-query-odata-logical-operators.md)。
- 比較運算式，這比較欄位或範圍變數，以使用運算子的常數值`eq`， `ne`， `gt`， `lt`， `ge`，和`le`。 如需詳細資訊，請參閱 < [Azure 搜尋服務中的 OData 比較運算子](search-query-odata-comparison-operators.md)。 比較運算式也會用來比較使用地理空間座標之間的距離`geo.distance`函式。 如需詳細資訊，請參閱 < [Azure 搜尋服務中的 OData 地理空間函式](search-query-odata-geo-spatial-functions.md)。
- 布林常值`true`和`false`。 這些常數時很有用有時候以程式設計方式產生的篩選，否則不容易在練習中使用，但。
- 布林值的函式，包括呼叫：
  - `geo.intersects`它會測試指定的點是否在給定的多邊形內。 如需詳細資訊，請參閱 < [Azure 搜尋服務中的 OData 地理空間函式](search-query-odata-geo-spatial-functions.md)。
  - `search.in`其比較值的清單中每個值的欄位或範圍變數。 如需詳細資訊，請參閱 < [OData `search.in` Azure 搜尋服務中的函式](search-query-odata-search-in-function.md)。
  - `search.ismatch` 和`search.ismatchscoring`，這在篩選內容中執行全文檢索搜尋作業。 如需詳細資訊，請參閱 < [OData 全文檢索搜尋功能，Azure 搜尋服務中的](search-query-odata-full-text-search-functions.md)。
- 欄位路徑或範圍變數的型別`Edm.Boolean`。 例如，如果您的索引具有名為的布林值欄位`IsEnabled`和您想要傳回所有文件，此欄位所在`true`，您的篩選運算式只可以是名稱`IsEnabled`。
- 括號括住的布林運算式。 若要明確判斷作業在篩選中的順序，可協助使用括號。 如需預設的優先順序的 OData 運算子的詳細資訊，請參閱下一節。

### <a name="operator-precedence-in-filters"></a>在篩選中的運算子優先順序

如果您撰寫其子的運算式周圍沒有括號的篩選運算式時，Azure 搜尋服務會評估其依據的運算子優先順序規則集合。 這些規則根據哪個運算子來結合子運算式。 下表列出的順序從最高到最低優先順序的運算子的群組：

| 群組 | 運算子 |
| --- | --- |
| 邏輯運算子 | `not` |
| 比較運算子 | `eq`、`ne`、`gt`、`lt`、`ge`、`le` |
| 邏輯運算子 | `and` |
| 邏輯運算子 | `or` |

上表中較高的運算子會 「 繫結更緊密地 」 至其高於其他運算子的運算元。 例如，`and`的優先順序高於`or`，而且比較運算子的優先順序高於其中之一，因此下列兩個運算式相等：

    Rating gt 0 and Rating lt 3 or Rating gt 7 and Rating lt 10
    ((Rating gt 0) and (Rating lt 3)) or ((Rating gt 7) and (Rating lt 10))

`not`運算子優先順序最高的-甚至更高的比較運算子。 這就是為什麼如果您嘗試寫入篩選器，像這樣：

    not Rating gt 5

您會收到這個錯誤訊息：

    Invalid expression: A unary operator with an incompatible type was detected. Found operand type 'Edm.Int32' for operator kind 'Not'.

因為相關聯的運算子，就會發生這個錯誤只`Rating`欄位中，也就是型別的`Edm.Int32`，而不是使用整個比較運算式。 修正方法是將運算元`not`括號括住：

    not (Rating gt 5)

<a name="bkmk_limits"></a>

### <a name="filter-size-limitations"></a>篩選大小限制

可傳送至 Azure 搜尋服務的篩選運算式在大小和複雜度方面有一些限制。 這些限制大致上取決於篩選運算式中的子句數目。 理想的指導方針是，如果您有數百個子句時，您可能會超過限制。 我們建議您設計您的應用程式的方式，其不會產生無大小限制的篩選條件。

> [!TIP]
> 使用[`search.in`函式](search-query-odata-search-in-function.md)而不是長 disjunctions 相等的比較可協助避免篩選子句限制，因為函式呼叫會計算為單一子句。

## <a name="examples"></a>範例

尋找小於美金 200 元，達到或超過 4 分級與至少一個空間，且基本費率的所有旅館：

    $filter=Rooms/any(room: room/BaseRate lt 200.0) and Rating ge 4

尋找"Sea 檢視 Motel"以外重新裝修自 2010年的所有旅館：

    $filter=HotelName ne 'Sea View Motel' and LastRenovationDate ge 2010-01-01T00:00:00Z

尋找所有在 2010年或更新版本，整修的旅館。 日期時間常值包含太平洋標準時間時區資訊：  

    $filter=LastRenovationDate ge 2010-01-01T00:00:00-08:00

尋找包含停車且所有聊天室與非吸煙都所在的所有旅館：

    $filter=ParkingIncluded and Rooms/all(room: not room/SmokingAllowed)

 \- 或 -  

    $filter=ParkingIncluded eq true and Rooms/all(room: room/SmokingAllowed eq false)

尋找所有屬豪華等級，或附停車位且獲評為 5 星級的飯店：  

    $filter=(Category eq 'Luxury' or ParkingIncluded eq true) and Rating eq 5

至少一個房間內尋找的標記"wifi"的所有旅館 (其中每個聊天室都有標籤儲存在`Collection(Edm.String)`欄位):  

    $filter=Rooms/any(room: room/Tags/any(tag: tag eq 'wifi'))

尋找有任何房間的所有旅館：  

    $filter=Rooms/any()

尋找沒有聊天室的所有旅館：

    $filter=not Rooms/any()

尋找指定的參考點 10 公里內的所有旅館 (其中`Location`是型別的欄位`Edm.GeographyPoint`):

    $filter=geo.distance(Location, geography'POINT(-122.131577 47.678581)') le 10

尋找在指定的檢視區所謂的多邊形內的所有旅館 (其中`Location`是 Edm.GeographyPoint 類型的欄位)。 多邊形必須閉合，這表示第一個和最後一個點組必須相同。 此外，[點必須採用逆時針順序列出](https://docs.microsoft.com/rest/api/searchservice/supported-data-types#Anchor_1)。

    $filter=geo.intersects(Location, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))')

尋找其中的 [描述] 欄位為 null 的所有旅館。 欄位會是 null，如果它永遠不會設定，或如果已經明確設定為 null:  

    $filter=Description eq null

尋找所有旅館名稱等於 '海檢視 motel' 或 '預算旅館'）。 這些片語可以包含空格，以及空間預設分隔符號。 您可以指定單引號括住的替代的分隔符號，做為第三個字串參數：  

    $filter=search.in(HotelName, 'Sea View motel,Budget hotel', ',')

尋找具有名稱的所有旅館等於 '海檢視 motel' 或 '預算旅館' 以分隔 ' |'):  

    $filter=search.in(HotelName, 'Sea View motel|Budget hotel', '|')

尋找所有聊天室其中都有標籤 'wifi' 或 '發出啪一聲' 的所有旅館：

    $filter=Rooms/any(room: room/Tags/any(tag: search.in(tag, 'wifi, tub'))

在標記中找到相符項目在集合中，例如 '加熱的毛巾機架' 或 'hairdryer 包含' 片語。

    $filter=Rooms/any(room: room/Tags/any(tag: search.in(tag, 'heated towel racks,hairdryer included', ','))

尋找含有「海濱」一詞的文件。 此篩選查詢等同於使用 `search=waterfront` 的[搜尋要求](https://docs.microsoft.com/rest/api/searchservice/search-documents)。

    $filter=search.ismatchscoring('waterfront')

尋找含有「青年旅館」一詞、且評分為 4 或以上的文件，或含有「汽車旅館」一詞、且評分為 5 的文件。 此要求無法表示不含`search.ismatchscoring`函式，因為它會結合使用的篩選作業全文檢索搜尋`or`。

    $filter=search.ismatchscoring('hostel') and rating ge 4 or search.ismatchscoring('motel') and rating eq 5

尋找不含「豪華」一詞的文件。

    $filter=not search.ismatch('luxury')

尋找含有「海景」一詞或評分為 5 的文件。 `search.ismatchscoring` 查詢只會對欄位 `HotelName` 和 `Description` 執行。 比對分離的第二個子句的文件將會傳回 – 旅館`Rating`等於 5。 這些文件將不會傳回分數等於零，以便清除它們不符合任何評分的組件的運算式。

    $filter=search.ismatchscoring('"ocean view"', 'Description,HotelName') or Rating eq 5

詞彙"hotel"和 「 機場 」 是不超過五個字相差在描述中，以及所有聊天室都的非吸煙的位置，請尋找旅館。 此查詢會使用[完整 Lucene 查詢語言](query-lucene-syntax.md)。

    $filter=search.ismatch('"hotel airport"~5', 'Description', 'full', 'any') and not Rooms/any(room: room/SmokingAllowed)

## <a name="next-steps"></a>後續步驟  

- [Azure 搜尋服務中的篩選條件](search-filters.md)
- [Azure 搜尋服務的 OData 運算式語言概觀](query-odata-filter-orderby-syntax.md)
- [Azure 搜尋服務的 OData 運算式語法參考](search-query-odata-syntax-reference.md)
- [搜尋文件 (Azure 搜尋服務 REST API)](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
