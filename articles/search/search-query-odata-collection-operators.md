---
title: OData 集合運算子參考
titleSuffix: Azure Cognitive Search
description: Azure 認知搜尋查詢中的 OData 集合運算子、any 和 all 和 lambda 運算式。
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
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
ms.openlocfilehash: fdb250a844b70cef4f6941debbb1fa7450874932
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/23/2019
ms.locfileid: "72793408"
---
# <a name="odata-collection-operators-in-azure-cognitive-search---any-and-all"></a>Azure 認知搜尋中的 OData 集合運算子-`any` 和 `all`

撰寫[OData 篩選條件運算式](query-odata-filter-orderby-syntax.md)以搭配使用 Azure 認知搜尋時，篩選集合欄位通常會很有用。 您可以使用 `any` 和 `all` 運算子來達到這個目的。

## <a name="syntax"></a>語法

下列 EBNF （[Extended 巴克斯-Backus-naur 表單](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)）定義使用 `any` 或 `all`之 OData 運算式的文法。

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
collection_filter_expression ::=
    field_path'/all(' lambda_expression ')'
    | field_path'/any(' lambda_expression ')'
    | field_path'/any()'

lambda_expression ::= identifier ':' boolean_expression
```

也提供互動式語法圖：

> [!div class="nextstepaction"]
> [Azure 認知搜尋的 OData 語法圖表](https://azuresearch.github.io/odata-syntax-diagram/#collection_filter_expression)

> [!NOTE]
> 如需完整的 EBNF，請參閱[Azure 認知搜尋的 OData 運算式語法參考](search-query-odata-syntax-reference.md)。

有三種形式的運算式會篩選集合。

- 前兩個反覆運算會逐一查看集合欄位，將 lambda 運算式形式的述詞套用至集合的每個元素。
  - 如果該集合的每個專案的述詞為 true，則使用 `all` 的運算式會傳回 `true`。
  - 如果至少有一個集合元素的述詞為 true，則使用 `any` 的運算式會傳回 `true`。
- 第三種形式的集合篩選器會使用沒有 lambda 運算式的 `any` 來測試集合欄位是否為空白。 如果集合具有任何元素，則會傳回 `true`。 如果集合是空的，則會傳回 `false`。

集合篩選中的**lambda 運算式**類似于程式設計語言中的迴圈主體。 它會定義一個變數，稱為**範圍變數**，在反復專案期間保存集合的目前元素。 它也會定義另一個布林運算式，這是要套用至集合中每個元素之範圍變數的篩選準則。

## <a name="examples"></a>範例

比對 `tags` 欄位確實包含字串 "wifi" 的檔：

    tags/any(t: t eq 'wifi')

比對檔，其中 `ratings` 欄位的每個元素都介於3和5之間，包括：

    ratings/all(r: r ge 3 and r le 5)

比對檔，其中 `locations` 欄位中的任何地理座標都在指定的多邊形內：

    locations/any(loc: geo.intersects(loc, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))

符合 `rooms` 欄位空白的檔：

    not rooms/any()

符合所有房間的檔，`rooms/amenities` 欄位包含 "tv"，`rooms/baseRate` 小於100：

    rooms/all(room: room/amenities/any(a: a eq 'tv') and room/baseRate lt 100.0)

## <a name="limitations"></a>限制

在 lambda 運算式的主體內，不會提供篩選條件運算式的每個功能。 限制會根據您要篩選之集合欄位的資料類型而有所不同。 下表摘要說明這些限制。

[!INCLUDE [Limitations on OData lambda expressions in Azure Cognitive Search](../../includes/search-query-odata-lambda-limitations.md)]

如需這些限制和範例的詳細資訊，請參閱針對[Azure 認知搜尋中的集合篩選進行疑難排解](search-query-troubleshoot-collection-filters.md)。 如需這些限制為何存在的深入資訊，請參閱[瞭解 Azure 認知搜尋中的集合篩選](search-query-understand-collection-filters.md)。

## <a name="next-steps"></a>後續步驟  

- [Azure 認知搜尋中的篩選](search-filters.md)
- [Azure 認知搜尋的 OData 運算式語言總覽](query-odata-filter-orderby-syntax.md)
- [Azure 認知搜尋的 OData 運算式語法參考](search-query-odata-syntax-reference.md)
- [搜尋檔&#40;Azure 認知搜尋 REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
