---
title: 快速入門：取得支援的語言清單 (C#) - 翻譯工具文字 API
titleSuffix: Azure Cognitive Services
description: 在本快速入門中，您可以使用翻譯工具文字 API，取得翻譯、直譯及查閱字典時所支援的語言清單。
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: quickstart
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: 5b5bf33c70b9f8827f1f55378e9caf4d1c858bed
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/28/2019
ms.locfileid: "67449412"
---
# <a name="quickstart-use-the-translator-text-api-to-get-a-list-of-supported-languages-using-c"></a>快速入門：搭配使用翻譯工具文字 API 與 C# 來取得支援的語言清單

在本快速入門中，您可以使用翻譯工具文字 API，取得翻譯、直譯及查閱字典時所支援的語言清單。

## <a name="prerequisites"></a>必要條件

* [.NET SDK](https://www.microsoft.com/net/learn/dotnet/hello-world-tutorial)
* [Json.NET NuGet 套件](https://www.nuget.org/packages/Newtonsoft.Json/)
* [Visual Studio](https://visualstudio.microsoft.com/downloads/)、[Visual Studio Code](https://code.visualstudio.com/download)，或您最愛的文字編輯器

## <a name="create-a-net-core-project"></a>建立 .NET Core 專案

開啟新的命令提示字元 (或終端機工作階段) 並執行下列命令：

```console
dotnet new console -o languages-sample
cd languages-sample
```

第一個命令會執行兩項工作。 它會建立新的 .NET 主控台應用程式，並建立名為 `languages-sample` 的目錄。 第二個命令會變更為您專案的目錄。

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

## <a name="create-a-function-to-get-a-list-of-languages"></a>建立可取得語言清單的函式

在 `Program` 類別內建立稱為 `GetLanguages` 的函式。 這個類別會封裝用來呼叫 Languages 資源的程式碼，並將結果輸出至主控台。

```csharp
static void GetLanguages()
{
  /*
   * The code for your call to the translation service will be added to this
   * function in the next few sections.
   */
}
```

## <a name="set-the-host-name-and-path"></a>設定主機名稱和路俓

將這些行新增至 `GetLanguages` 函式。

```csharp
string host = "https://api.cognitive.microsofttranslator.com";
string route = "/languages?api-version=3.0";
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
* 新增必要的標頭
* 提出非同步要求
* 列印回應

將下列程式碼新增至 `HttpRequestMessage`：

```csharp
// Set the method to GET
request.Method = HttpMethod.Get;
// Construct the full URI
request.RequestUri = new Uri(host + route);
// Send request, get response
var response = client.SendAsync(request).Result;
var jsonResponse = response.Content.ReadAsStringAsync().Result;
// Pretty print the response
Console.WriteLine(PrettyPrint(jsonResponse));
Console.WriteLine("Press any key to continue.");
```

如果您使用認知服務的多服務訂用帳戶，您也必須在要求參數中包含 `Ocp-Apim-Subscription-Region`。 [深入了解如何使用多服務訂用帳戶進行驗證](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication)。 

若要使用「美化顯示」(用於回應的格式) 來列印回應，請將此函式新增至 Program 類別：
```
static string PrettyPrint(string s)
{
    return JsonConvert.SerializeObject(JsonConvert.DeserializeObject(s), Formatting.Indented);
}
```

## <a name="put-it-all-together"></a>組合在一起

最後一個步驟是在 `Main` 中呼叫 `GetLanguages()`。 找出 `static void Main(string[] args)` 並新增下列幾行：

```csharp
GetLanguages();
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
{
    "translation": {
        "af": {
            "name": "Afrikaans",
            "nativeName": "Afrikaans",
            "dir": "ltr"
        },
        "ar": {
            "name": "Arabic",
            "nativeName": "العربية",
            "dir": "rtl"
        },
        ...
    },
    "transliteration": {
        "ar": {
            "name": "Arabic",
            "nativeName": "العربية",
            "scripts": [
                {
                    "code": "Arab",
                    "name": "Arabic",
                    "nativeName": "العربية",
                    "dir": "rtl",
                    "toScripts": [
                        {
                            "code": "Latn",
                            "name": "Latin",
                            "nativeName": "اللاتينية",
                            "dir": "ltr"
                        }
                    ]
                },
                {
                    "code": "Latn",
                    "name": "Latin",
                    "nativeName": "اللاتينية",
                    "dir": "ltr",
                    "toScripts": [
                        {
                            "code": "Arab",
                            "name": "Arabic",
                            "nativeName": "العربية",
                            "dir": "rtl"
                        }
                    ]
                }
            ]
        },
      ...
    },
    "dictionary": {
        "af": {
            "name": "Afrikaans",
            "nativeName": "Afrikaans",
            "dir": "ltr",
            "translations": [
                {
                    "name": "English",
                    "nativeName": "English",
                    "dir": "ltr",
                    "code": "en"
                }
            ]
        },
        "ar": {
            "name": "Arabic",
            "nativeName": "العربية",
            "dir": "rtl",
            "translations": [
                {
                    "name": "English",
                    "nativeName": "English",
                    "dir": "ltr",
                    "code": "en"
                }
            ]
        },
      ...
    }
}
```

## <a name="clean-up-resources"></a>清除資源

請務必從您的範例應用程式來源程式碼中移除任何機密資訊，例如訂用帳戶金鑰。

## <a name="next-steps"></a>後續步驟

瀏覽本快速入門及其他文件的範例程式碼，包括音譯和語言識別，以及 GitHub 上的其他「翻譯工具文字」專案範例。

> [!div class="nextstepaction"]
> [瀏覽 GitHub 上的 C# 範例](https://aka.ms/TranslatorGitHub?type=&language=c%23)

## <a name="see-also"></a>另請參閱

* [翻譯文字](quickstart-csharp-translate.md)
* [進行文字音譯](quickstart-csharp-transliterate.md)
* [從輸入識別語言](quickstart-csharp-detect.md)
* [取得替代的翻譯](quickstart-csharp-dictionary.md)
* [從輸入判斷句子長度](quickstart-csharp-sentences.md)
