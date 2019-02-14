---
title: 如何逐頁瀏覽可用的影片 - Bing 影片搜尋
titlesuffix: Azure Cognitive Services
description: 了解如何逐頁瀏覽 Bing 影片搜尋 API 所傳回的所有影片。
services: cognitive-services
author: swhite-msft
manager: cgronlun
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: conceptual
ms.date: 01/31/2019
ms.author: scottwhi
ms.openlocfilehash: 5fd11088c84327325040a05de9616f19c3455b01
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/01/2019
ms.locfileid: "55564896"
---
# <a name="paging-through-video-search-results"></a>逐頁瀏覽影片搜尋結果

Bing 影片搜尋 API 會傳回其針對您的查詢所找到所有搜尋結果的子集。 藉由透過後續的 API 呼叫來逐頁瀏覽這些結果，便可以取得這些結果並於應用程式中顯示。

> [!NOTE]
> 逐頁瀏覽只適用於影片搜尋 (/videos/search)，而不適用於影片見解 (/videos/details) 或發燒影片 (/videos/trending)。

## <a name="total-estimated-matches"></a>估計的相符項目總數

若要取得所找到搜尋結果的估計數目，請使用 JSON 回應中的 [totalEstimatedMatches](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#videos-totalestimatedmatches) 欄位。   
  
```json  
{
    "_type" : "Videos",
    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=81EF7545D56...",
    "totalEstimatedMatches" : 1000,
    "value" : [...]
}  
```  
  
## <a name="paging-through-videos"></a>逐頁瀏覽影片

若要逐頁瀏覽可用的影片，請在傳送要求時使用 [count](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#count) 和 [offset](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#offset) 查詢參數。  
  

|參數  |說明  |
|---------|---------|
|`count`     | 指定要在回應中傳回的結果數目。 您可能在回應中要求的結果數目上限為 100。 預設值為 10。 傳遞的實際數目可能小於所要求的數目。        |
|`offset`     | 指定要略過的結果數目。 `offset` 是以零為起始，而且應該小於 (`totalEstimatedMatches` - `count`)。          |

例如，如果您希望每頁顯示 20 篇文章，您可以將 `count` 設定為 20 並將 `offset` 設定為 0，以取得結果的第一頁。 對於後續每個頁面，您可讓 `offset` 以 20 遞增 (例如 20、40)。  
  
以下範例要求 20 個於位移 40 開始的影片。  
  
```cURL  
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/search?q=sailing+dinghies&count=20&offset=40&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```  

如果您使用 [count](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#count) 的預設值，則只需要指定 `offset` 查詢參數，如下列範例所示。  
  
```cURL  
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/search?q=sailing+dinghies&offset=40&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```  

如果您要一次逐頁瀏覽 35 個影片，請在第一個要求將 `offset` 查詢參數設定為 0，然後在每個後續要求將 `offset` 遞增 35。 不過，下一個回應中的某些結果可能會包含與上一個回應重複的影片結果。 例如，此回應中的前兩部影片可能與前一個回應中的最後兩部影片相同。

若要消除重複的結果，請使用 `Videos` 物件的 [nextOffset](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#videos-nextoffset) 欄位。

例如，如果您想要一次逐頁瀏覽 30 部影片，您可將第一個要求中的 `count` 設定為 30 並將 `offset` 設定為 0。 在下一個要求中，請將 `offset` 查詢參數設定為 `nextOffset` 值。

> [!NOTE]
> `TotalEstimatedAnswers` 欄位是針對您可從目前查詢中擷取之搜尋結果總數的估計。  當您設定 `count` 和 `offset` 參數時，`TotalEstimatedAnswers` 的數目可能會變更。 
  
## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
[取得影片深入解析](video-insights.md)
