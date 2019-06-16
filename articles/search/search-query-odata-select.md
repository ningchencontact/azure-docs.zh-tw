---
title: OData 選取參考-Azure 搜尋服務
description: 適用於 OData 語言參考選取 Azure 搜尋服務查詢語法。
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
ms.openlocfilehash: 9383ae725fffac55854488ffbc6aeb161ae7e0c2
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079674"
---
# <a name="odata-select-syntax-in-azure-search"></a>Azure 搜尋服務中的 OData $select 語法

 您可以使用[OData **$select**參數](query-odata-filter-orderby-syntax.md)選擇要從 Azure 搜尋服務的搜尋結果中包含的欄位。 這篇文章描述的語法 **$select**在詳細資料。 如需有關如何使用更多一般資訊 **$select**時顯示搜尋結果，請參閱[如何使用搜尋在 Azure 搜尋服務中結果](search-pagination-page-layout.md)。

## <a name="syntax"></a>語法

**$Select**參數會決定在查詢結果集傳回哪些欄位，每個文件。 下列 EBNF ([擴充式 Backus-naur 形式](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) 定義的文法 **$select**參數：

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
select_expression ::= '*' | field_path(',' field_path)*

field_path ::= identifier('/'identifier)*
```

互動式的語法圖表也會提供：

> [!div class="nextstepaction"]
> [Azure 搜尋服務的 OData 語法圖表](https://azuresearch.github.io/odata-syntax-diagram/#select_expression)

> [!NOTE]
> 請參閱[Azure 搜尋服務的 OData 運算式語法參考](search-query-odata-syntax-reference.md)的完整 EBNF。

**$Select**參數有兩種形式：

1. 一個星號 (`*`)，指出應該傳回所有可擷取的欄位，或
1. 欄位路徑需以逗號分隔清單，識別哪些欄位應該會傳回。

在使用第二種形式時，您只能指定可擷取的欄位清單中。

如果您未明確指定其子欄位列出複雜的欄位，則所有可擷取的子欄位將包含查詢結果集中。 例如，假設您的索引具有`Address`欄位`Street`， `City`，和`Country`所有擷取的子欄位。 如果您指定`Address`中 **$select**，查詢結果會包含所有三個的子欄位。

## <a name="examples"></a>範例

包含`HotelId`， `HotelName`，並`Rating`結果中的最上層欄位，以及`City`子欄位`Address`:

    $select=HotelId, HotelName, Rating, Address/City

範例結果可能如下所示：

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

包含`HotelName`結果，以及所有的子欄位的最上層欄位`Address`，而`Type`並`BaseRate`中每個物件的子欄位`Rooms`集合：

    $select=HotelName, Address, Rooms/Type, Rooms/BaseRate

範例結果可能如下所示：

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

- [如何使用 搜尋結果，在 Azure 搜尋服務](search-pagination-page-layout.md)
- [Azure 搜尋服務的 OData 運算式語言概觀](query-odata-filter-orderby-syntax.md)
- [Azure 搜尋服務的 OData 運算式語法參考](search-query-odata-syntax-reference.md)
- [搜尋文件 (Azure 搜尋服務 REST API)](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
