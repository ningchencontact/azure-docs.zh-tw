---
title: OData 選取參考-Azure 搜尋服務
description: Azure 搜尋服務查詢中 select 語法的 OData 語言參考。
ms.date: 06/13/2019
services: search
ms.service: search
ms.topic: conceptual
author: Brjohnstmsft
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
ms.openlocfilehash: 64e9ad75d88f595ab5def6fe8b63fee9407ae0fe
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/20/2019
ms.locfileid: "69647882"
---
# <a name="odata-select-syntax-in-azure-search"></a>Azure 搜尋服務中的 OData $select 語法

 您可以使用[OData **$select**參數](query-odata-filter-orderby-syntax.md), 從 Azure 搜尋服務選擇要包含在搜尋結果中的欄位。 本文會詳細說明 **$select**的語法。 如需有關如何在呈現搜尋結果時使用 **$select**的一般資訊, 請參閱[如何在 Azure 搜尋服務中使用搜尋結果](search-pagination-page-layout.md)。

## <a name="syntax"></a>語法

**$Select**參數會決定要在查詢結果集中傳回每份檔的哪些欄位。 下列 EBNF ([Extended 巴克斯-Backus-naur 表單](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) 定義 **$select**參數的文法:

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
select_expression ::= '*' | field_path(',' field_path)*

field_path ::= identifier('/'identifier)*
```

也提供互動式語法圖:

> [!div class="nextstepaction"]
> [Azure 搜尋服務的 OData 語法圖表](https://azuresearch.github.io/odata-syntax-diagram/#select_expression)

> [!NOTE]
> 如需完整 EBNF 的 Azure 搜尋服務, 請參閱[OData 運算式語法參考](search-query-odata-syntax-reference.md)。

**$Select**參數的形式有兩種:

1. 單一星形 (`*`), 表示應該傳回所有可抓取的欄位, 或
1. 以逗號分隔的欄位路徑清單, 可識別應傳回的欄位。

使用第二個表單時, 您只能在清單中指定可抓取的欄位。

如果您在不明確指定子欄位的情況下列出複雜欄位, 所有可抓取的子欄位都會包含在查詢結果集中。 例如, 假設您的索引有一個`Address`欄位, `Street`其中`City`包含、 `Country`和子欄位全都可供取得。 如果您在`Address` **$select**中指定, 則查詢結果會包含三個子欄位。

## <a name="examples"></a>範例

`HotelName` `Rating` `City`在結果中包含`Address`、和最上層欄位, 以及的子欄位: `HotelId`

    $select=HotelId, HotelName, Rating, Address/City

範例結果可能如下所示:

```json
{
  "HotelId": "1",
  "HotelName": "Secret Point Motel",
  "Rating": 4,
  "Address": {
    "City": "New York"
  }
}
```

`Rooms` `BaseRate` `Type` `Address`將最上層欄位包含在結果中, 以及的所有子欄位, 以及集合中每個物件的和子欄位: `HotelName`

    $select=HotelName, Address, Rooms/Type, Rooms/BaseRate

範例結果可能如下所示:

```json
{
  "HotelName": "Secret Point Motel",
  "Rating": 4,
  "Address": {
    "StreetAddress": "677 5th Ave",
    "City": "New York",
    "StateProvince": "NY",
    "Country": "USA",
    "PostalCode": "10022"
  },
  "Rooms": [
    {
      "Type": "Budget Room",
      "BaseRate": 9.69
    },
    {
      "Type": "Budget Room",
      "BaseRate": 8.09
    }
  ]
}
```

## <a name="next-steps"></a>後續步驟  

- [如何在 Azure 搜尋服務中使用搜尋結果](search-pagination-page-layout.md)
- [Azure 搜尋服務的 OData 運算式語言總覽](query-odata-filter-orderby-syntax.md)
- [Azure 搜尋服務的 OData 運算式語法參考](search-query-odata-syntax-reference.md)
- [搜尋文件 (Azure 搜尋服務 REST API)](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
