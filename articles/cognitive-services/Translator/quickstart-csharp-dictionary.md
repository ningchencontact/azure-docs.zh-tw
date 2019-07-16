---
title: 快速入門：在雙語字典中查詢單字 (C#) - 翻譯工具文字 API
titleSuffix: Azure Cognitive Services
description: 在本快速入門中，您可以使用 .NET Core 和翻譯工具文字 API，取得字詞的替代翻譯，以及這些替代翻譯的使用範例。
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: quickstart
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: 1f80f9b0f044fe8b32a555b0509e14cd2172dd0a
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2019
ms.locfileid: "67704604"
---
# <a name="quickstart-look-up-words-with-bilingual-dictionary-using-c"></a>快速入門：使用 C# 在雙語字典中查詢單字

在本快速入門中，您可以使用 .NET Core 和翻譯工具文字 API，取得字詞的替代翻譯，以及這些替代翻譯的使用範例。

本快速入門需要 [Azure 認知服務帳戶](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)和翻譯工具文字資源。 如果您還沒有帳戶，可以使用[免費試用](https://azure.microsoft.com/try/cognitive-services/)來取得訂用帳戶金鑰。

>[!TIP]
> 如果您想要同時查看所有程式碼，您可以在 [GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-C-Sharp) 上找到此範例的原始程式碼。

## <a name="prerequisites"></a>必要條件

* [.NET SDK](https://www.microsoft.com/net/learn/dotnet/hello-world-tutorial)
* [Json.NET NuGet 套件](https://www.nuget.org/packages/Newtonsoft.Json/)
* [Visual Studio](https://visualstudio.microsoft.com/downloads/)、[Visual Studio Code](https://code.visualstudio.com/download)，或您最愛的文字編輯器。
* 翻譯工具文字的 Azure 訂用帳戶金鑰

## <a name="create-a-net-core-project"></a>建立 .NET Core 專案

開啟新的命令提示字元 (或終端機工作階段) 並執行下列命令：

```console
dotnet new console -o alternate-sample
cd alternate-sample
```

第一個命令會執行兩項工作。 它會建立新的 .NET 主控台應用程式，並建立名為 `alternate-sample` 的目錄。 第二個命令會變更為您專案的目錄。

接下來，您必須安裝 Json.Net。 從您的專案目錄中，執行：

```console
dotnet add package Newtonsoft.Json --version 11.0.2
```

## <a name="add-required-namespaces-to-your-project"></a>將所需的命名空間新增至您的專案

您先前建立專案時執行的 `dotnet new console` 命令，包括 `Program.cs`。 這個檔案是您將放置應用程式程式碼的地方。 開啟 `Program.cs`，並取代現有的 using 陳述式。 這些陳述式可確保您有權存取建置和執行範例應用程式所需的所有類型。

```csharp
using System;
using System.Net.Http;
using System.Text;
using Newtonsoft.Json;
```

## <a name="create-a-function-to-get-alternate-translations"></a>建立可取得替代翻譯的函式

在 `Program` 類別內建立稱為 `AltTranslation` 的函式。 這個類別會封裝用來呼叫 Dictionary 資源的程式碼，並將結果輸出至主控台。

```csharp
static void AltTranslation()
{
  /*
   * The code for your call to the translation service will be added to this
   * function in the next few sections.
   */
}
```

## <a name="set-the-subscription-key-host-name-and-path"></a>設定訂用帳戶金鑰、主機名稱和路徑

將這些行新增至 `AltTranslation` 函式。 您會注意到，連同 `api-version` ，已將兩個額外參數附加到 `route`。 這些參數用來設定翻譯輸入和輸出。 在此範例中為英文 (`en`) 和西班牙文 (`es`)。

```csharp
string host = "https://api.cognitive.microsofttranslator.com";
string route = "/dictionary/lookup?api-version=3.0&from=en&to=es";
string subscriptionKey = "YOUR_SUBSCRIPTION_KEY";
```

接下來，我們需要建立及序列化 JSON 物件，該物件包含您要翻譯的文字。 請記住，您可以在 `body` 陣列中傳遞多個物件。

```csharp
System.Object[] body = new System.Object[] { new { Text = @"Elephants" } };
var requestBody = JsonConvert.SerializeObject(body);
```



## <a name="instantiate-the-client-and-make-a-request"></a>將用戶端具現化並提出要求

這幾行會將 `HttpClient` 和 `HttpRequestMessage` 具現化：

```csharp
using (var client = new HttpClient())
using (var request = new HttpRequestMessage())
{
  // In the next few sections you'll add code to construct the request.
}
```

## <a name="construct-the-request-and-print-the-response"></a>建構要求並列印回應

在 `HttpRequestMessage` 內，您將會：

* 宣告 HTTP 方法
* 建構要求 URI
* 插入要求本文 (序列化的 JSON 物件)
* 新增必要的標頭
* 提出非同步要求
* 列印回應

將下列程式碼新增至 `HttpRequestMessage`：

```csharp
// Set the method to POST
request.Method = HttpMethod.Post;

// Construct the full URI
request.RequestUri = new Uri(host + route);

// Add the serialized JSON object to your request
request.Content = new StringContent(requestBody, Encoding.UTF8, "application/json");

// Add the authorization header
request.Headers.Add("Ocp-Apim-Subscription-Key", subscriptionKey);

// Send request, get response
var response = client.SendAsync(request).Result;
var jsonResponse = response.Content.ReadAsStringAsync().Result;

// Print the response
Console.WriteLine(PrettyPrint(jsonResponse));
Console.WriteLine("Press any key to continue.");
```

新增 `PrettyPrint` 以新增 JSON 回應的格式：
```csharp
static string PrettyPrint(string s)
{
    return JsonConvert.SerializeObject(JsonConvert.DeserializeObject(s), Formatting.Indented);
}
```

如果您使用認知服務的多服務訂用帳戶，您也必須在要求參數中包含 `Ocp-Apim-Subscription-Region`。 [深入了解如何使用多服務訂用帳戶進行驗證](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication)。

## <a name="put-it-all-together"></a>組合在一起

最後一個步驟是在 `Main` 中呼叫 `AltTranslation()`。 找出 `static void Main(string[] args)` 並新增下列幾行：

```csharp
AltTranslation();
Console.ReadLine();
```

## <a name="run-the-sample-app"></a>執行範例應用程式

就這樣，您準備要執行範例應用程式。 從命令列 (或終端機工作階段)，請瀏覽至您的專案目錄，然後執行：

```console
dotnet run
```

## <a name="sample-response"></a>範例回應

```json
[
    {
        "displaySource": "elephants",
        "normalizedSource": "elephants",
        "translations": [
            {
                "backTranslations": [
                    {
                        "displayText": "elephants",
                        "frequencyCount": 1207,
                        "normalizedText": "elephants",
                        "numExamples": 5
                    }
                ],
                "confidence": 1.0,
                "displayTarget": "elefantes",
                "normalizedTarget": "elefantes",
                "posTag": "NOUN",
                "prefixWord": ""
            }
        ]
    }
]
```

## <a name="clean-up-resources"></a>清除資源

請務必從您的範例應用程式來源程式碼中移除任何機密資訊，例如訂用帳戶金鑰。

## <a name="next-steps"></a>後續步驟

查看 API 參考，以了解您可以使用翻譯工具文字 API 執行的所有作業。

> [!div class="nextstepaction"]
> [API 參考](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)

## <a name="see-also"></a>另請參閱

* [翻譯文字](quickstart-csharp-translate.md)
* [進行文字音譯](quickstart-csharp-transliterate.md)
* [從輸入識別語言](quickstart-csharp-detect.md)
* [取得支援的語言清單](quickstart-csharp-languages.md)
* [從輸入判斷句子長度](quickstart-csharp-sentences.md)
