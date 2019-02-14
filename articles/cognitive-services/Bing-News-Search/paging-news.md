---
title: 如何逐頁檢視 Bing 新聞搜尋結果
titlesuffix: Azure Cognitive Services
description: 了解如何逐頁瀏覽「Bing 新聞 API 搜尋」傳回的所有新聞文章。
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: scottwhi
ms.openlocfilehash: 1d344f388b03acb3a81fcfde0e214eb7d82dc9b9
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/07/2019
ms.locfileid: "55885067"
---
# <a name="how-to-page-through-news-search-results"></a>如何逐頁檢視新聞搜尋結果

當您呼叫「新聞搜尋 API」時，Bing 會傳回與您查詢相關結果的清單。 若要取得可用結果的預估總數，請存取解答物件的 [totalEstimatedMatches](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#news-totalmatches) 欄位。  
  
下列範例顯示新聞解答包含的 `totalEstimatedMatches` 欄位。  

```json
{  
    "_type" : "News",  
    "readLink" : "https:\/\/api.cognitive.microsoft.com\/bing\/v7\/news\/search?q=sailing+dinghies",  
    "totalEstimatedMatches" : 88400,  
    "value" : [...]  
}  
```  
  
若要逐頁瀏覽可用的文章，請使用 [count](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#count) 和 [offset](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#offset) 查詢參數。  
 

|參數  |說明  |
|---------|---------|
|`count`     | 指定要在回應中傳回的結果數目。 您可能在回應中要求的結果數目上限為 100。 預設值為 10。 傳遞的實際數目可能小於所要求的數目。        |
|`offset`     | 指定要略過的結果數目。 `offset` 是以零為起始，而且應該小於 (`totalEstimatedMatches` - `count`)。          |

例如，如果您希望每頁顯示 20 篇文章，您可以將 `count` 設定為 20 並將 `offset` 設定為 0，以取得結果的第一頁。 對於後續每個頁面，您可讓 `offset` 以 20 遞增 (例如 20、40)。  
  
以下範例會要求 20 個於位移 40 開始的新聞文章。  

```
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search?q=sailing+dinghies&count=20&offset=40&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```  
  
如果預設 `count` 值適用於您的實作，您只需指定如下列範例所示的 `offset` 查詢參數：  

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search?q=sailing+dinghies&offset=40&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```  

> [!NOTE]
> 逐頁瀏覽只適用於新聞搜尋 (/news/search)，但不適用於發燒主題 (/news/trendingtopics) 或新聞分類 (/news)。

> [!NOTE]
> `TotalEstimatedAnswers` 欄位是針對您可從目前查詢中擷取之搜尋結果總數的估計。  當您設定 `count` 和 `offset` 參數時，`TotalEstimatedAnswers` 的數目可能會變更。 
