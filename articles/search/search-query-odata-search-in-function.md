---
title: OData search.in 函式參考-Azure 搜尋服務
description: 在 Azure 搜尋服務查詢的 OData search.in 函式。
ms.date: 06/13/2019
services: search
ms.service: search
ms.topic: conceptual
author: brjohnstmsft
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
ms.openlocfilehash: a61291e547021077341a5f1b3db7422afa5b9440
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/28/2019
ms.locfileid: "67449976"
---
# <a name="odata-searchin-function-in-azure-search"></a>OData `search.in` Azure 搜尋服務中的函式

中的常見案例[OData 篩選條件運算式](query-odata-filter-orderby-syntax.md)是檢查每份文件中的單一欄位是否等於其中一個許多可能的值。 比方說，這是實作某些應用程式的方式[安全性調整](search-security-trimming-for-azure-search.md)-藉由檢查包含一或多個主體的 Sid，針對表示發出查詢之使用者的主體識別碼清單的欄位。 其中一種方式撰寫如下的查詢是使用[ `eq` ](search-query-odata-comparison-operators.md)並[ `or` ](search-query-odata-logical-operators.md)運算子：

    group_ids/any(g: g eq '123' or g eq '456' or g eq '789')

不過，還有較短的方法來撰寫本文時，使用`search.in`函式：

    group_ids/any(g: search.in(g, '123, 456, 789'))

> [!IMPORTANT]
> 除了更簡短、 更方便閱讀，使用`search.in`也會提供[效益](#bkmk_performance)，並避免某些[大小限制的篩選條件](search-query-odata-filter.md#bkmk_limits)時有數百個或甚至數千個值若要包含在篩選中。 基於這個理由，強烈建議使用`search.in`而不是更複雜的等號比較運算式的分離。

> [!NOTE]
> OData 標準 4.01 版最近導入了[`in`運算子](https://docs.oasis-open.org/odata/odata/v4.01/cs01/part2-url-conventions/odata-v4.01-cs01-part2-url-conventions.html#_Toc505773230)，其中包含類似的行為與`search.in`Azure 搜尋服務中的函式。 不過，Azure 搜尋服務不支援這個運算子，因此您必須使用`search.in`函式。

## <a name="syntax"></a>語法

下列 EBNF ([擴充式 Backus-naur 形式](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) 定義的文法`search.in`函式：

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
search_in_call ::=
    'search.in(' variable ',' string_literal(',' string_literal)? ')'
```

互動式的語法圖表也會提供：

> [!div class="nextstepaction"]
> [Azure 搜尋服務的 OData 語法圖表](https://azuresearch.github.io/odata-syntax-diagram/#search_in_call)

> [!NOTE]
> 請參閱[Azure 搜尋服務的 OData 運算式語法參考](search-query-odata-syntax-reference.md)的完整 EBNF。

`search.in`函式會測試是否為指定的字串欄位或範圍變數就等於其中一個指定值的清單。 變數與清單中的每個值之間的等號比較區分大小寫的方式，與相同的方式決定`eq`運算子。 因此，像 `search.in(myfield, 'a, b, c')` 這樣的運算式會等同於 `myfield eq 'a' or myfield eq 'b' or myfield eq 'c'`，差別在於 `search.in` 所產生的效果會好得多。

有兩個多載`search.in`函式：

- `search.in(variable, valueList)`
- `search.in(variable, valueList, delimiters)`

下表中定義的參數：

| 參數名稱 | 類型 | 描述 |
| --- | --- | --- |
| `variable` | `Edm.String` | 字串欄位參考 (或透過在案例中的字串集合欄位的範圍變數所在`search.in`使用於內部`any`或`all`運算式)。 |
| `valueList` | `Edm.String` | 字串，包含要比對的值以分隔的清單`variable`參數。 如果`delimiters`參數未指定，預設的分隔符號是空格和逗號。 |
| `delimiters` | `Edm.String` | 字串，其中每個字元會被視為分隔符號剖析時`valueList`參數。 此參數的預設值是`' ,'`表示空格及/或以逗點隔開的任何值會被分開。 如果您需要使用空格和逗點以外的分隔符號，因為您的值包含這些字元，您可以指定替代的分隔符號例如`'|'`此參數中。 |

<a name="bkmk_performance"></a>

### <a name="performance-of-searchin"></a>效能 `search.in`

如果您使用 `search.in`，當第二個參數所含的清單有數百或數千個值時，預期的回應時間應該不到一秒。 沒有任何明確的限制，您可以傳遞給項目數目`search.in`，不過您仍然會受到最大要求大小。 不過，當值的數目增加時，延遲也會隨之增長。

## <a name="examples"></a>範例

尋找具有名稱的所有旅館 '海檢視 motel' 或 '預算旅館' 相同。 片語可以包含空格，這是預設的分隔符號。 您可以指定單引號括住的替代的分隔符號，做為第三個字串參數：  

    search.in(HotelName, 'Sea View motel,Budget hotel', ',')

尋找具有名稱的所有旅館等於 '海檢視 motel' 或 '預算旅館' 以分隔 ' |'):

    search.in(HotelName, 'Sea View motel|Budget hotel', '|')

尋找與有標籤 'wifi' 或 '發出啪一聲' 的聊天室的所有旅館：

    Rooms/any(room: room/Tags/any(tag: search.in(tag, 'wifi, tub')))

在標記中找到相符項目在集合中，例如 '加熱的毛巾機架' 或 'hairdryer 包含' 片語。

    Rooms/any(room: room/Tags/any(tag: search.in(tag, 'heated towel racks,hairdryer included', ','))

尋找沒有標記 'motel' 或 '木屋給毀' 的所有旅館：

    Tags/all(tag: not search.in(tag, 'motel, cabin'))

## <a name="next-steps"></a>後續步驟  

- [Azure 搜尋服務中的篩選條件](search-filters.md)
- [Azure 搜尋服務的 OData 運算式語言概觀](query-odata-filter-orderby-syntax.md)
- [Azure 搜尋服務的 OData 運算式語法參考](search-query-odata-syntax-reference.md)
- [搜尋文件 (Azure 搜尋服務 REST API)](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
