---
title: Bing 自訂搜尋：取得自訂自動建議的建議 | Microsoft Docs
description: 描述如何擷取自訂自動建議的建議
services: cognitive-services
author: brapel
manager: ehansen
ms.service: cognitive-services
ms.technology: bing-custom-search
ms.topic: article
ms.date: 09/28/2017
ms.author: v-brapel
ms.openlocfilehash: 73059038018602f7aa76377bf7c98d4658576576
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/23/2018
ms.locfileid: "35368827"
---
# <a name="get-custom-suggestions"></a>取得自訂建議
將查詢傳送至 Bing 自訂搜尋之前，請先呼叫自訂自動建議 API 產生搜尋字詞建議，然後加強搜尋體驗。 自訂自動建議 API 會根據使用者提供的部分查詢字串，傳回一份建議的查詢清單。 您指定的任何相關自訂查詢字詞，會在自動建議所產生的建議前面出現。 如需詳細資訊，請參閱[定義自訂的搜尋建議](define-custom-suggestions.md)。

## <a name="endpoint"></a>端點
若要使用 Bing 自訂搜尋 API 取得建議的查詢，請傳送 `GET` 要求到下列端點。

```
GET https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/Suggestions 
```

## <a name="response-json"></a>回應 JSON
回應會包含一份 SearchAction 物件清單，其中包含建議的查詢字詞。

```
        {  
            "displayText" : "sailing lessons seattle",  
            "query" : "sailing lessons seattle",  
            "searchKind" : "CustomSearch"  
        },  
```

每個建議都包含 `displayText` 和 `query` 欄位。 `displayText` 欄位包含建議的查詢，您可以用來填入搜尋方塊的下拉式清單。

如果使用者從下拉式清單中選取建議的查詢，請在呼叫 [Bing 自訂搜尋 API](overview.md) 時，使用 `query` 欄位中的查詢字詞。

## <a name="throttling-requests"></a>節流要求

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../includes/cognitive-services-bing-throttling-requests.md)]

## <a name="next-steps"></a>後續步驟

- [呼叫您的自訂搜尋](./search-your-custom-view.md)
- [設定託管的 UI 體驗](./hosted-ui.md)