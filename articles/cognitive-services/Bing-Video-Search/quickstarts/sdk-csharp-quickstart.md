---
title: 快速入門：使用適用於 C# 的 Bing 影片搜尋 SDK 來搜尋影片
titleSuffix: Azure Cognitive Services
description: 透過本快速入門，使用適用於 C# 的 Bing 影片搜尋 SDK 來傳送影片搜尋要求。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: quickstart
ms.date: 01/31/2019
ms.author: aahi
ms.openlocfilehash: e10d9088f6de3b7a3a638cdbe18f51425d206b7b
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/16/2019
ms.locfileid: "65798170"
---
# <a name="quickstart-perform-a-video-search-with-the-bing-video-search-sdk-for-c"></a>快速入門：使用適用於 C# 的 Bing 影片搜尋 SDK 來執行影片搜尋

透過本快速入門開始使用適用於 C# 的 Bing 影片搜尋 SDK 來搜尋新聞。 雖然 Bing 影片搜尋具有與大部分程式設計語言相容的 REST API，但 SDK 會提供簡單的方法，將服務整合到您的應用程式。 此範例的原始程式碼可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingVideoSearch) 上找到。 其中包含更多的註解和功能。

## <a name="prerequisites"></a>必要條件

* [Visual Studio 2017 或更新版本](https://visualstudio.microsoft.com/downloads/)的任何版本。
* Json.NET 架構 ([以 NuGet 套件形式](https://www.nuget.org/packages/Newtonsoft.Json/)提供)。

若要將 Bing 影片搜尋 SDK 新增至您的專案，請在 Visual Studio 中選取 [方案總管]  中的 [管理 NuGet 套件]  。 新增 `Microsoft.Azure.CognitiveServices.Search.VideoSearch` 套件。

安裝 [[NuGet 影片搜尋 SDK 套件]](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.VideoSearch/1.2.0) 也會安裝下列相依性：

* Microsoft.Rest.ClientRuntime
* Microsoft.Rest.ClientRuntime.Azure
* Newtonsoft.Json

[!INCLUDE [cognitive-services-bing-video-search-signup-requirements](../../../../includes/cognitive-services-bing-video-search-signup-requirements.md)]


## <a name="create-and-initialize-a-project"></a>建立專案並將其初始化

1. 在 Visual Studio 中建立新的 C# 主控台解決方案。 然後將下列內容新增至主要程式碼檔案。

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using Microsoft.Azure.CognitiveServices.Search.VideoSearch;
    using Microsoft.Azure.CognitiveServices.Search.VideoSearch.Models;
    ```

2. 藉由使用訂用帳戶金鑰建立新的 `ApiKeyServiceClientCredentials` 物件，並呼叫建構函式，來將用戶端具現化。

    ```csharp
    var client = new VideoSearchAPI(new ApiKeyServiceClientCredentials("YOUR-ACCESS-KEY"));
    ```

## <a name="send-a-search-request-and-process-the-results"></a>傳送搜尋要求並處理結果

1. 使用用戶端傳送搜尋要求。 將 "SwiftKey" 用於搜尋查詢。

    ```csharp
    var videoResults = client.Videos.SearchAsync(query: "SwiftKey").Result;
    ```

2. 如果傳回任何結果，取得第一個具有 `videoResults.Value[0]` 的結果。 然後列印影片的識別碼、標題和 URL。

    ```csharp
    if (videoResults.Value.Count > 0)
    {
        var firstVideoResult = videoResults.Value[0];

        Console.WriteLine($"\r\nVideo result count: {videoResults.Value.Count}");
        Console.WriteLine($"First video id: {firstVideoResult.VideoId}");
        Console.WriteLine($"First video name: {firstVideoResult.Name}");
        Console.WriteLine($"First video url: {firstVideoResult.ContentUrl}");
    }
    else
    {
        Console.WriteLine("Couldn't find video results!");
    }
    ```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [建立單頁 Web 應用程式](../tutorial-bing-video-search-single-page-app.md)

## <a name="see-also"></a>另請參閱 

* [什麼是 Bing 影片搜尋 API？](../overview.md)
* [認知服務 .NET SDK 範例](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)
