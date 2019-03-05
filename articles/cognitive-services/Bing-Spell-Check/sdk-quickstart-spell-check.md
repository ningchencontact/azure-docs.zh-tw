---
title: 快速入門：使用適用於 C# 的 Bing 拼字檢查 SDK 進行檢查拼字
titlesuffix: Azure Cognitive Services
description: 開始使用 Bing 拼字檢查 REST API 來檢查拼字和文法。
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 02/20/2019
ms.author: v-gedod
ms.openlocfilehash: ae558c40a3eb30cb239b19a59542d9d83d5a9566
ms.sourcegitcommit: 24906eb0a6621dfa470cb052a800c4d4fae02787
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2019
ms.locfileid: "56886140"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-sdk-for-c"></a>快速入門：使用適用於 C# 的 Bing 拼字檢查 SDK 進行檢查拼字

透過本快速入門使用適用於 C# 的 Bing 拼字檢查 SDK 開始進行拼字檢查。 雖然 Bing 拼字檢查具有與大部分程式設計語言相容的 REST API，但 SDK 會提供簡單的方法，將服務整合到您的應用程式。 此範例的原始程式碼可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/samples/SpellCheck) 上找到。

## <a name="application-dependencies"></a>應用程式相依性

* 任何一版的 [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/)。
* Bing 拼字檢查 [NuGet 套件](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.SpellCheck)

若要將 Bing 拼字檢查 SDK 新增至您的專案，請在 Visual Studio 中從 [方案總管] 按一下 `Manage NuGet Packages`。 新增 `Microsoft.Azure.CognitiveServices.Language.SpellCheck` 套件。 此套件也會安裝下列相依性：

* Microsoft.Rest.ClientRuntime
* Microsoft.Rest.ClientRuntime.Azure
* Newtonsoft.Json

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>建立應用程式並將其初始化

1. 在 Visual Studio 中建立新的 C# 主控台解決方案。 然後新增下列 `using` 陳述式。
    
    ```csharp
    using System;
    using System.Linq;
    using System.Threading.Tasks;
    using Microsoft.Azure.CognitiveServices.Language.SpellCheck;
    using Microsoft.Azure.CognitiveServices.Language.SpellCheck.Models;
    ```

2. 建立新的類別。 然後建立名為 `SpellCheckCorrection()` 的非同步函式，該函式會採用訂用帳戶金鑰並傳送拼字檢查要求。

3. 建立新的 `ApiKeyServiceClientCredentials` 物件以具現化用戶端。 

    ```csharp
    public static class SpellCheckSample{
        public static async Task SpellCheckCorrection(string key){
            var client = new SpellCheckClient(new ApiKeyServiceClientCredentials(key));
        }
        //...
    }
    ```

## <a name="send-the-request-and-read-the-response"></a>傳送要求並讀取回應

1. 在以上建立的函式中，執行下列步驟。 透過用戶端傳送拼字檢查要求。 將要檢查的文字新增至 `text` 參數，並將模式設定為 `proof`。  
    
    ```csharp
    var result = await client.SpellCheckerWithHttpMessagesAsync(text: "Bill Gatas", mode: "proof");
    ```

2. 取得第一個拼字檢查結果 (如果有的話)。 列印所傳回的第一個拼錯的字組 (Token)、Token 類型和建議數目。

    ```csharp
    if (firstspellCheckResult != null){
        var firstspellCheckResult = result.Body.FlaggedTokens.FirstOrDefault();
    
        Console.WriteLine("SpellCheck Results#{0}", result.Body.FlaggedTokens.Count);
        Console.WriteLine("First SpellCheck Result token: {0} ", firstspellCheckResult.Token);
        Console.WriteLine("First SpellCheck Result Type: {0} ", firstspellCheckResult.Type);
        Console.WriteLine("First SpellCheck Result Suggestion Count: {0} ", firstspellCheckResult.Suggestions.Count);
    }
    ```

3. 取得第一個建議的修正 (如果有的話)。列印建議分數，以及建議的字組。 

    ```csharp
            var suggestions = firstspellCheckResult.Suggestions;

            if (suggestions?.Count > 0)
            {
                var firstSuggestion = suggestions.FirstOrDefault();
                Console.WriteLine("First SpellCheck Suggestion Score: {0} ", firstSuggestion.Score);
                Console.WriteLine("First SpellCheck Suggestion : {0} ", firstSuggestion.Suggestion);
            }
}

## Next steps

> [!div class="nextstepaction"]
> [Create a single page web-app](tutorials/spellcheck.md)

- [What is the Bing Spell Check API?](overview.md)
- [Bing Spell Check C# SDK reference guide](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/bingspellcheck?view=azure-dotnet)