---
title: 快速入門：取得句子長度 (C#) - 翻譯工具文字 API
titleSuffix: Azure Cognitive Services
description: 在此快速入門中，您將了解如何使用 .NET Core 和翻譯工具文字 API 來判斷句子長度。
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: quickstart
ms.date: 06/13/2019
ms.author: swmachan
ms.openlocfilehash: 1c711639d5566f8909543901e30bd774abbe8a0d
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2019
ms.locfileid: "67704578"
---
# <a name="quickstart-use-the-translator-text-api-to-determine-sentence-length-using-c"></a>快速入門：搭配使用翻譯工具文字 API 與 C# 來判斷句子長度

在本快速入門中，您將了解如何使用 .NET Core、C# 7.1 或更新版本和翻譯工具文字 API 來判斷句子長度。

本快速入門需要 [Azure 認知服務帳戶](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)和翻譯工具文字資源。 如果您還沒有帳戶，可以使用[免費試用](https://azure.microsoft.com/try/cognitive-services/)來取得訂用帳戶金鑰。

>[!TIP]
> 如果您想要同時查看所有程式碼，您可以在 [GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-C-Sharp) 上找到此範例的原始程式碼。

## <a name="prerequisites"></a>必要條件

* C# 7.1 或更新版本
* [.NET SDK](https://www.microsoft.com/net/learn/dotnet/hello-world-tutorial)
* [Json.NET NuGet 套件](https://www.nuget.org/packages/Newtonsoft.Json/)
* [Visual Studio](https://visualstudio.microsoft.com/downloads/)、[Visual Studio Code](https://code.visualstudio.com/download)，或您最愛的文字編輯器。
* 翻譯工具文字的 Azure 訂用帳戶金鑰

## <a name="create-a-net-core-project"></a>建立 .NET Core 專案

開啟新的命令提示字元 (或終端機工作階段) 並執行下列命令：

```console
dotnet new console -o sentences-sample
cd sentences-sample
```

第一個命令會執行兩項工作。 它會建立新的 .NET 主控台應用程式，並建立名為 `sentences-sample` 的目錄。 第二個命令會變更為您專案的目錄。

接下來，您必須安裝 Json.Net。 從您的專案目錄中，執行：

```console
dotnet add package Newtonsoft.Json --version 11.0.2
```

## <a name="select-the-c-language-version"></a>選取 C# 語言版本

此快速入門需要 C# 7.1 或更新版本。 有幾個方式可以變更您專案的 C# 版本。 在本指南中，我們將示範如何調整 `sentences-sample.csproj` 檔案。 如需所有可用的選項，例如在 Visual Studio 中變更語言，請參閱[選取 C# 語言版本](https://docs.microsoft.com/dotnet/csharp/language-reference/configure-language-version)。

開啟您的專案，然後開啟 `sentences-sample.csproj`。 確定 `LangVersion` 已設為 7.1 或更新版本。 如果沒有語言版本的屬性群組，請加入這些程式行：

```xml
<PropertyGroup>
   <LangVersion>7.1</LangVersion>
</PropertyGroup>
```

## <a name="add-required-namespaces-to-your-project"></a>將所需的命名空間新增至您的專案

您先前建立專案時執行的 `dotnet new console` 命令，包括 `Program.cs`。 這個檔案是您將放置應用程式程式碼的地方。 開啟 `Program.cs`，並取代現有的 using 陳述式。 這些陳述式可確保您有權存取建置和執行範例應用程式所需的所有類型。

```csharp
using System;
using System.Net.Http;
using System.Text;
using System.Threading.Tasks;
// Install Newtonsoft.Json with NuGet
using Newtonsoft.Json;
```

## <a name="create-classes-for-the-json-response"></a>建立 JSON 回應的類別

接下來，我們將建立在還原序列化翻譯工具文字 API 所傳回的 JSON 回應時所使用的類別。

```csharp
/// <summary>
/// The C# classes that represents the JSON returned by the Translator Text API.
/// </summary>
public class BreakSentenceResult
{
    public int[] SentLen { get; set; }
    public DetectedLanguage DetectedLanguage { get; set; }
}

public class DetectedLanguage
{
    public string Language { get; set; }
    public float Score { get; set; }
}
```

## <a name="create-a-function-to-determine-sentence-length"></a>建立可判斷句子長度的函式

在 `Program` 類別內建立稱為 `BreakSentence()` 的函式。 此函式會採用四個引數：`subscriptionKey`、`host`、`route` 和 `inputText`。

```csharp
static public async Task BreakSentenceRequest(string subscriptionKey, string host, string route, string inputText)
{
  /*
   * The code for your call to the translation service will be added to this
   * function in the next few sections.
   */
}
```

## <a name="serialize-the-break-sentence-request"></a>將斷句要求序列化

接下來，您需要建立及序列化包含文字的 JSON 物件。 請記住，您可以在 `body` 陣列中傳遞多個物件。

```csharp
object[] body = new object[] { new { Text = inputText } };
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
// Build the request.
// Set the method to POST
request.Method = HttpMethod.Post;
// Construct the URI and add headers.
request.RequestUri = new Uri(host + route);
request.Content = new StringContent(requestBody, Encoding.UTF8, "application/json");
request.Headers.Add("Ocp-Apim-Subscription-Key", subscriptionKey);

// Send the request and get response.
HttpResponseMessage response = await client.SendAsync(request).ConfigureAwait(false);
// Read response as a string.
string result = await response.Content.ReadAsStringAsync();
// Deserialize the response using the classes created earlier.
BreakSentenceResult[] deserializedOutput = JsonConvert.DeserializeObject<BreakSentenceResult[]>(result);
foreach (BreakSentenceResult o in deserializedOutput)
{
    Console.WriteLine("The detected language is '{0}'. Confidence is: {1}.", o.DetectedLanguage.Language, o.DetectedLanguage.Score);
    Console.WriteLine("The first sentence length is: {0}", o.SentLen[0]);
}
```

如果您使用認知服務的多服務訂用帳戶，您也必須在要求參數中包含 `Ocp-Apim-Subscription-Region`。 [深入了解如何使用多服務訂用帳戶進行驗證](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication)。

## <a name="put-it-all-together"></a>組合在一起

最後一個步驟是在 `Main` 中呼叫 `BreakSentenceRequest()`。 找出 `static void Main(string[] args)` 並將其取代為下列程式碼：

```csharp
static async Task Main(string[] args)
{
    // This is our main function.
    // Output languages are defined in the route.
    // For a complete list of options, see API reference.
    string subscriptionKey = "YOUR_TRANSLATOR_TEXT_KEY_GOES_HERE";
    string host = "https://api.cognitive.microsofttranslator.com";
    string route = "/breaksentence?api-version=3.0";
    // Feel free to use any string.
    string breakSentenceText = @"How are you doing today? The weather is pretty pleasant. Have you been to the movies lately?";
    await BreakSentenceRequest(subscriptionKey, host, route, breakSentenceText);
}
```

您會發現，您在 `Main` 中宣告了 `subscriptionKey`、`host`、`route`，以及用來評估 `breakSentenceText` 的文字。

## <a name="run-the-sample-app"></a>執行範例應用程式

就這樣，您準備要執行範例應用程式。 從命令列 (或終端機工作階段)，請瀏覽至您的專案目錄，然後執行：

```console
dotnet run
```

## <a name="sample-response"></a>範例回應

執行範例之後，您應該會看到下列內容列印到終端機：

```bash
The detected language is \'en\'. Confidence is: 1.
The first sentence length is: 25
```

此訊息是從原始 JSON 建置的，會顯示如下：

```json
[
    {
        "detectedLanguage":
        {
            "language":"en",
            "score":1.0
        },
        "sentLen":[25,32,35]
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
* [取得替代的翻譯](quickstart-csharp-dictionary.md)
* [取得支援的語言清單](quickstart-csharp-languages.md)
* [從輸入判斷句子長度](quickstart-csharp-sentences.md)
