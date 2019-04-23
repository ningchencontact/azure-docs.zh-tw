---
title: 使用 ImageInsightsToken 尋找先前搜尋中的類似影像 - Bing 圖像式搜尋
titleSuffix: Azure Cognitive Services
description: 使用 Bing 圖像式搜尋 SDK 來取得 ImageInsightsToken 所指定影像的 URL。
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: article
ms.date: 04/05/2019
ms.author: rosh
ms.openlocfilehash: 39a95e877c766eb8f491c166edeb9d96f21db7dd
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/18/2019
ms.locfileid: "59493940"
---
# <a name="find-similar-images-from-previous-searches-using-imageinsightstoken"></a>使用 ImageInsightsToken 尋找先前搜尋中的類似影像

圖像式搜尋 SDK 可讓您從先前傳回 `ImageInsightsToken` 的搜尋中，線上尋找影像。 此應用程式可取得 `ImageInsightsToken`，並在後續搜尋中使用權杖。 然後，將傳送`ImageInsightsToken`Bing 和傳回的結果，包括 Bing 搜尋 Url 和在線上找到的類似影像的 Url。

本教學課程的完整原始程式碼都位於與其他錯誤處理和附註[GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/Tutorials/Bing-Visual-Search/BingVisualSearchInisghtsTokens.cs)。

## <a name="prerequisites"></a>必要條件

* 任何一版的 [Visual Studio 2017](https://www.visualstudio.com/downloads/)。
* 如果您使用 Linux/MacOS，您可以執行此應用程式使用[Mono](https://www.mono-project.com/)。
* NuGet 圖像式搜尋和 Bing 影像搜尋套件。
    - 在 Visual Studio 中方案總管 中以滑鼠右鍵按一下專案，然後選取**管理 NuGet 套件**從功能表。 安裝 `Microsoft.Azure.CognitiveServices.Search.CustomSearch` 套件和 `Microsoft.Azure.CognitiveServices.Search.ImageSearch` 套件。 安裝 NuGet 套件也會安裝：
        - Microsoft.Rest.ClientRuntime
        - Microsoft.Rest.ClientRuntime.Azure
        - Newtonsoft.Json


[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="get-the-imageinsightstoken-from-the-bing-image-search-sdk"></a>從 Bing 影像搜尋 SDK 取得 ImageInsightsToken

此應用程式會使用透過 [Bing 影像搜尋 SDK](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/image-search-sdk-quickstart) 所取得的 `ImageInsightsToken`。 在新的 C# 主控台應用程式中，建立會使用 `ImageSearchAPI()` 來呼叫 API 的用戶端。 然後使用`SearchAsync()`與您的查詢：

```csharp
var client = new ImageSearchAPI(new Microsoft.Azure.CognitiveServices.Search.ImageSearch.ApiKeyServiceClientCredentials(subKey));
var imageResults = client.Images.SearchAsync(query: "canadian rockies").Result;
Console.WriteLine("Search images for query \"canadian rockies\"");
```

使用 `imageResults.Value.First()` 儲存第一個搜尋結果，然後儲存影像深入解析的 `ImageInsightsToken`。

```csharp
String insightTok = "None";
if (imageResults.Value.Count > 0)
{
    var firstImageResult = imageResults.Value.First();
    insightTok = firstImageResult.ImageInsightsToken;
}
else
{
    insightTok = "None found";
    Console.WriteLine("Couldn't find image results!");
}
```

這`ImageInsightsToken`會傳送給 Bing 圖像式搜尋要求。

## <a name="add-the-imageinsightstoken-to-a-visual-search-request"></a>在圖像式搜尋要求中新增 ImageInsightsToken

藉由從 Bing 圖像式搜尋回應中所包含的 `ImageInsightsToken` 建立 `ImageInfo` 物件，來指定圖像式搜尋要求的 `ImageInsightsToken`。

```csharp
ImageInfo ImageInfo = new ImageInfo(imageInsightsToken: insightsTok);
```

## <a name="use-bing-visual-search-to-find-images-from-an-imageinsightstoken"></a>使用 Bing 圖像式搜尋來尋找 ImageInsightsToken 中的影像

`VisualSearchRequest` 物件包含所要搜尋 `ImageInfo` 中影像的相關資訊。 `VisualSearchMethodAsync()` 方法會取得結果。 影像會以權杖來表示，所以您不需要提供影像二進位檔。

```csharp
VisualSearchRequest VisualSearchRequest = new VisualSearchRequest(ImageInfo);

var visualSearchResults = client.Images.VisualSearchMethodAsync(knowledgeRequest: VisualSearchRequest).Result;

```

## <a name="iterate-through-the-visual-search-results"></a>逐一查看圖像式搜尋結果

圖像式搜尋的結果為 `ImageTag` 物件。 每個標記都包含 `ImageAction` 物件清單。 每個`ImageAction`包含`Data`欄位，也就是一份取決於動作類型的值。 例如，您可以逐一查看 `visualSearchResults.Tags` 中的 `ImageTag` 物件，並從中取得 `ImageAction` 標記。 下列範例會列印的詳細資料`PagesIncluding`動作：

```csharp
if (visualSearchResults.Tags.Count > 0)
{
    // List of tags
    foreach (ImageTag t in visualSearchResults.Tags)
    {
        foreach (ImageAction i in t.Actions)
        {
            Console.WriteLine("\r\n" + "ActionType: " + i.ActionType + " WebSearchURL: " + i.WebSearchUrl);

            if (i.ActionType == "PagesIncluding")
            {
                foreach (ImageObject o in (i as ImageModuleAction).Data.Value)
                {
                    Console.WriteLine("ContentURL: " + o.ContentUrl);
                }
            }
        }
    }
}
```

### <a name="pagesincluding-actiontypes"></a>PagesIncluding ActionType

從動作類型取得實際的影像 URL 需要進行轉換，以將 `ActionType` 讀取為 `ImageModuleAction`，其中包含具有值清單的 `Data` 元素。 每個值都是某個影像的 URL。  下列轉換`PagesIncluding`動作類型`ImageModuleAction`和讀取的值：

```csharp
    if (i.ActionType == "PagesIncluding")
    {
        foreach(ImageObject o in (i as ImageModuleAction).Data.Value)
        {
            Console.WriteLine("ContentURL: " + o.ContentUrl);
        }
    }
```

如需這些資料類型的詳細資訊，請參閱[影像 - 圖像式搜尋](https://docs.microsoft.com/rest/api/cognitiveservices/bingvisualsearch/images/visualsearch)。

## <a name="returned-urls"></a>傳回的 URL

完整的應用程式會傳回下列 URL：

|ActionType  |URL  | |
|---------|---------|---------|
|MoreSizes -> WebSearchUrl     |         |
|VisualSearch -> WebSearchUrl     |         |
|ImageById -> WebSearchUrl    |         |
|RelatedSearches -> WebSearchUrl:    |         |
|DocumentLevelSuggestions -> WebSearchUrl:     |         |
|TopicResults -> WebSearchUrl    | https://www.bing.com/cr?IG=3E32CC6CA5934FBBA14ABC3B2E4651F9&CID=1BA795A21EAF6A63175699B71FC36B7C&rd=1&h=BcQifmzdKFyyBusjLxxgO42kzq1Geh7RucVVqvH-900&v=1&r=https%3a%2f%2fwww.bing.com%2fdiscover%2fcanadian%2brocky&p=DevEx,5823.1       |
|ImageResults -> WebSearchUrl    |  https://www.bing.com/cr?IG=3E32CC6CA5934FBBA14ABC3B2E4651F9&CID=1BA795A21EAF6A63175699B71FC36B7C&rd=1&h=PV9GzMFOI0AHZp2gKeWJ8DcveSDRE3fP2jHDKMpJSU8&v=1&r=https%3a%2f%2fwww.bing.com%2fimages%2fsearch%3fq%3doutdoor&p=DevEx,5831.1       |

如上所示，`TopicResults` 和 `ImageResults` 類型包含針對相關影像的查詢。 URL 會連結至 Bing 搜尋結果。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [建立圖像式搜尋單一頁面 web 應用程式](tutorial-bing-visual-search-single-page-app.md)
