---
title: 快速入門：執行新聞搜尋 - 適用於 C# 的 Bing 新聞搜尋 SDK
titleSuffix: Azure Cognitive Services
description: 使用此快速入門以運用「適用於 Python 的 Bing 新聞搜尋 SDK」來搜尋新聞，並處理回應。
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: quickstart
ms.date: 01/10/2019
ms.author: v-gedod
ms.custom: seodec2018
ms.openlocfilehash: 66f743273f9937902c3d39a0fc4dd2f034ab8d10
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/14/2019
ms.locfileid: "54260239"
---
# <a name="quickstart-perform-a-news-search-with-the-bing-news-search-sdk-for-c"></a>快速入門：使用適用於 C# 的 Bing 新聞搜尋 SDK 來執行新聞搜尋

透過本快速入門開始使用適用於 C# 的 Bing 新聞搜尋 SDK 來搜尋新聞。 雖然 Bing 新聞搜尋具有與大部分程式設計語言相容的 REST API，但 SDK 會提供簡單的方法，將服務整合到您的應用程式。 此範例的原始程式碼可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingNewsSearch) 上找到。

## <a name="prerequisites"></a>必要條件

* 任何一版的 [Visual Studio 2017](https://www.visualstudio.com/downloads/)。
* [Json.NET](https://www.newtonsoft.com/json) 架構 (以 NuGet 套件形式提供)。
* 如果您使用 Linux/MacOS，則可以使用 [Mono](http://www.mono-project.com/)來執行此應用程式。

* [Bing 新聞搜尋 SDK 的 NuGet 套件](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.NewsSearch/1.2.0)。 安裝此套件也會安裝：
    * Microsoft.Rest.ClientRuntime
    * Microsoft.Rest.ClientRuntime.Azure
    * Newtonsoft.Json

若要使用 Bing 新聞搜尋 SDK 來設定主控台應用程式，請在 Visual Studio 中瀏覽至 [方案總管] 中的 `Manage NuGet Packages` 選項。  新增 `Microsoft.Azure.CognitiveServices.Search.NewsSearch` 套件。

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../includes/cognitive-services-bing-news-search-signup-requirements.md)]

另請參閱[認知服務定價 - Bing 搜尋 API](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/)。

## <a name="create-and-initialize-a-project"></a>建立專案並將其初始化

1. 在 Visual Studio 中建立新的 C# 主控台解決方案。 然後將下列內容新增至主要程式碼檔案。
    
    ```csharp
    using System;
    using System.Linq;
    using Microsoft.Azure.CognitiveServices.Search.NewsSearch;
    ```

2. 為您的 API 金鑰建立變數 (搜尋字詞)，然後以該變數具現化新聞搜尋用戶端。

    ```csharp
    var key = "YOUR-ACCESS-KEY";
    var searchTerm = "Quantum Computing";
    var client = new NewsSearchClient(new ApiKeyServiceClientCredentials(key));
    ```

## <a name="send-a-request-and-parse-the-result"></a>傳送要求並剖析結果

1. 使用用戶端將搜尋要求傳送至 Bing 新聞搜尋服務：
    ```csharp
    var newsResults = client.News.SearchAsync(query: searchTerm, market: "en-us", count: 10).Result;
    ```

2. 如果傳回任何結果，請加以剖析：

    ```csharp
    if (newsResults.Value.Count > 0)
    {
        var firstNewsResult = newsResults.Value[0];
    
        Console.WriteLine($"TotalEstimatedMatches value: {newsResults.TotalEstimatedMatches}");
        Console.WriteLine($"News result count: {newsResults.Value.Count}");
        Console.WriteLine($"First news name: {firstNewsResult.Name}");
        Console.WriteLine($"First news url: {firstNewsResult.Url}");
        Console.WriteLine($"First news description: {firstNewsResult.Description}");
        Console.WriteLine($"First news published time: {firstNewsResult.DatePublished}");
        Console.WriteLine($"First news provider: {firstNewsResult.Provider[0].Name}");
    }
    
    else
    {
        Console.WriteLine("Couldn't find news results!");
    }
    Console.WriteLine("Enter any key to exit...");
    Console.ReadKey();
    ```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
[建立單頁 Web 應用程式](tutorial-bing-news-search-single-page-app.md)