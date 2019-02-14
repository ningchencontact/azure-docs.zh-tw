---
title: 什麼是 Bing 圖像式搜尋？
titleSuffix: Azure Cognitive Services
description: Bing 圖像式搜尋可提供影像的相關詳細資料或深入解析，例如類似影像或購物來源。
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: overview
ms.date: 04/10/2018
ms.author: scottwhi
ms.openlocfilehash: 9e323e9ec928d9f8b0592850adcd088a589ebf28
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/07/2019
ms.locfileid: "55863086"
---
# <a name="what-is-the-bing-visual-search-api"></a>什麼是 Bing 圖像式搜尋 API？

Bing 圖像式搜尋 API 可對 Bing.com/images 上顯示的影像提供類似的影像詳細資料。 上傳影像或提供其 URL，此 API 即可識別各種不同的詳細資料，包括外觀上類似的影像、購物來源、包含影像的網頁等等。 如果您使用 [Bing 影像搜尋 API](../bing-image-search/overview.md)，您可以使用已附加至 API 搜尋結果的見解權杖，而非上傳影像。

## <a name="insights"></a>深入解析

以下是圖像式搜尋可讓您探索的見解：

| 見解                              | 說明 |
|--------------------------------------|-------------|
| 在視覺效果上類似的影像              | 外觀上類似於輸入影像的影像清單。 |
| 在視覺效果上類似的產品            | 外觀上類似於所示產品的產品。            |
| 購物來源                     | 可購買到輸入影像中所含項目的地點清單。            |
| 相關搜尋                     | 由他人建立或根據影像內容而建立的相關搜尋。            |
| 包含影像的網頁     | 包含輸入影像的網頁。            |
| 食譜                              | 一份網頁清單，這些網頁包含用來烹調輸入影像中所含菜餚的食譜            |

除了這些深入解析以外，圖像式搜尋也會傳回從輸入影像衍生的各種字詞 (標記) 集。 這些標記可讓使用者瀏覽影像中出現的概念。 例如，如果輸入影像顯示的是知名運動員，則其中一個標記可能是運動員的名字，另外也可能有「運動」標記。 或者，如果輸入影像顯示的是蘋果派，則可能會有「蘋果派」、「甜派」、「甜點」等標記，讓使用者能夠瀏覽相關的概念。

圖像式搜尋的結果也會包含影像中相關區域的週框。 例如，如果影像包含數個名人，則結果可能會為影像中可辨識的每個名人加上週框。 或者，如果 Bing 在影像中辨識出某項產品或服飾，則結果可能會為可辨識的產品或服飾項目加上週框。

> [!IMPORTANT]
> 如果您使用 Bing 影像搜尋 API 取得影像見解，請考慮切換至 Bing 圖像式搜尋 API，其可提供更完整的見解。

## <a name="workflow"></a>工作流程

Bing 圖像式搜尋 API 是一種 RESTful Web 服務，因此可輕易地從任何可發出 HTTP 要求及剖析 JSON 的程式設計語言呼叫。 您可以透過 REST API 或 SDK 來使用此服務。

1. 建立具備 Bing 搜尋 API 存取權的認知服務 API 帳戶。 如果您沒有 Azure 訂用帳戶，可以建立免費帳戶。
2. 使用有效的搜尋查詢，將要求傳送至 API。
3. 剖析傳回的 JSON 訊息以處理 API 回應。


## <a name="next-steps"></a>後續步驟

首先，觀看 Bing 影像搜尋 API 的[互動式示範](https://azure.microsoft.com/services/cognitive-services/bing-visual-search/)。
此示範將說明如何快速地自訂搜尋查詢，並翻找出 Web 上的影像。

當您準備好要呼叫 API 時，請建立[認知服務 API 帳戶](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)。 如果您沒有 Azure 訂用帳戶，可以[建立免費帳戶](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api)。

若要快速開始使用您的第一個要求，請參閱快速入門：[C#](quickstarts/csharp.md) | [Java](quickstarts/java.md) | [node.js](quickstarts/nodejs.md) | [Python](quickstarts/python.md)。


## <a name="see-also"></a>另請參閱

* [Bing 圖像式搜尋參考](https://docs.microsoft.com/rest/api/cognitiveservices/bingvisualsearch/images/visualsearch)文件包含您在要求以影像為基礎的搜尋結果時所能使用的端點、標頭、API 回應和查詢參數的定義和資訊。

* [Bing 使用和顯示需求](./use-and-display-requirements.md)指定了透過 Bing 搜尋 API 取得的內容和資訊可行的用法。
