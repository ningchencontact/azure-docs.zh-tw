---
title: 快速入門：使用適用於 C# 的 Bing 實體搜尋 SDK 來搜尋實體
titleSuffix: Azure Cognitive Services
description: 使用本快速入門，透過適用於 C# 的 Bing 實體搜尋 SDK 來搜尋實體。
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: quickstart
ms.date: 02/01/2019
ms.author: v-gedod
ms.openlocfilehash: 6a365ef5421de3ceb31c5cc78a424f786f174ab3
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/07/2019
ms.locfileid: "55861913"
---
# <a name="send-a-search-request-with-the-bing-entity-search-sdk-for-c"></a>使用適用於 C# 的 Bing 實體搜尋 SDK 來傳送搜尋要求

使用本快速入門，透過適用於 C# 的 Bing 實體搜尋 SDK 開始搜尋實體。 雖然 Bing 實體搜尋具有與大部分程式設計語言相容的 REST API，但此 SDK 可提供簡單的方法，將服務整合到您的應用程式。 此範例的原始程式碼可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingEntitySearch) 上找到。


## <a name="prerequisites"></a>必要條件

* 任何一版的 [Visual Studio 2017](https://www.visualstudio.com/downloads/)。
* [Json.NET](https://www.newtonsoft.com/json) 架構 (以 NuGet 套件形式提供)。
* 如果您使用 Linux/MacOS，則可以使用 [Mono](http://www.mono-project.com/)來執行此應用程式。
* [Bing 新聞搜尋 SDK 的 NuGet 套件](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.EntitySearch/1.2.0)。 安裝此套件也會安裝：
    * Microsoft.Rest.ClientRuntime
    * Microsoft.Rest.ClientRuntime.Azure
    * Newtonsoft.Json

若要將 Bing 實體搜尋 SDK 新增至 Visual Studio 專案，請使用 [方案總管] 中的 `Manage NuGet Packages` 選項，然後新增 `Microsoft.Azure.CognitiveServices.Search.EntitySearch` 套件。


[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../../includes/cognitive-services-bing-entity-search-signup-requirements.md)]

## <a name="create-and-initialize-an-application"></a>建立應用程式並將其初始化

1. 在 Visual Studio 中建立新的 C# 主控台解決方案。 然後將下列內容新增至主要程式碼檔案。

    ```csharp
    using System;
    using System.Linq;
    using System.Text;
    using Microsoft.Azure.CognitiveServices.Search.EntitySearch;
    using Microsoft.Azure.CognitiveServices.Search.EntitySearch.Models;
    using Newtonsoft.Json;
    ```

## <a name="create-a-client-and-send-a-search-request"></a>建立用戶端並傳送搜尋要求

2. 建立新的搜尋用戶端。 建立新的 `ApiKeyServiceClientCredentials` 以新增訂用帳戶金鑰。

    ```csharp
    var client = new EntitySearchAPI(new ApiKeyServiceClientCredentials("YOUR-ACCESS-KEY"));
    ```

3. 使用用戶端的 `Entities.Search()` 函式來搜尋您的查詢：
    
    ```csharp
    var entityData = client.Entities.Search(query: "Satya Nadella");
    ```

## <a name="get-and-print-an-entity-description"></a>取得並列印實體描述

1. 如果此 API 傳回搜尋結果，請從 `entityData` 取得主要實體。

    ```csharp
    var mainEntity = entityData.Entities.Value.Where(thing => thing.EntityPresentationInfo.EntityScenario == EntityScenario.DominantEntity).FirstOrDefault();
    ```

2. 列印主要實體的描述 

    ```csharp
    Console.WriteLine(mainEntity.Description);
    ```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [建立單頁 Web 應用程式](../tutorial-bing-entities-search-single-page-app.md)

* [什麼是 Bing 實體搜尋 API？](../overview.md )