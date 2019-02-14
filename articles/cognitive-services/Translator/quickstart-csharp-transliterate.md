---
title: 快速入門：轉換文字字集 (C#) - 翻譯工具文字
titleSuffix: Azure Cognitive Services
description: 在本快速入門中，您將了解如何使用 .NET Core 和翻譯工具文字 REST API，將文字從一個字集直譯 (轉換) 成另一個字集。 在此範例中，日文會直譯為使用拉丁字母。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: quickstart
ms.date: 11/21/2018
ms.author: erhopf
ms.openlocfilehash: 45ecb3bb17f0064853e8e31bb96a04c62afa9608
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/07/2019
ms.locfileid: "55881227"
---
# <a name="quickstart-use-the-translator-text-api-to-transliterate-text-using-c"></a>快速入門：搭配使用翻譯工具文字 API 與 C# 進行文字音譯

在本快速入門中，您將了解如何使用 .NET Core (C#) 和翻譯工具文字 REST API，將文字從一個字集直譯 (轉換) 成另一個字集。 在所提供的範例中，日文會直譯為使用拉丁字母。

本快速入門需要 [Azure 認知服務帳戶](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)和翻譯工具文字資源。 如果您還沒有帳戶，可以使用[免費試用](https://azure.microsoft.com/try/cognitive-services/)來取得訂用帳戶金鑰。

## <a name="prerequisites"></a>必要條件

* [.NET SDK](https://www.microsoft.com/net/learn/dotnet/hello-world-tutorial)
* [Json.NET NuGet 套件](https://www.nuget.org/packages/Newtonsoft.Json/)
* [Visual Studio](https://visualstudio.microsoft.com/downloads/)、[Visual Studio Code](https://code.visualstudio.com/download)，或您最愛的文字編輯器。
* 翻譯工具文字的 Azure 訂用帳戶金鑰

## <a name="create-a-net-core-project"></a>建立 .NET Core 專案

開啟新的命令提示字元 (或終端機工作階段) 並執行下列命令：

```console
dotnet new console -o transliterate-sample
cd transliterate-sample
```

第一個命令會執行兩項工作。 它會建立新的 .NET 主控台應用程式，並建立名為 `transliterate-sample` 的目錄。 第二個命令會變更為您專案的目錄。

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

## <a name="create-a-function-to-transliterate-text"></a>建立可直譯文字的函式

在 `Program` 類別內建立稱為 `TransliterateText` 的函式。 這個類別會封裝用來呼叫 Transliterate 資源的程式碼，並將結果輸出至主控台。

```csharp
static void TransliterateText()
{
  /*
   * The code for your call to the translation service will be added to this
   * function in the next few sections.
   */
}
```

## <a name="set-the-subscription-key-host-name-and-path"></a>設定訂用帳戶金鑰、主機名稱和路徑

將這些行新增至 `TransliterateText` 函式。 您會注意到，連同 `api-version` ，已將兩個額外參數附加到 `route`。 這些參數用來設定輸入語言，以及用於直譯的指令碼。 在此範例中，它會設為日文 (`jpan`) 和英文 (`latn`)。 請務必更新訂用帳戶金鑰值。

```csharp
string host = "https://api.cognitive.microsofttranslator.com";
string route = "/transliterate?api-version=3.0&language=ja&fromScript=jpan&toScript=latn";
string subscriptionKey = "YOUR_SUBSCRIPTION_KEY";
```

接下來，我們需要建立及序列化 JSON 物件，該物件包含您要直譯的文字。 請記住，您可以在 `body` 陣列中傳遞多個物件。

```csharp
System.Object[] body = new System.Object[] { new { Text = @"こんにちは" } };
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

最後一個步驟是在 `Main` 中呼叫 `TransliterateText()`。 找出 `static void Main(string[] args)` 並新增下列幾行：

```csharp
TransliterateText();
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
        "script": "latn",
        "text": "konnnichiha"
    }
]
```

## <a name="clean-up-resources"></a>清除資源

請務必從您的範例應用程式來源程式碼中移除任何機密資訊，例如訂用帳戶金鑰。

## <a name="next-steps"></a>後續步驟

瀏覽本快速入門及其他文件的範例程式碼，包括翻譯和語言識別，以及 GitHub 上的其他「翻譯工具文字」專案範例。

> [!div class="nextstepaction"]
> [瀏覽 GitHub 上的 C# 範例](https://aka.ms/TranslatorGitHub?type=&language=c%23)

## <a name="see-also"></a>另請參閱

* [翻譯文字](quickstart-csharp-translate.md)
* [從輸入識別語言](quickstart-csharp-detect.md)
* [取得替代的翻譯](quickstart-csharp-dictionary.md)
* [取得支援的語言清單](quickstart-csharp-languages.md)
* [從輸入判斷句子長度](quickstart-csharp-sentences.md)
