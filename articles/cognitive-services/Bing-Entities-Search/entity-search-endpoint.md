---
title: Bing 實體搜尋 API 端點
titlesuffix: Azure Cognitive Services
description: 了解 Bing 實體搜尋 API 端點並對其傳送要求。
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: v-gedod
ms.openlocfilehash: cae65c8fb7deb9f68a297de2058a86249b60136b
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/06/2019
ms.locfileid: "55753975"
---
# <a name="bing-entity-search-api-endpoint"></a>Bing 實體搜尋 API 端點


Bing 實體搜尋 API 有一個端點，該端點會根據查詢從 Web 傳回實體。 這些搜尋結果會以 JSON 傳回。

## <a name="get-entity-results-from-the-endpoint"></a>從端點取得實體結果

若要使用 **Bing API** 取得實體結果，請將 `GET` 要求傳送至下列端點。 使用 [headers](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#headers) 和 [query 參數](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#query-parameters)來自訂您的搜尋要求。 搜尋要求可以使用 `?q=` 參數來傳送。

```cURL
 GET https://api.cognitive.microsoft.com/bing/v7.0/entities
```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [什麼是 Bing 實體搜尋 API？](overview.md)

## <a name="see-also"></a>另請參閱 

如需標頭、參數、市場代碼、回應物件、錯誤等項目的詳細資訊，請參閱 [Bing 實體搜尋 API v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference) 參考文章。
