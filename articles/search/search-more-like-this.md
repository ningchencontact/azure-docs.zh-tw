---
title: moreLikeThis （預覽）查詢功能
titleSuffix: Azure Cognitive Search
description: 說明 moreLikeThis （預覽）功能，其可在預覽版本的 Azure 認知搜尋 REST API 中取得。
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: fdde89f9ff88b15c464af805b81708b268e5ddf5
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/07/2019
ms.locfileid: "73721728"
---
# <a name="morelikethis-preview-in-azure-cognitive-search"></a>Azure 認知搜尋中的 moreLikeThis （預覽）

> [!IMPORTANT] 
> 此功能目前為公開預覽狀態。 預覽功能會在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。 [REST API 版本 2019-05-06-Preview](search-api-preview.md) 提供此功能。 目前沒有入口網站或 .NET SDK 支援。

`moreLikeThis=[key]` 是[搜尋檔 API](https://docs.microsoft.com/rest/api/searchservice/search-documents)中的查詢參數，會尋找類似檔索引鍵所指定檔的檔。 使用 `moreLikeThis` 提出搜尋要求時，系統會利用最能描述該文件的指定文件中擷取的搜尋詞彙，產生查詢。 然後將產生的查詢用於提出搜尋要求。 根據預設，所有可搜尋欄位的內容都會被視為，減去您使用 `searchFields` 參數指定的任何受限制欄位。 `moreLikeThis` 參數無法搭配搜尋參數 `search=[string]` 使用。

根據預設，會考慮所有最上層可搜尋欄位的內容。 如果您想要改為指定特定欄位，您可以使用 `searchFields` 參數。 

您無法在[複雜類型](search-howto-complex-data-types.md)中可搜尋的子欄位上使用 moreLikeThis。

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


## <a name="next-steps"></a>後續步驟

您可以使用任何 web 測試控管來試驗這項功能。  我們建議您在此練習中使用 Postman。

> [!div class="nextstepaction"]
> [使用 Postman 探索 Azure 認知搜尋 REST Api](search-get-started-postman.md)