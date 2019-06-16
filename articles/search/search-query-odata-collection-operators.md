---
title: OData 集合運算子參考-Azure 搜尋服務
description: OData 集合運算子，所有，並在 Azure 搜尋服務查詢中的 lambda 運算式。
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
ms.openlocfilehash: 7afafe158732b14ebe314eeee5d015acddc55b72
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079934"
---
# <a name="odata-collection-operators-in-azure-search---any-and-all"></a>OData 集合運算子，在 Azure 搜尋服務-`any`和 `all`

在撰寫[OData 篩選運算式](query-odata-filter-orderby-syntax.md)若要使用 Azure 搜尋服務，通常很有用篩選集合的欄位。 您可以達到此目的使用`any`和`all`運算子。

## <a name="syntax"></a>語法

下列 EBNF ([擴充式 Backus-naur 形式](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) 會定義一個使用的 OData 運算式文法`any`或`all`。

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
collection_filter_expression ::=
    field_path'/all(' lambda_expression ')'
    | field_path'/any(' lambda_expression ')'
    | field_path'/any()'

lambda_expression ::= identifier ':' boolean_expression
```

互動式的語法圖表也會提供：

> [!div class="nextstepaction"]
> [Azure 搜尋服務的 OData 語法圖表](https://azuresearch.github.io/odata-syntax-diagram/#collection_filter_expression)

> [!NOTE]
> 請參閱[Azure 搜尋服務的 OData 運算式語法參考](search-query-odata-syntax-reference.md)的完整 EBNF。

有三種形式的運算式，以篩選集合。

- 前兩個逐一查看集合 欄位中，套用每個項目集合中的 lambda 運算式的形式指定的述詞。
  - 使用運算式`all`傳回`true`如果述詞的集合中的每個項目，則為 true。
  - 使用運算式`any`傳回`true`述詞是否至少一個元素的集合，則為 true。
- 第三個表單的集合篩選使用`any`而不需要 lambda 運算式來測試是否是空的集合欄位。 如果集合有任何項目，它會傳回`true`。 如果集合是空的它會傳回`false`。

A **lambda 運算式**集合中篩選條件就像是一種程式設計語言中的迴圈的主體。 它會定義一個變數，呼叫**範圍變數**，保存在反覆項目集合的目前項目。 它也會定義為篩選準則，以套用至每個項目集合的範圍變數的另一個布林運算式。

## <a name="examples"></a>範例

比對文件： 其`tags`欄位包含剛好"wifi"的字串：

    tags/any(t: t eq 'wifi')

比對文件位置的每個項目`ratings`落介於 3 到 5 （含) 之間的欄位：

    ratings/all(r: r ge 3 and r le 5)

任何在地理位置座標中的位置比對文件`locations`欄位是在給定的多邊形內：

    locations/any(loc: geo.intersects(loc, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))

比對文件的位置`rooms`欄位是空的：

    not rooms/any()

比對文件，其中的所有聊天室`rooms/amenities`欄位包含"電視 」 和`rooms/baseRate`小於 100:

    rooms/all(room: room/amenities/any(a: a eq 'tv') and room/baseRate lt 100.0)

## <a name="limitations"></a>限制

使用 lambda 運算式主體內的篩選條件運算式不是每一項功能。 限制是根據您想要篩選的集合欄位的資料類型而有所不同。 下表摘要說明的限制。

[!INCLUDE [Limitations on OData lambda expressions in Azure Search](../../includes/search-query-odata-lambda-limitations.md)]

如需這些限制，以及範例的詳細資訊，請參閱 <<c0> [ 疑難排解 Azure 搜尋服務中的集合篩選](search-query-troubleshoot-collection-filters.md)。 如需更深入了解為何這些限制存在，請參閱[了解 Azure 搜尋服務中的集合篩選](search-query-understand-collection-filters.md)。

## <a name="next-steps"></a>後續步驟  

- [Azure 搜尋服務中的篩選條件](search-filters.md)
- [Azure 搜尋服務的 OData 運算式語言概觀](query-odata-filter-orderby-syntax.md)
- [Azure 搜尋服務的 OData 運算式語法參考](search-query-odata-syntax-reference.md)
- [搜尋文件 (Azure 搜尋服務 REST API)](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
