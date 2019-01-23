---
title: Bing 新聞搜尋端點
titlesuffix: Azure Cognitive Services
description: 新聞搜尋 API 端點的摘要。
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: conceptual
ms.date: 1/10/2019
ms.author: v-gedod
ms.openlocfilehash: cc38dff7cd3ae8d10d8a20cbf98749d724de1ee6
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/14/2019
ms.locfileid: "54262805"
---
# <a name="bing-news-search-api-endpoints"></a>Bing 新聞搜尋 API 端點

**新聞搜尋 API** 會傳回新聞文章、網頁、影像、影片和[實體](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/search-the-web)。 實體包含人員、地點或主題的摘要資訊。

## <a name="endpoints"></a>端點

若要使用「Bing 新聞搜尋 API」取得新聞搜尋結果，請將 `GET` 要求傳送至下列其中一個端點。 標頭和 URL 參數可定義進一步的規格。

### <a name="news-items-by-search-query"></a>依搜尋查詢的新聞項目

```
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search
```

根據搜尋查詢傳回新聞項目。 如果搜尋查詢是空的，則 API 將傳回不同類別的熱門新聞文章。 傳送查詢，做法是將搜尋字詞進行 url 編碼，然後將 `q=""` 參數加上該字詞。 如需了解可用性，請參閱[支援的國家/地區與市場](language-support.md#supported-markets-for-news-search-endpoint)。

### <a name="top-news-items-by-category"></a>依類別的熱門新聞項目

```
GET https://api.cognitive.microsoft.com/bing/v7.0/news  
```

依照類別傳回熱門新聞項目。 您可以使用 `category=business``category=sports` 或 `category=entertainment`，特別要求熱門商業、運動或娛樂文章。  `category` 參數只能搭配 `/news` URL 使用。 指定類別時有一些正式需求；請參閱[查詢參數](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#query-parameters)文件中的 `category`。 傳送查詢，做法是將搜尋字詞進行 url 編碼，然後將 `q=""` 參數加上該字詞。 如需了解可用性，請參閱[支援的國家/地區與市場](language-support.md#supported-markets-for-news-endpoint)。

### <a name="trending-news-topics"></a>趨勢新聞主題 

```
GET https://api.cognitive.microsoft.com/bing/v7.0/news/trendingtopics
```

傳回社交網路上目前的趨勢新聞主題。 若包含 `/trendingtopics` 選項，Bing 搜尋會忽略其他幾個參數，例如 `freshness` 和 `?q=""`。 如需了解可用性，請參閱[支援的國家/地區與市場](language-support.md#supported-markets-for-news-trending-endpoint)。

## <a name="next-steps"></a>後續步驟

如需關於標頭、參數、市場代碼、回應物件、錯誤的詳細資料，請參閱 [Bing 新聞搜尋 API v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference) 參考。

如需每個端點支援的參數完整相關資訊，請參閱每種類型的參考頁面。
如需透過範例了解使用新聞搜尋 API 的基本要求，請參閱 [Bing 新聞搜尋快速入門](https://docs.microsoft.com/azure/cognitive-services/bing-news-search)。
