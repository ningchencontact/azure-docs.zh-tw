---
title: 專案 URL 預覽端點
titlesuffix: Azure Cognitive Services
description: URL 預覽端點的摘要。
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: url-preview
ms.topic: reference
ms.date: 03/29/2018
ms.author: rosh, v-gedod
ms.openlocfilehash: 7cc52493ec0e2b9c81d52da4bb22102c2c7e5e5c
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/07/2019
ms.locfileid: "55861982"
---
# <a name="project-url-preview-endpoint"></a>專案 URL 預覽端點

URL 預覽 API 包含一個端點。

## <a name="endpoint"></a>端點
若要取得 URL 預覽，請將要求傳送至下列端點。 若有特定要求，請使用標頭和 URL 參數。

GET：
```
https://api.labs.cognitive.microsoft.com/urlpreview/v7.0/search?q=https://swiftkey.com

```

### <a name="query-parameters"></a>查詢參數
|Name|值|類型|必要|  
|----------|-----------|----------|--------------|  
|q|要預覽的 URL|字串 |yes|
|safeSearch|禁止非法成人內容或盜版內容，錯誤代碼為 400，並且不會傳回 *isFamilyFriendly* 旗標。 <p>對於合法的成人內容，會出現以下行為。 狀態碼傳回 200，而且 *isFamilyFriendly* 旗標設為 false。<ul><li>safeSearch=strict：不會傳回標題、描述、URL 以及映像。</li><li>safeSearch=moderate；取得標題、URL 以及描述，但不會有描述性的影像。</li><li>safeSearch=off；取得所有回應物件/元素 – 標題、URL、描述、影像。</li></ul> |字串|不需要。 </br> 預設值為 safeSearch=strict。| 

## <a name="response-object"></a>回應物件

回應包括顯示下列屬性的 HTTP 標頭和網頁物件：`name`、`url`、`description`、`isFamilyFriendly` 和 `primaryImageOfPage`。

```
BingAPIs-TraceId: 15AFE52A97AA422F960433A94803F6CE
BingAPIs-SessionId: 40587764F42142D3A8BA99F66B2B3BB6
X-MSEdge-ClientID: 0389E3EDED106B5E1424E82FEC436A56
BingAPIs-Market: en-US
X-MSEdge-Ref: Ref A: 15AFE52A97AA422F960433A94803F6CE Ref B: PAOEDGE0418 Ref C: 2018-03-30T16:36:27Z
{
  "_type": "WebPage",
  "name": "SwiftKey - Smart prediction technology for easier mobile ...",
  "url": "https://swiftkey.com/",
   "description": "Discover the best Android and iPhone and iPad apps for faster, easier typing with emoji, colorful themes and more - download SwiftKey Keyboard free today.",
  "isFamilyFriendly": true,
  "primaryImageOfPage": {
    "contentUrl": "https://swiftkey.com/images/og/default.jpg"
  }
}

```

## <a name="next-steps"></a>後續步驟
- [C# 快速入門](csharp.md)
- [JAVA 快速入門](java-quickstart.md)
- [JavaScript 快速入門](javascript.md)
- [Node 快速入門](node-quickstart.md)
- [Python 快速入門](python-quickstart.md)
