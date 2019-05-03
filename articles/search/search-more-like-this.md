---
title: Azure 搜尋服務中的 moreLikeThis (預覽) - Azure 搜尋服務
description: Azure Search REST API 中公開的 moreLikeThis (預覽) 功能預備文件。
author: brjohnstmsft
manager: jlembicz
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: brjohnst
ms.custom: seodec2018
ms.openlocfilehash: d18069335bb20f78a5bcda85eb6fcb2a5abe75f7
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/02/2019
ms.locfileid: "65024680"
---
# <a name="morelikethis-in-azure-search-preview"></a>Azure 搜尋服務中的 moreLikeThis (預覽)

`moreLikeThis=[key]` 是中的查詢參數[搜尋文件 API](https://docs.microsoft.com/rest/api/searchservice/search-documents)所尋找的文件類似文件索引鍵所指定的文件。 使用 `moreLikeThis` 提出搜尋要求時，系統會利用最能描述該文件的指定文件中擷取的搜尋詞彙，產生查詢。 然後將產生的查詢用於提出搜尋要求。 根據預設，所有可搜尋欄位的內容會被視為，減去使用指定的任何限制欄位`searchFields`參數。 `moreLikeThis` 參數無法搭配搜尋參數 `search=[string]` 使用。

根據預設，會被視為所有最上層的可搜尋欄位的內容。 如果您想要改為指定特定欄位，您可以使用`searchFields`參數。 

> [!NOTE]
> `moreLikeThis` 預覽中可搜尋的子欄位上無法運作[複雜型別](search-howto-complex-data-types.md)。

## <a name="examples"></a>範例 

以下是 moreLikeThis 查詢的範例。 查詢會尋找其描述欄位最類似 `moreLikeThis` 參數所指定來源文件之欄位的文件。

```
Get /indexes/hotels/docs?moreLikeThis=1002&searchFields=description&api-version=2019-05-06-Preview
```

```
POST /indexes/hotels/docs/search?api-version=2019-05-06-Preview
    {
      "moreLikeThis": "1002",
      "searchFields": "description"
    }
```

## <a name="feature-availability"></a>功能可用性

`moreLikeThis`參數是僅供預覽 REST Api (`api-version=2019-05-06-Preview`)。

## <a name="next-steps"></a>後續步驟

您可以使用任何 web 測試工具，來試驗這項功能。  我們建議針對此練習中使用 Postman。

> [!div class="nextstepaction"]
> [探索 Azure 搜尋服務 REST Api 使用 Postman](search-fiddler.md)