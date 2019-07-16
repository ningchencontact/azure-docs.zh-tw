---
title: 快速入門：將文字轉換為語音，.NET Core - 語音服務
titleSuffix: Azure Cognitive Services
description: 在本快速入門中，您將了解如何使用文字轉換語音 REST API，將文字轉換為語音。 本指南中的範例文字包含已建構為語音合成標記語言 (SSML)。 這可讓您選擇語音回應的語音和語言。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 1dd54adeffab80d4334d8f08733cfd6603f673d3
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/05/2019
ms.locfileid: "67606324"
---
# <a name="quickstart-convert-text-to-speech-using-net-core"></a>快速入門：使用 .NET Core 將文字轉換為語音

在本快速入門中，您將了解如何使用 .NET Core 和文字轉換語音 REST API，將文字轉換為語音。 本指南中的範例文字已結構化為[語音合成標記語言 (SSML)](speech-synthesis-markup.md)，這可讓您選擇回應的語音和語言。

本快速入門需要 [Azure 認知服務帳戶](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)和語音服務資源。 如果您還沒有帳戶，可以使用[免費試用](https://azure.microsoft.com/try/cognitive-services/)來取得訂用帳戶金鑰。

## <a name="prerequisites"></a>必要條件

本快速入門需要：

* [.NET Core SDK](https://dotnet.microsoft.com/download)
* [Visual Studio](https://visualstudio.microsoft.com/downloads/)、[Visual Studio Code](https://code.visualstudio.com/download)，或您最愛的文字編輯器
* 語音服務的 Azure 訂用帳戶金鑰

## <a name="create-a-net-core-project"></a>建立 .NET Core 專案

開啟新的命令提示字元 (或終端機工作階段) 並執行下列命令：

```console
dotnet new console -o tts-sample
cd tts-sample
```

第一個命令會執行兩項工作。 它會建立新的 .NET 主控台應用程式，並建立名為 `tts-sample` 的目錄。 第二個命令會變更為您專案的目錄。

## <a name="select-the-c-language-version"></a>選取 C# 語言版本

此快速入門需要 C# 7.1 或更新版本。 有幾個方式可以變更您專案的 C# 版本。 在本指南中，我們將示範如何調整 `tts-sample.csproj` 檔案。 如需所有可用的選項，例如在 Visual Studio 中變更語言，請參閱[選取 C# 語言版本](https://docs.microsoft.com/dotnet/csharp/language-reference/configure-language-version)。

開啟您的專案，然後開啟 `tts-sample.csproj`。 確定 `LangVersion` 已設為 7.1 或更新版本。 如果沒有語言版本的屬性群組，請加入這些程式行：

```xml
<PropertyGroup>
   <LangVersion>7.1</LangVersion>
</PropertyGroup>
```

務必儲存變更。

## <a name="add-required-namespaces-to-your-project"></a>將所需的命名空間新增至您的專案

您先前建立專案時執行的 `dotnet new console` 命令，包括 `Program.cs`。 這個檔案是您將放置應用程式程式碼的地方。 開啟 `Program.cs`，並取代現有的 using 陳述式。 這些陳述式可確保您有權存取建置和執行範例應用程式所需的所有類型。

```csharp
using System;
using System.Net.Http;
using System.Text;
using System.IO;
using System.Threading.Tasks;
```

## <a name="create-a-class-for-token-exchange"></a>建立用於權杖交換的類別

文字轉換語音 REST API 需要存取權杖進行驗證。 若要取得存取權杖，則必須進行交換。 此範例會使用 `issueToken` 端點，以語音服務訂用帳戶金鑰交換存取權杖。

這個範例假設您的語音服務訂用帳戶是在美國西部區域。 如果您使用不同的區域，請更新 `FetchTokenUri` 的值。 如需完整清單，請參閱[區域](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#rest-apis)。

```csharp
public class Authentication
{
    private string subscriptionKey;
    private string tokenFetchUri;

    public Authentication(string tokenFetchUri, string subscriptionKey)
    {
        if (string.IsNullOrWhiteSpace(tokenFetchUri))
        {
            throw new ArgumentNullException(nameof(tokenFetchUri));
        }
        if (string.IsNullOrWhiteSpace(subscriptionKey))
        {
            throw new ArgumentNullException(nameof(subscriptionKey));
        }
        this.tokenFetchUri = tokenFetchUri;
        this.subscriptionKey = subscriptionKey;
    }

    public async Task<string> FetchTokenAsync()
    {
        using (var client = new HttpClient())
        {
            client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", this.subscriptionKey);
            UriBuilder uriBuilder = new UriBuilder(this.tokenFetchUri);

            var result = await client.PostAsync(uriBuilder.Uri.AbsoluteUri, null).ConfigureAwait(false);
            return await result.Content.ReadAsStringAsync().ConfigureAwait(false);
        }
    }
}
```

> [!NOTE]
> 如需驗證的詳細資訊，請參閱[使用存取權杖進行驗證](https://docs.microsoft.com/azure/cognitive-services/authentication#authenticate-with-an-authentication-token)。

## <a name="get-an-access-token-and-set-the-host-url"></a>取得存取權杖並設定主機 URL

找到 `static void Main(string[] args)` 並使用 `static async Task Main(string[] args)` 取代它。

接下來，將此程式碼複製到 Main 方法。 這會執行一些動作，但最重要的是它會將文字視為輸入，並呼叫 `Authentication` 函式，將您的訂用帳戶金鑰交換以取得存取權杖。 如果發生錯誤，錯誤會印出到主控台中。

請務必加入您的訂用帳戶金鑰，然後才執行應用程式。

```csharp
// Prompts the user to input text for TTS conversion
Console.Write("What would you like to convert to speech? ");
string text = Console.ReadLine();

// Gets an access token
string accessToken;
Console.WriteLine("Attempting token exchange. Please wait...\n");

// Add your subscription key here
// If your resource isn't in WEST US, change the endpoint
Authentication auth = new Authentication("https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken", "YOUR_SUBSCRIPTION_KEY");
try
{
    accessToken = await auth.FetchTokenAsync().ConfigureAwait(false);
    Console.WriteLine("Successfully obtained an access token. \n");
}
catch (Exception ex)
{
    Console.WriteLine("Failed to obtain an access token.");
    Console.WriteLine(ex.ToString());
    Console.WriteLine(ex.Message);
    return;
}
```

接著，設定文字轉換語音的主機和路由：

```csharp
string host = "https://westus.tts.speech.microsoft.com/cognitiveservices/v1";
```

## <a name="build-the-ssml-request"></a>建置 SSML 要求

系統會將文字作為 `POST` 要求的本文傳送。 透過 SSML，您可以指定語音與語言。 在本快速入門中，我們將搭配使用語言設定為 `en-US` 以及語音設定為 `ZiraRUS` 的 SSML。 讓我們來建構適合您要求的 SSML：

```csharp
string body = @"<speak version='1.0' xmlns='https://www.w3.org/2001/10/synthesis' xml:lang='en-US'>
              <voice name='Microsoft Server Speech Text to Speech Voice (en-US, ZiraRUS)'>" +
              text + "</voice></speak>";
```

> [!NOTE]
> 此範例使用 `ZiraRUS` 語音音調。 如需 Microsoft 提供的完整語音/語言清單，請參閱[語言支援](language-support.md)。 如果您想要為品牌建立獨特可辨識的語音，請參閱[建立自訂語音音調](how-to-customize-voice-font.md)。

## <a name="instantiate-the-client-make-a-request-and-save-synthesized-audio-to-a-file"></a>具現化用戶端、發起要求，然後將合成的音訊儲存為檔案

此程式碼範例中會發生許多動作。 讓我們快速檢閱將發生動作：

* 具現化用戶端和要求。
* HTTP 方法設為 `POST`。
* 必要的標頭加入至要求。
* 傳送要求並且檢查狀態碼。
* 回應是以非同步方式讀取，並且寫入至名為 sample.wav 的檔案。

將此程式碼複製到您的專案中。 務必將 `User-Agent` 標頭的值取代為您 Azure 入口網站中的資源名稱。

```csharp
using (var client = new HttpClient())
{
    using (var request = new HttpRequestMessage())
    {
        // Set the HTTP method
        request.Method = HttpMethod.Post;
        // Construct the URI
        request.RequestUri = new Uri(host);
        // Set the content type header
        request.Content = new StringContent(body, Encoding.UTF8, "application/ssml+xml");
        // Set additional header, such as Authorization and User-Agent
        request.Headers.Add("Authorization", "Bearer " + accessToken);
        request.Headers.Add("Connection", "Keep-Alive");
        // Update your resource name
        request.Headers.Add("User-Agent", "YOUR_RESOURCE_NAME");
        request.Headers.Add("X-Microsoft-OutputFormat", "riff-24khz-16bit-mono-pcm");
        // Create a request
        Console.WriteLine("Calling the TTS service. Please wait... \n");
        using (var response = await client.SendAsync(request).ConfigureAwait(false))
        {
            response.EnsureSuccessStatusCode();
            // Asynchronously read the response
            using (var dataStream = await response.Content.ReadAsStreamAsync().ConfigureAwait(false))
            {
                Console.WriteLine("Your speech file is being written to file...");
                using (var fileStream = new FileStream(@"sample.wav", FileMode.Create, FileAccess.Write, FileShare.Write))
                {
                    await dataStream.CopyToAsync(fileStream).ConfigureAwait(false);
                    fileStream.Close();
                }
                Console.WriteLine("\nYour file is ready. Press any key to exit.");
                Console.ReadLine();
            }
        }
    }
}
```

## <a name="run-the-sample-app"></a>執行範例應用程式

就這樣，您已準備好可以執行文字轉換語音應用程式。 從命令列 (或終端機工作階段)，請瀏覽至您的專案目錄，然後執行：

```console
dotnet run
```

如果成功，語音檔案會儲存在專案資料夾中。 使用您最愛的媒體播放器來播放。

## <a name="clean-up-resources"></a>清除資源

如果您已將訂用帳戶金鑰硬式編碼到您的程式中，請務必在完成本快速入門後移除訂用帳戶金鑰。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [在 GitHub 上探索 .NET 範例](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/Samples-Http/NETCore) \(英文\)

## <a name="see-also"></a>另請參閱

* [文字轉換語音 API 參考](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis)
* [建立自訂語音調](how-to-customize-voice-font.md)
* [錄製語音樣本來建立自訂語音](record-custom-voice-samples.md)
