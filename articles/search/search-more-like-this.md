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
ms.openlocfilehash: 95b9c76a2ff962cb2fa4bacbb1b1e9a953b7014f
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/05/2019
ms.locfileid: "74873806"
---
# <a name="morelikethis-preview-in-azure-cognitive-search"></a>Azure 認知搜尋中的 moreLikeThis （預覽）

> [!IMPORTANT] 
> 此功能目前為公開預覽狀態。 預覽功能是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。 [REST API 版本 2019-05-06-Preview](search-api-preview.md) 提供此功能。 目前沒有入口網站或 .NET SDK 支援。

`moreLikeThis=[key]` 是[搜尋檔 API](https://docs.microsoft.com/rest/api/searchservice/search-documents)中的查詢參數，會尋找類似檔索引鍵所指定檔的檔。 使用 `moreLikeThis` 提出搜尋要求時，系統會利用最能描述該文件的指定文件中擷取的搜尋詞彙，產生查詢。 然後將產生的查詢用於提出搜尋要求。 根據預設，所有可搜尋欄位的內容都會被視為，減去您使用 `searchFields` 參數指定的任何受限制欄位。 `moreLikeThis` 參數無法搭配搜尋參數 `search=[string]` 使用。

根據預設，會考慮所有最上層可搜尋欄位的內容。 如果您想要改為指定特定欄位，您可以使用 `searchFields` 參數。 

您無法在[複雜類型](search-howto-complex-data-types.md)中可搜尋的子欄位上使用 `MoreLikeThis`。

## <a name="examples"></a>範例

下列所有範例都使用[快速入門：在 Azure 入口網站中建立搜尋索引中](search-get-started-portal.md)的飯店範例。

### <a name="simple-query"></a>簡單查詢

下列查詢會尋找其描述欄位與 `moreLikeThis` 參數所指定之來源文件欄位最相似的檔：

```
GET /indexes/hotels-sample-index/docs?moreLikeThis=29&searchFields=Description&api-version=2019-05-06-Preview
```

在此範例中，要求會搜尋與 `HotelId` 29 相似的飯店。
您也可以使用 HTTP POST 來叫用 `MoreLikeThis`，而不是使用 HTTP GET：

```
POST /indexes/hotels-sample-index/docs/search?api-version=2019-05-06-Preview
    {
      "moreLikeThis": "29",
      "searchFields": "Description"
    }
```

### <a name="apply-filters"></a>套用篩選條件

`MoreLikeThis` 可以與其他常見的查詢參數（例如 `$filter`）結合。 例如，您可以將查詢限制為只有其類別為「預算」且評等高於3.5 的飯店：

```
GET /indexes/hotels-sample-index/docs?moreLikeThis=20&searchFields=Description&$filter=(Category eq 'Budget' and Rating gt 3.5)&api-version=2019-05-06-Preview
```

### <a name="select-fields-and-limit-results"></a>選取欄位並限制結果

`$top` 選取器可以用來限制 `MoreLikeThis` 查詢中應該傳回多少結果。 此外，您也可以使用 `$select`來選取欄位。 這裡會選取前三個飯店，以及其識別碼、名稱和評等： 

```
GET /indexes/hotels-sample-index/docs?moreLikeThis=20&searchFields=Description&$filter=(Category eq 'Budget' and Rating gt 3.5)&$top=3&$select=HotelId,HotelName,Rating&api-version=2019-05-06-Preview
```

## <a name="next-steps"></a>後續步驟

您可以使用任何 web 測試控管來試驗這項功能。  我們建議您在此練習中使用 Postman。

> [!div class="nextstepaction"]
> [使用 Postman 探索 Azure 認知搜尋 REST Api](search-get-started-postman.md)
