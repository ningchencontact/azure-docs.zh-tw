---
title: 疑難排解 OData 集合篩選器-Azure 搜尋服務
description: 疑難排解在 Azure 搜尋服務查詢的 OData 集合篩選錯誤。
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
ms.openlocfilehash: c7fa00c82eea03a50bae22fcb1ad16e230aa5bcb
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079622"
---
# <a name="troubleshooting-odata-collection-filters-in-azure-search"></a>疑難排解 Azure 搜尋服務中的 OData 集合篩選器

若要[篩選](query-odata-filter-orderby-syntax.md)在 Azure 搜尋服務中的集合欄位，您可以使用[`any`並`all`運算子](search-query-odata-collection-operators.md)搭配**lambda 運算式**。 Lambda 運算式是套用至集合的每個元素的子篩選。

Lambda 運算式內使用的篩選條件運算式不是每一項功能。 哪些功能可依您想要篩選的集合欄位的資料類型而有所不同。 如果您嘗試在該內容中不支援的 lambda 運算式中使用的功能，這可以導致錯誤。 如果嘗試透過集合欄位中撰寫複雜的篩選條件時，會發生這類錯誤，這篇文章將協助您疑難排解問題。

## <a name="common-collection-filter-errors"></a>常見的集合篩選錯誤

下表列出您在嘗試執行集合篩選條件時可能會遇到的錯誤。 當您使用的篩選條件運算式不支援 lambda 運算式內的一項功能時，就會發生這些錯誤。 每個錯誤提供一些指導方針方式重寫您的篩選條件，以避免發生錯誤。 這個表格也會包含本文提供如何避免這個錯誤的詳細資訊的相關區段的連結。

| 錯誤訊息 | 情況 | 如需詳細資訊，請參閱 |
| --- | --- | --- |
| 函式 'ismatch' 沒有任何繫結至範圍變數的參數 '。 只有繫結支援 lambda 運算式 （'any' 或 'all'） 內參考的欄位。 請變更您的篩選條件，使 'ismatch' 函式的 lambda 運算式外部，然後再試一次。 | 使用`search.ismatch`或`search.ismatchscoring`在 lambda 運算式 | [篩選複雜的集合規則](#bkmk_complex) |
| 無效的 lambda 運算式。 找到相等或不等的測試，逐一查看的型別 collection （edm.string） 欄位的 lambda 運算式中，其中必須相反。 對於 'any'，請使用 'search.in(...)' 之 'x eq y' 格式的運算式。 為 'all'，請使用 'x ne y 格式 '，'不 (x eq y)' 或 '不是 search.in(...)' 的運算式。 | 篩選類型的欄位 `Collection(Edm.String)` | [篩選字串的集合規則](#bkmk_strings) |
| 無效的 lambda 運算式。 找到不支援的一種複雜的布林運算式。 對於 'any'，請使用 ' Or 的 ANDs'，也稱為 Disjunctive Normal Form 的運算式。 例如： '(a and b) 或 （c 和 d） ' a，b，c 和 d 會比較或等號比較子的運算式。 為 'all'，請使用 ' And 的 Or '，也就是連結的一般形式的運算式。 例如： '(a or b) 並 （c 或 d） ' a，b，c 和 d 會比較或不等比較子的運算式。 比較運算式的範例: 'x gt 5'、' le 2 倍。 等號比較運算式的範例: ' x eq 5'。 不等比較運算式的範例: ' x ne 5'。 | 篩選的欄位型別的`Collection(Edm.DateTimeOffset)`， `Collection(Edm.Double)`， `Collection(Edm.Int32)`，或 `Collection(Edm.Int64)` | [篩選可比較的集合規則](#bkmk_comparables) |
| 無效的 lambda 運算式。 找到不支援的使用 geo.distance() 或 geo.intersects() 逐一查看的型別 Collection(Edm.GeographyPoint) 欄位在 lambda 運算式。 'Any'，請確定您比較 geo.distance() 使用 'lt' 或 'le' 運算子，並確定 geo.intersects() 的任何使用量不會否定運算。 為 'all'，請確定您比較 geo.distance() 使用 'gt' 或 'ge' 運算子，並確定 geo.intersects() 的任何用量，就會失效。 | 篩選類型的欄位 `Collection(Edm.GeographyPoint)` | [用於篩選 GeographyPoint 集合規則](#bkmk_geopoints) |
| 無效的 lambda 運算式。 在逐一查看欄位的型別 Collection(Edm.GeographyPoint) 的 lambda 運算式中不支援複雜的布林運算式。 為 'any'，請加入具有的子運算式 ';'和' 不支援。 為 'all'，請加入具有的子運算式 '和';'或' 不支援。 | 篩選的欄位型別的`Collection(Edm.String)`或 `Collection(Edm.GeographyPoint)` | [篩選字串的集合規則](#bkmk_strings) <br/><br/> [用於篩選 GeographyPoint 集合規則](#bkmk_geopoints) |
| 無效的 lambda 運算式。 找到比較運算子 （其中 'lt'、 'le'、 'gt'、 或 'ge'）。 逐一查看的型別 collection （edm.string） 欄位的 lambda 運算式中允許只是等號比較運算子。 對於 'any'，請使用 'x eq y' 格式的運算式。 為 'all'，請使用格式 'x ne y' 或 '不 (x eq y)' 的運算式。 | 篩選類型的欄位 `Collection(Edm.String)` | [篩選字串的集合規則](#bkmk_strings) |

<a name="bkmk_examples"></a>

## <a name="how-to-write-valid-collection-filters"></a>如何寫入有效的集合篩選器

撰寫有效的集合篩選條件的規則是每個資料類型不同。 下列各節描述的規則顯示的範例的篩選器的支援功能，以及這不是：

- [篩選字串的集合規則](#bkmk_strings)
- [篩選布林值的集合規則](#bkmk_bools)
- [用於篩選 GeographyPoint 集合規則](#bkmk_geopoints)
- [篩選可比較的集合規則](#bkmk_comparables)
- [篩選複雜的集合規則](#bkmk_complex)

<a name="bkmk_strings"></a>

## <a name="rules-for-filtering-string-collections"></a>篩選字串的集合規則

Lambda 運算式中的字串集合，可以使用唯一的比較運算子不會`eq`和`ne`。

> [!NOTE]
> Azure 搜尋服務不支援`lt` / `le` / `gt` / `ge`運算子的字串是否內部或外部 lambda 運算式。

主體`any`可以只測試是否相等的主體時`all`只能測試是否不相等。

它也可透過多個運算式結合`or`的主體中`any`，和透過`and`的主體中`all`。 由於`search.in`函式相當於結合使用的相等檢查`or`，它也允許的主體中`any`。 相反地，`not search.in`允許的主體中`all`。

比方說，這些運算式可：

- `tags/any(t: t eq 'books')`
- `tags/any(t: search.in(t, 'books, games, toys'))`
- `tags/all(t: t ne 'books')`
- `tags/all(t: not (t eq 'books'))`
- `tags/all(t: not search.in(t, 'books, games, toys'))`
- `tags/any(t: t eq 'books' or t eq 'games')`
- `tags/all(t: t ne 'books' and not (t eq 'games'))`

雖然這些運算式不允許：

- `tags/any(t: t ne 'books')`
- `tags/any(t: not search.in(t, 'books, games, toys'))`
- `tags/all(t: t eq 'books')`
- `tags/all(t: search.in(t, 'books, games, toys'))`
- `tags/any(t: t eq 'books' and t ne 'games')`
- `tags/all(t: t ne 'books' or not (t eq 'games'))`

<a name="bkmk_bools"></a>

## <a name="rules-for-filtering-boolean-collections"></a>篩選布林值的集合規則

型別`Edm.Boolean`僅支援`eq`和`ne`運算子。 因此，它不太合理允許結合檢查具有相同的範圍變數這類子句`and` / `or`因為，一律會導致 tautologies 或互相矛盾。

以下是允許的布林值集合上的篩選器的一些範例：

- `flags/any(f: f)`
- `flags/all(f: f)`
- `flags/any(f: f eq true)`
- `flags/any(f: f ne true)`
- `flags/all(f: not f)`
- `flags/all(f: not (f eq true))`

不同於字串集合，布林值的集合，請在 lambda 運算式的型別運算子可用於沒有限制。 兩者`eq`並`ne`可用的主體中`any`或`all`。

布林值的集合不允許運算式如下所示：

- `flags/any(f: f or not f)`
- `flags/any(f: f or f)`
- `flags/all(f: f and not f)`
- `flags/all(f: f and f eq true)`

<a name="bkmk_geopoints"></a>

## <a name="rules-for-filtering-geographypoint-collections"></a>用於篩選 GeographyPoint 集合規則

型別的值`Edm.GeographyPoint`集合中無法比較直接向彼此。 相反地，它們必須使用做為參數`geo.distance`和`geo.intersects`函式。 `geo.distance`函式接著必須相較於使用其中一個比較運算子的距離值`lt`， `le`， `gt`，或`ge`。 這些規則也適用於非集合 Edm.GeographyPoint 欄位。

像是字串集合`Edm.GeographyPoint`集合有一些可以如何使用並結合不同類型的 lambda 運算式中的地理空間函式的規則：

- 您可以搭配使用的比較運算子`geo.distance`函式取決於 lambda 運算式的類型。 針對`any`，您可以只使用`lt`或`le`。 針對`all`，您可以只使用`gt`或`ge`。 運算式涉及變換正負號`geo.distance`，但是您必須變更比較運算子 (`geo.distance(...) lt x`會變成`not (geo.distance(...) ge x)`並`geo.distance(...) le x`會變成`not (geo.distance(...) gt x)`)。
- 本文中`all`，則`geo.intersects`函式必須要變為負值。 相反地，在主體`any`，則`geo.intersects`函式必須不要變為負值。
- 本文中`any`，可以使用結合地理空間運算式`or`。 本文中`all`，這類運算式可以使用結合`and`。

因類似原因的上述限制是以字串集合的等號/不等限制形式存在。 請參閱[了解 OData 集合篩選器，在 Azure 搜尋服務](search-query-understand-collection-filters.md)的更深入的了解這些原因。

以下是上的 篩選器的一些範例`Edm.GeographyPoint`允許的集合：

- `locations/any(l: geo.distance(l, geography'POINT(-122 49)') lt 10)`
- `locations/any(l: not (geo.distance(l, geography'POINT(-122 49)') ge 10) or geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`
- `locations/all(l: geo.distance(l, geography'POINT(-122 49)') ge 10 and not geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`

如下所示的運算式不可用於`Edm.GeographyPoint`集合：

- `locations/any(l: l eq geography'POINT(-122 49)')`
- `locations/any(l: not geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`
- `locations/all(l: geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`
- `locations/any(l: geo.distance(l, geography'POINT(-122 49)') gt 10)`
- `locations/all(l: geo.distance(l, geography'POINT(-122 49)') lt 10)`
- `locations/any(l: geo.distance(l, geography'POINT(-122 49)') lt 10 and geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`
- `locations/all(l: geo.distance(l, geography'POINT(-122 49)') le 10 or not geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`

<a name="bkmk_comparables"></a>

## <a name="rules-for-filtering-comparable-collections"></a>篩選可比較的集合規則

本節適用於下列的資料類型：

- `Collection(Edm.DateTimeOffset)`
- `Collection(Edm.Double)`
- `Collection(Edm.Int32)`
- `Collection(Edm.Int64)`

類型，例如`Edm.Int32`並`Edm.DateTimeOffset`支援所有六個比較運算子： `eq`， `ne`， `lt`， `le`， `gt`，以及`ge`。 透過這些型別的集合的 lambda 運算式可以包含使用任何這些運算子的簡單運算式。 這同時適用於`any`和`all`。 例如，允許這些篩選條件：

- `ratings/any(r: r ne 5)`
- `dates/any(d: d gt 2017-08-24T00:00:00Z)`
- `not margins/all(m: m eq 3.5)`

不過，在這類比較的運算式如何結合更複雜的運算式，在 lambda 運算式的限制：

- 規則`any`:
  - 簡單的不相等運算式不得常見搭配任何其他的運算式。 例如，允許此運算式：
    - `ratings/any(r: r ne 5)`

    不過，這個運算式不是：
    - `ratings/any(r: r ne 5 and r gt 2)`

    然後，雖然允許這個運算式，但並不實用因為重疊的條件：
    - `ratings/any(r: r ne 5 or r gt 7)`
  - 簡單的比較運算式涉及`eq`， `lt`， `le`， `gt`，或`ge`可以結合`and` / `or`。 例如:
    - `ratings/any(r: r gt 2 and r le 5)`
    - `ratings/any(r: r le 5 or r gt 7)`
  - 比較運算式結合`and`(nor) 可以進一步使用結合`or`。 這種形式已知為的布林邏輯中 「[Disjunctive Normal Form](https://en.wikipedia.org/wiki/Disjunctive_normal_form)"（） 來表示。 例如:
    - `ratings/any(r: (r gt 2 and r le 5) or (r gt 7 and r lt 10))`
- 規則`all`:
  - 簡單的等號比較運算式無法有效地結合任何其他的運算式。 例如，允許此運算式：
    - `ratings/all(r: r eq 5)`

    不過，這個運算式不是：
    - `ratings/all(r: r eq 5 or r le 2)`

    然後，雖然允許這個運算式，但並不實用因為重疊的條件：
    - `ratings/all(r: r eq 5 and r le 7)`
  - 簡單的比較運算式涉及`ne`， `lt`， `le`， `gt`，或`ge`可以結合`and` / `or`。 例如:
    - `ratings/all(r: r gt 2 and r le 5)`
    - `ratings/all(r: r le 5 or r gt 7)`
  - 比較運算式結合`or`(disjunctions) 可以進一步使用結合`and`。 這種形式已知為的布林邏輯中 「[連結的一般形式](https://en.wikipedia.org/wiki/Conjunctive_normal_form)」 (CNF)。 例如:
    - `ratings/all(r: (r le 2 or gt 5) and (r lt 7 or r ge 10))`

<a name="bkmk_complex"></a>

## <a name="rules-for-filtering-complex-collections"></a>篩選複雜的集合規則

處理複雜集合的 lambda 運算式支援的基本型別集合更有彈性的語法比 lambda 運算式。 您可以使用任何這類 lambda 運算式內，您可以使用外部的一個，但只有兩個例外狀況的篩選條件建構。

首先，函式`search.ismatch`和`search.ismatchscoring`lambda 運算式中不支援。 如需詳細資訊，請參閱 <<c0> [ 了解 OData 集合篩選器，在 Azure 搜尋服務](search-query-understand-collection-filters.md)。

第二，參考不是欄位*繫結*範圍變數 (所謂*自由變數*) 不允許。 例如，請考慮下列兩個對等 OData 篩選運算式：

1. `stores/any(s: s/amenities/any(a: a eq 'parking')) and details/margin gt 0.5`
1. `stores/any(s: s/amenities/any(a: a eq 'parking' and details/margin gt 0.5))`

將允許的第一個運算式，而第二個表單將會遭到拒絕因為`details/margin`沒有繫結至範圍變數`s`。

此規則也會延伸到已繫結於外部範圍變數的運算式。 這類變數是以其出現的範圍對於免費的。 比方說，第一個運算式允許，而第二個對等的運算式不允許，因為`s/name`是免費的範圍變數的範圍對於`a`:

1. `stores/any(s: s/amenities/any(a: a eq 'parking') and s/name ne 'Flagship')`
1. `stores/any(s: s/amenities/any(a: a eq 'parking' and s/name ne 'Flagship'))`

這項限制不應該是實際上的問題，因為它一律是可以建構篩選，使 lambda 運算式包含繫結的變數。

## <a name="cheat-sheet-for-collection-filter-rules"></a>集合篩選規則的功能提要

下表摘要說明用於建構有效的篩選，每個集合資料類型的規則。

[!INCLUDE [Limitations on OData lambda expressions in Azure Search](../../includes/search-query-odata-lambda-limitations.md)]

如需如何建構有效的篩選器，每個案例的範例，請參閱[如何撰寫有效的集合篩選](#bkmk_examples)。

如果情況下，寫入篩選器，了解從第一個原則規則，將可協助您超過只要記住它們，請參閱[了解 OData 集合篩選器，在 Azure 搜尋服務](search-query-understand-collection-filters.md)。

## <a name="next-steps"></a>後續步驟  

- [了解 Azure 搜尋服務中的 OData 集合篩選](search-query-understand-collection-filters.md)
- [Azure 搜尋服務中的篩選條件](search-filters.md)
- [Azure 搜尋服務的 OData 運算式語言概觀](query-odata-filter-orderby-syntax.md)
- [Azure 搜尋服務的 OData 運算式語法參考](search-query-odata-syntax-reference.md)
- [搜尋文件 (Azure 搜尋服務 REST API)](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
