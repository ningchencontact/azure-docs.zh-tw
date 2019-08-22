---
title: OData 搜尋。計分函數參考-Azure 搜尋服務
description: 'OData 搜尋: Azure 搜尋服務查詢中的計分函數。'
ms.date: 06/13/2019
services: search
ms.service: search
ms.topic: conceptual
author: brjohnstmsft
ms.author: brjohnst
manager: nitinme
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
ms.openlocfilehash: b6bf56a61ca685b306a15e474623336216ba531b
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/20/2019
ms.locfileid: "69647524"
---
# <a name="odata-searchscore-function-in-azure-search"></a>Azure 搜尋服務`search.score`中的 OData 函式

當您將查詢傳送至沒有[ **$orderby**參數](search-query-odata-orderby.md)的 Azure 搜尋服務時, 傳回的結果會依相關性分數以遞減順序排序。 即使您使用 **$orderby**, 預設也會使用相關性分數來中斷系結。 不過, 使用相關性分數做為初始排序準則, 以及一些其他準則做為系結工具有時會很有用。 `search.score`函式可讓您執行此動作。

## <a name="syntax"></a>語法

`search.score` 在 **$orderby** 中的語法為 `search.score()`。 `search.score` 函式不接受任何參數。 它可以與`asc`或`desc`排序次序規範搭配使用, 就像 **$orderby**參數中的任何其他子句一樣。 它可以出現在排序條件清單中的任何位置。

## <a name="example"></a>範例

以遞減的順序依`search.score`和`rating`排序飯店, 然後依照指定座標的遞增順序, 讓兩個飯店具有相同的評等, 最先列出最接近的旅館:

    search.score() desc,rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc

## <a name="next-steps"></a>後續步驟  

- [Azure 搜尋服務的 OData 運算式語言總覽](query-odata-filter-orderby-syntax.md)
- [Azure 搜尋服務的 OData 運算式語法參考](search-query-odata-syntax-reference.md)
- [搜尋文件 (Azure 搜尋服務 REST API)](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
