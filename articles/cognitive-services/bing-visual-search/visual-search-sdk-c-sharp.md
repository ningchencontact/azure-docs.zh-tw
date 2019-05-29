---
title: 快速入門：使用適用於 C# 的 Bing 圖像式搜尋 SDK 取得影像見解
titleSuffix: Azure Cognitive Services
description: 了解如何使用 Bing 圖像式搜尋 SDK 上傳影像並取得其見解。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: quickstart
ms.date: 05/15/2019
ms.author: v-gedod
ms.openlocfilehash: 9fd693207b429ddc0a2bfdd772f4822f188ac3f7
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/20/2019
ms.locfileid: "65884940"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-sdk-for-c"></a>快速入門：使用適用於 C# 的 Bing 圖像式搜尋 SDK 取得影像見解

使用本快速入門，開始使用 C# SDK 從 Bing 圖像式搜尋服務取得影像見解。 雖然 Bing 圖像式搜尋具有與大部分程式設計語言相容的 REST API，但 SDK 會提供簡單的方法，將服務整合到您的應用程式。 此範例的原始程式碼可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingVisualSearch) 上找到。

## <a name="prerequisites"></a>必要條件

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)。
* 如果您使用 Linux/MacOS，則可以使用 [Mono](https://www.mono-project.com/)來執行此應用程式。
* NuGet 圖像式搜尋套件。 
    - 在 Visual Studio 的 [方案總管] 中，以滑鼠右鍵按一下專案，然後從功能表選取 `Manage NuGet Packages`。 安裝 `Microsoft.Azure.CognitiveServices.Search.VisualSearch` 套件。 安裝 NuGet 套件也會安裝：
        - Microsoft.Rest.ClientRuntime
        - Microsoft.Rest.ClientRuntime.Azure
        - Newtonsoft.Json


[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

<a name="client"></a>

## <a name="create-and-initialize-the-application"></a>建立應用程式並將其初始化

1. 在 Visual Studio 中，建立新專案。 接著，新增下列指示詞。
    
    ```csharp
    using Microsoft.Azure.CognitiveServices.Search.VisualSearch;
    using Microsoft.Azure.CognitiveServices.Search.VisualSearch.Models;
    ```

2. 使用訂用帳戶金鑰具現化用戶端。
    
    ```csharp
    var client = new VisualSearchClient(new ApiKeyServiceClientCredentials("YOUR-ACCESS-KEY"));
    ```
    
## <a name="send-a-search-request"></a>傳送搜尋要求 

1. 在影像中建立 `FileStream` (在此案例中為 `TestImages/image.jpg`)。 接著，使用用戶端透過 `client.Images.VisualSearchMethodAsync()` 傳送搜尋要求。 
    
    ```csharp
     System.IO.FileStream stream = new FileStream(Path.Combine("TestImages", "image.jpg"), FileMode.Open);
     // The knowledgeRequest parameter is not required if an image binary is passed in the request body
     var visualSearchResults = client.Images.VisualSearchMethodAsync(image: stream, knowledgeRequest: (string)null).Result;
    ```
    
2. 剖析先前查詢的結果：

    ```csharp
    // Visual Search results
    if (visualSearchResults.Image?.ImageInsightsToken != null)
    {
        Console.WriteLine($"Uploaded image insights token: {visualSearchResults.Image.ImageInsightsToken}");
    }
    else
    {
        Console.WriteLine("Couldn't find image insights token!");
    }
    
    // List of tags
    if (visualSearchResults.Tags.Count > 0)
    {
        var firstTagResult = visualSearchResults.Tags[0];
        Console.WriteLine($"Visual search tag count: {visualSearchResults.Tags.Count}");
    
        // List of actions in first tag
        if (firstTagResult.Actions.Count > 0)
        {
            var firstActionResult = firstTagResult.Actions[0];
            Console.WriteLine($"First tag action count: {firstTagResult.Actions.Count}");
            Console.WriteLine($"First tag action type: {firstActionResult.ActionType}");
        }
        else
        {
            Console.WriteLine("Couldn't find tag actions!");
        }
    }
    ```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [建置單頁 Web 應用程式](tutorial-bing-visual-search-single-page-app.md)
