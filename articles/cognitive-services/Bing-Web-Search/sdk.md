---
title: Bing 搜尋 SDK | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: 搜尋 Web 之應用程式的 Bing Web 搜尋 SDK。
services: cognitive-services
author: mikedodaro
manager: rosh
ms.assetid: ''
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: article
ms.date: 1/11/2018
ms.author: v-gedod
ms.openlocfilehash: 9d2f363d772febfb9fffd2d9c2924398e438d6e0
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/23/2018
ms.locfileid: "35370446"
---
# <a name="bing-search-sdk"></a>Bing 搜尋 SDK
Bing Web 搜尋 API 範例包括下列情節：
1. 使用單一字詞查詢來取得結果，並印出第一個之每個 Web、影像、新聞和影片結果的名稱和 URL。
2. 以片語查詢，並驗證結果數目，然後印出第一個結果的名稱和 URL。
3. 在回應篩選設定為 `news` 的情況下查詢搜尋字詞，並列印新聞結果的詳細資料。
4. 使用 `answerCount` 和 `promote` 參數查詢搜尋字詞，然後列印結果的詳細資料。

Bing 搜尋 SDK 透過下列程式設計語言更輕易地存取 Web 搜尋功能：
* 開始使用 [.NET 範例](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7) 
    * [NuGet 套件](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.WebSearch/1.2.0)
    * 另請參閱 [.NET 程式庫](https://github.com/Azure/azure-sdk-for-net/tree/psSdkJson6/src/SDKs/CognitiveServices/dataPlane/Search/BingWebSearch)，以了解定義和相依性。
* 開始使用 [Node.js 範例](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples) 
    * 另請參閱 [Node.js 程式庫](https://github.com/Azure/azure-sdk-for-node/tree/master/lib/services/webSearch)，以了解定義和相依性。
* 開始使用 [Java 範例](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples) 
    * 另請參閱 [Java 程式庫](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master/Search/BingWebSearch)，以了解定義和相依性。
* 開始使用 [Python 範例](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples) 
    * 另請參閱 [Python 程式庫](https://github.com/Azure/azure-sdk-for-python/tree/master/azure-cognitiveservices-search-websearch)，以了解定義和相依性。

每種語言的 SDK 範例都會包括讀我檔案，內含必要條件和安裝/執行範例的詳細資料。
