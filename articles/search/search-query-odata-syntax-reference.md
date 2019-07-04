---
title: OData 運算式語法參考-Azure 搜尋服務
description: 正式文法和語法規格，Azure 搜尋服務查詢中的 OData 運算式。
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
ms.openlocfilehash: ebe41ba61ac5136900328db9c35acb8551dcd5b2
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/28/2019
ms.locfileid: "67428648"
---
# <a name="odata-expression-syntax-reference-for-azure-search"></a>Azure 搜尋服務的 OData 運算式語法參考

Azure 搜尋服務會使用[OData 運算式](https://docs.oasis-open.org/odata/odata/v4.01/odata-v4.01-part2-url-conventions.html)作為整個 API 的參數。 OData 運算式用於大多數情況下，`$orderby`和`$filter`參數。 這些運算式可能很複雜，其中包含多個子句、 函數和運算子的。 不過，甚至是簡單的 OData 運算式，例如 Azure 搜尋服務 REST API 的許多地方使用路徑的屬性。 比方說，路徑運算式用來參考的複雜的欄位，隨處在 API 中，例如當列出子欄位中的子欄位[建議工具](index-add-suggesters.md)，則[計分函數](index-add-scoring-profiles.md)，則`$select`參數或甚至[Lucene 查詢中加入欄位的搜尋](query-lucene-syntax.md)。

本文說明所有這些形式的 OData 運算式使用正式文法。 另外還有[互動式圖表](#syntax-diagram)協助以視覺化方式探索文法。

## <a name="formal-grammar"></a>正式文法

我們可以說明使用 EBNF 的 Azure 搜尋服務所支援的 OData 語言子集 ([擴充式 Backus-naur 形式](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) 文法。 「 由上而下 」，最複雜的運算式中，從開始，它們分解成更基本的運算式時，會列出規則。 在頂端的文法規則對應到特定的參數，Azure 搜尋服務 REST api:

- [`$filter`](search-query-odata-filter.md)定義由`filter_expression`規則。
- [`$orderby`](search-query-odata-orderby.md)定義由`order_by_expression`規則。
- [`$select`](search-query-odata-select.md)定義由`select_expression`規則。
- 欄位所定義的路徑`field_path`規則。 欄位路徑中使用 API。 它們會參考的索引，其中一個最上層欄位或 子欄位與一或多個[複雜欄位](search-howto-complex-data-types.md)祖系。

EBNF 可瀏覽後[語法圖](https://en.wikipedia.org/wiki/Syntax_diagram)，可讓您以互動方式探索其規則之間的文法和關聯性。

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
/* Top-level rules */

filter_expression ::= boolean_expression

order_by_expression ::= order_by_clause(',' order_by_clause)*

select_expression ::= '*' | field_path(',' field_path)*

field_path ::= identifier('/'identifier)*


/* Shared base rules */

identifier ::= [a-zA-Z_][a-zA-Z_0-9]*


/* Rules for $orderby */

order_by_clause ::= (field_path | sortable_function) ('asc' | 'desc')?

sortable_function ::= geo_distance_call | 'search.score()'


/* Rules for $filter */

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

collection_filter_expression ::=
    field_path'/all(' lambda_expression ')'
    | field_path'/any(' lambda_expression ')'
    | field_path'/any()'

lambda_expression ::= identifier ':' boolean_expression

logical_expression ::=
    boolean_expression ('and' | 'or') boolean_expression
    | 'not' boolean_expression

comparison_expression ::= 
    variable_or_function comparison_operator constant | 
    constant comparison_operator variable_or_function

variable_or_function ::= variable | function_call

comparison_operator ::= 'gt' | 'lt' | 'ge' | 'le' | 'eq' | 'ne'


/* Rules for constants and literals */

constant ::=
    string_literal
    | date_time_offset_literal
    | integer_literal
    | float_literal
    | boolean_literal
    | 'null'

string_literal ::= "'"([^'] | "''")*"'"

date_time_offset_literal ::= date_part'T'time_part time_zone

date_part ::= year'-'month'-'day

time_part ::= hour':'minute(':'second('.'fractional_seconds)?)?

zero_to_fifty_nine ::= [0-5]digit

digit ::= [0-9]

year ::= digit digit digit digit

month ::= '0'[1-9] | '1'[0-2]

day ::= '0'[1-9] | [1-2]digit | '3'[0-1]

hour ::= [0-1]digit | '2'[0-3]

minute ::= zero_to_fifty_nine

second ::= zero_to_fifty_nine

fractional_seconds ::= integer_literal

time_zone ::= 'Z' | sign hour':'minute

sign ::= '+' | '-'

/* In practice integer literals are limited in length to the precision of
the corresponding EDM data type. */
integer_literal ::= digit+

float_literal ::=
    sign? whole_part fractional_part? exponent?
    | 'NaN'
    | '-INF'
    | 'INF'

whole_part ::= integer_literal

fractional_part ::= '.'integer_literal

exponent ::= 'e' sign? integer_literal

boolean_literal ::= 'true' | 'false'


/* Rules for functions */

function_call ::=
    geo_distance_call |
    boolean_function_call

geo_distance_call ::=
    'geo.distance(' variable ',' geo_point ')'
    | 'geo.distance(' geo_point ',' variable ')'

geo_point ::= "geography'POINT(" lon_lat ")'"

lon_lat ::= float_literal ' ' float_literal

boolean_function_call ::=
    geo_intersects_call |
    search_in_call |
    search_is_match_call

geo_intersects_call ::=
    'geo.intersects(' variable ',' geo_polygon ')'

/* You need at least four points to form a polygon, where the first and
last points are the same. */
geo_polygon ::=
    "geography'POLYGON((" lon_lat ',' lon_lat ',' lon_lat ',' lon_lat_list "))'"

lon_lat_list ::= lon_lat(',' lon_lat)*

search_in_call ::=
    'search.in(' variable ',' string_literal(',' string_literal)? ')'

/* Note that it is illegal to call search.ismatch or search.ismatchscoring
from inside a lambda expression. */
search_is_match_call ::=
    'search.ismatch'('scoring')?'(' search_is_match_parameters ')'

search_is_match_parameters ::=
    string_literal(',' string_literal(',' query_type ',' search_mode)?)?

query_type ::= "'full'" | "'simple'"

search_mode ::= "'any'" | "'all'"
```

## <a name="syntax-diagram"></a>語法圖

若要以視覺化方式探索 Azure 搜尋服務所支援的 OData 語言文法，請嘗試互動式的語法圖：

> [!div class="nextstepaction"]
> [Azure 搜尋服務的 OData 語法圖表](https://azuresearch.github.io/odata-syntax-diagram/)

## <a name="see-also"></a>請參閱  

- [Azure 搜尋服務中的篩選條件](search-filters.md)
- [搜尋文件 (Azure 搜尋服務 REST API)](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
- [Lucene 查詢語法](query-lucene-syntax.md)
- [Azure 搜尋服務中的簡單查詢語法](query-simple-syntax.md)
