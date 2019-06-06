---
title: 如何逐頁檢視搜尋結果 - Bing Web 搜尋 API
titleSuffix: Azure Cognitive Services
description: 了解如何逐頁檢視 Bing Web 搜尋 API 的搜尋結果。
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.assetid: 26CA595B-0866-43E8-93A2-F2B5E09D1F3B
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 05/15/2019
ms.author: aahi
ms.openlocfilehash: a038dc2706c7cb128751630f8997851409886290
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/30/2019
ms.locfileid: "66384817"
---
# <a name="how-to-page-through-results-from-the-bing-web-search-api"></a>如何逐頁檢視 Bing Web 搜尋 API 的搜尋結果

當您呼叫 Web 搜尋 API 時，Bing 會傳回結果清單。 此清單是查詢相關結果總數的子集。 若要取得可用結果的預估總數，請存取解答物件的 [totalEstimatedMatches](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference) 欄位。  

下列範例顯示 Web 解答包含的 `totalEstimatedMatches` 欄位。  

```json
{
    "_type" : "SearchResponse",
    "webPages" : {
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=3A43CA...",
        "totalEstimatedMatches" : 262000,
        "value" : [...]
    }
}  
```

若要逐頁瀏覽可用的網頁，請使用 [count](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#count) 和 [offset](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#offset) 查詢參數。  

`count` 參數指定要在回應中傳回的結果數目。 您可能在回應中要求的結果數目上限為 50。 預設值為 10。 傳遞的實際數目可能小於所要求的數目。

`offset` 參數指定要略過的結果數目。 `offset` 是以零為起始，而且應該小於 (`totalEstimatedMatches` - `count`)。  

如果您希望每頁顯示 15 個網頁，您可以將 `count` 設定為 15 並將 `offset` 設定為 0，以取得結果的第一頁。 對於後續每個頁面，您可讓 `offset` 以 15 遞增 (例如 15、30)。  

以下範例要求 15 個於位移 45 開始的網頁。  

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&count=15&offset=45&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```

如果預設 `count` 值適用於您的實作，您只需要指定 `offset` 查詢參數。  

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&offset=45&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```

Web 搜尋 API 會傳回包含網頁的結果，其中可能包含影像、影片和新聞。 當您逐頁瀏覽搜尋結果時，您會逐頁瀏覽 [WebAnswer](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#webanswer) 解答，而不是逐頁瀏覽其他解答 (例如影像或新聞)。 例如，如果您將 `count` 設定為 50，您會得到 50 筆網頁結果，但回應可能也包含其他解答的結果。 例如，回應可能包含 15 個影像和 4 篇新聞文章。 此外，結果也可能包含第一頁 (但不是第二頁) 上的新聞，反之亦然。   

如果您指定 `responseFilter` 查詢參數且未在篩選條件清單中包含網頁，請勿使用 `count` 和 `offset` 參數。 

> [!NOTE]
> `TotalEstimatedAnswers` 欄位是針對您可從目前查詢中擷取之搜尋結果總數的估計。  當您設定 `count` 和 `offset` 參數時，`TotalEstimatedAnswers` 的數目可能會變更。 

## <a name="next-steps"></a>後續步驟

* [什麼是 Bing Web 搜尋 API](overview.md)？
