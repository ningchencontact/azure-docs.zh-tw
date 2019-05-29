---
title: 快速入門：翻譯文字 (C#) - 翻譯工具文字
titleSuffix: Azure Cognitive Services
description: 在此快速入門中，您可以使用翻譯工具文字 API 搭配 C#，將文字翻譯成另一種語言。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: quickstart
ms.date: 02/21/2019
ms.author: erhopf
ms.openlocfilehash: 7b596c9d16c71db8570024eec94a1c56518b38bf
ms.sourcegitcommit: 6ea7f0a6e9add35547c77eef26f34d2504796565
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/14/2019
ms.locfileid: "65605379"
---
# <a name="quickstart-use-the-translator-text-api-to-translate-a-string-using-c"></a>快速入門：搭配使用翻譯工具文字 API 與 C# 來翻譯字串

在本快速入門中，您將了解如何使用 .NET Core 和翻譯工具文字 REST API，將文字字串從英文翻譯成義大利文和德文。

本快速入門需要 [Azure 認知服務帳戶](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)和翻譯工具文字資源。 如果您還沒有帳戶，可以使用[免費試用](https://azure.microsoft.com/try/cognitive-services/)來取得訂用帳戶金鑰。

## <a name="prerequisites"></a>必要條件

* [.NET SDK](https://www.microsoft.com/net/learn/dotnet/hello-world-tutorial)
* [Json.NET NuGet 套件](https://www.nuget.org/packages/Newtonsoft.Json/)
* [Visual Studio](https://visualstudio.microsoft.com/downloads/)、[Visual Studio Code](https://code.visualstudio.com/download)，或您最愛的文字編輯器。
* 翻譯工具文字的 Azure 訂用帳戶金鑰

## <a name="create-a-net-core-project"></a>建立 .NET Core 專案

開啟新的命令提示字元 (或終端機工作階段) 並執行下列命令：

```console
dotnet new console -o translate-sample
cd translate-sample
```

第一個命令會執行兩項工作。 它會建立新的 .NET 主控台應用程式，並建立名為 `translate-sample` 的目錄。 第二個命令會變更為您專案的目錄。

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

## <a name="create-a-function-to-translate-text"></a>建立可翻譯文字的函式

在 `Program` 類別內建立稱為 `TranslateText` 的函式。 這個類別會封裝用來呼叫 Translate 資源的程式碼，並將結果輸出至主控台。

```csharp
static void TranslateText()
{
  /*
   * The code for your call to the translation service will be added to this
   * function in the next few sections.
   */
}
```

## <a name="set-the-subscription-key-host-name-and-path"></a>設定訂用帳戶金鑰、主機名稱和路徑

將這些行新增至 `TranslateText` 函式。 您會注意到，連同 `api-version` ，已將兩個額外參數附加到 `route`。 這些參數用來設定翻譯輸出。 在此範例中，它會設為德文 (`de`) 和義大利文 (`it`)。 請務必更新訂用帳戶金鑰值。

```csharp
string host = "https://api.cognitive.microsofttranslator.com";
string route = "/translate?api-version=3.0&to=de&to=it";
string subscriptionKey = "YOUR_SUBSCRIPTION_KEY";
```

接下來，我們需要建立及序列化 JSON 物件，該物件包含您要翻譯的文字。 請記住，您可以在 `body` 陣列中傳遞多個物件。

```csharp
System.Object[] body = new System.Object[] { new { Text = @"Hello world!" } };
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
Console.WriteLine(jsonResponse);
Console.WriteLine("Press any key to continue.");
```

## <a name="put-it-all-together"></a>組合在一起

最後一個步驟是在 `Main` 中呼叫 `TranslateText()`。 找出 `static void Main(string[] args)` 並新增下列幾行：

```csharp
TranslateText();
Console.ReadLine();
```

## <a name="run-the-sample-app"></a>執行範例應用程式

就這樣，您準備要執行範例應用程式。 從命令列 (或終端機工作階段)，請瀏覽至您的專案目錄，然後執行：

```console
dotnet run
```

## <a name="sample-response"></a>範例回應

請在此[語言清單](https://docs.microsoft.com/azure/cognitive-services/translator/language-support)中尋找國家/地區縮寫。

```json
[
  {
    "detectedLanguage": {
      "language": "en",
      "score": 1.0
    },
    "translations": [
      {
        "text": "Hallo Welt!",
        "to": "de"
      },
      {
        "text": "Salve, mondo!",
        "to": "it"
      }
    ]
  }
]
```

## <a name="clean-up-resources"></a>清除資源

請務必從您的範例應用程式來源程式碼中移除任何機密資訊，例如訂用帳戶金鑰。

## <a name="next-steps"></a>後續步驟

瀏覽本快速入門及其他文件的範例程式碼，包括音譯和語言識別，以及 GitHub 上的其他「翻譯工具文字」專案範例。

> [!div class="nextstepaction"]
> [瀏覽 GitHub 上的 C# 範例](https://aka.ms/TranslatorGitHub?type=&language=c%23)

## <a name="see-also"></a>另請參閱

* [進行文字音譯](quickstart-csharp-transliterate.md)
* [從輸入識別語言](quickstart-csharp-detect.md)
* [取得替代的翻譯](quickstart-csharp-dictionary.md)
* [取得支援的語言清單](quickstart-csharp-languages.md)
* [從輸入判斷句子長度](quickstart-csharp-sentences.md)
