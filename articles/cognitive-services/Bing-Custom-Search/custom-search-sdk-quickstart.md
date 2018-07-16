---
title: 自訂搜尋 SDK C# 快速入門 | Microsoft Docs
titleSuffix: Cognitive Services
description: 設定自訂搜尋 SDK C# 主控台應用程式。
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: article
ms.date: 01/31/2018
ms.author: rosh
ms.openlocfilehash: 59b208b53bec974433c50c0e2304dc96bd9bd09e
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/23/2018
ms.locfileid: "35367870"
---
# <a name="custom-search-sdk-c-quickstart"></a>自訂搜尋 SDK C# 快速入門

Bing 自訂搜尋 SDK 包含用於實體搜尋以及剖析結果的 REST API 功能。

## <a name="application-dependencies"></a>應用程式相依性

若要使用 Bing 自訂搜尋 SDK 來設定主控台應用程式，請在 Visual Studio 中瀏覽至 [方案總管] 中的 `Manage NuGet Packages` 選項。 新增 `Microsoft.Azure.CognitiveServices.Search.CustomSearch` 套件。

安裝 [NuGet 自訂搜尋](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.CustomSearch/1.2.0)套件也會一併安裝相依性，包括下列組件：
* Microsoft.Rest.ClientRuntime
* Microsoft.Rest.ClientRuntime.Azure
* Newtonsoft.Json

## <a name="entity-search-client"></a>實體搜尋用戶端

若要建立 CustomSearchAPI 用戶端的執行個體，請新增 using 指示詞：
```
using Microsoft.Azure.CognitiveServices.Search.CustomSearch;

```

具現化自訂搜尋用戶端：以您的存取金鑰和在[我的執行個體](https://www.customsearch.ai/)指派的 API 端點組態取代 `YOUR-CUSTOM-SEARCH-KEY` 和 `YOUR-CUSTOM-CONFIG-ID`。
```
var client = new CustomSearchAPI(new ApiKeyServiceClientCredentials("YOUR-CUSTOM-SEARCH-KEY"));

```
使用用戶端以利用查詢文字搜尋：
```
var webData = client.CustomInstance.SearchAsync(query: "Xbox", customConfig: Int32.Parse("YOUR-CUSTOM-CONFIG-ID")).Result;

```
## <a name="parse-the-results"></a>剖析結果

`SearchAsync` 方法會傳回包含 `WebPages` (如果已就此查詢找到) 的 `WebData` 物件。 此程式碼會尋找第一個結果，並取得其 `Name` 和 `URL`。
```
var webData = client.CustomInstance.SearchAsync(query: "Xbox", customConfig: Int32.Parse("YOUR-CUSTOM-CONFIG-ID")).Result;
 
Console.WriteLine("Searched for Query# \" Xbox \"");

 //WebPages
if (webData?.WebPages?.Value?.Count > 0)
{
    // find the first web page
    var firstWebPagesResult = webData.WebPages.Value.FirstOrDefault();

    if (firstWebPagesResult != null)
    {
        Console.WriteLine("Webpage Results#{0}", webData.WebPages.Value.Count);
        Console.WriteLine("First web page name: {0} ", firstWebPagesResult.Name);
        Console.WriteLine("First web page URL: {0} ", firstWebPagesResult.Url);
    }
    else
    {
        Console.WriteLine("Couldn't find web results!");
    }
}
else
{
    Console.WriteLine("Didn't see any Web data..");
}

```
## <a name="complete-console-application"></a>完成主控台應用程式

下列程式碼會就 "Xbox" 查詢進行搜尋，並列出第一個 Web 結果的 `Name` 和 `URL`。
```
using System;
using System.Linq;
using Microsoft.Azure.CognitiveServices.Search.CustomSearch;

namespace CustomSrchSDK
{
    class Program
    {
        static void Main(string[] args)
        {

            var client = new CustomSearchAPI(new ApiKeyServiceClientCredentials("YOUR-CUSTOM-SEARCH-KEY"));

            try
            {
                // This will look up a single query (Xbox).
                var webData = client.CustomInstance.SearchAsync(query: "Xbox", customConfig: Int32.Parse("YOUR-CUSTOM-CONFIG-ID")).Result;
                Console.WriteLine("Searched for Query# \" Xbox \"");

                //WebPages
                if (webData?.WebPages?.Value?.Count > 0)
                {
                    // find the first web page
                    var firstWebPagesResult = webData.WebPages.Value.FirstOrDefault();

                    if (firstWebPagesResult != null)
                    {
                        Console.WriteLine("Webpage Results#{0}", webData.WebPages.Value.Count);
                        Console.WriteLine("First web page name: {0} ", firstWebPagesResult.Name);
                        Console.WriteLine("First web page URL: {0} ", firstWebPagesResult.Url);
                    }
                    else
                    {
                        Console.WriteLine("Couldn't find web results!");
                    }
                }
                else
                {
                    Console.WriteLine("Didn't see any Web data..");
                }
            }
            catch (Exception ex)
            {
                Console.WriteLine("Encountered exception. " + ex.Message);
            }

            Console.WriteLine("Any key to exit...");
            Console.ReadKey();
        }

    }
}


```

## <a name="next-steps"></a>後續步驟

[認知服務 .NET SDK 範例](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)
