---
title: OData search.score 函式參考-Azure 搜尋服務
description: 在 Azure 搜尋服務查詢的 OData search.score 函式。
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
ms.openlocfilehash: dc444216c4677b9970b867e92aa5ae259a197220
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079687"
---
# <a name="odata-searchscore-function-in-azure-search"></a>OData `search.score` Azure 搜尋服務中的函式

當您將查詢傳送到 Azure 搜尋服務，而不需要[ **$orderby**參數](search-query-odata-orderby.md)，會依相關性分數以遞減順序排序傳回的結果。 即使當您使用 **$orderby**，相關性分數會用來中斷繫結，根據預設。 不過，有時很有用的初始排序準則，以及其他準則的相關性分數作為繫結器。 `search.score`函式可讓您執行這項操作。

## <a name="syntax"></a>語法

`search.score` 在 **$orderby** 中的語法為 `search.score()`。 `search.score` 函式不接受任何參數。 它可以搭配`asc`或是`desc`排序次序的規範，就像中的其他子句 **$orderby**參數。 它可以出現在任何地方的排序準則的清單。

## <a name="example"></a>範例

排序以遞減順序的旅館`search.score`和`rating`，然後在指定座標的遞增順序距離，以便之間具有相同的評等的兩間旅館，一個最接近會先列出：

    search.score() desc,rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc

## <a name="next-steps"></a>後續步驟  

- [Azure 搜尋服務的 OData 運算式語言概觀](query-odata-filter-orderby-syntax.md)
- [Azure 搜尋服務的 OData 運算式語法參考](search-query-odata-syntax-reference.md)
- [搜尋文件 (Azure 搜尋服務 REST API)](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
