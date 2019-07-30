---
title: 快速入門：使用適用於 .NET 的 Azure SDK 和 C# 呼叫文字分析服務
titleSuffix: Azure Cognitive Services
description: 協助您快速開始使文字分析服務和 C# 的資訊及程式碼範例。
services: cognitive-services
author: raymondl
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 07/18/2019
ms.author: assafi
ms.openlocfilehash: 09713528f51675f6e9d7f3073b6c81b095d23631
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/19/2019
ms.locfileid: "68356972"
---
# <a name="quickstart-use-the-net-sdk-and-c-to-call-the-text-analytics-service"></a>快速入門：使用 .NET SDK 和 C# 來呼叫文字分析服務
<a name="HOLTop"></a>

本快速入門可協助您開始使用適用於 .NET 的 Azure SDK 和 C# 來分析語言。 雖然[文字分析](//go.microsoft.com/fwlink/?LinkID=759711) REST API 與大部分程式設計語言相容，但 SDK 會提供簡單的方法，將服務整合到您的應用程式。

> [!NOTE]
> [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/samples/TextAnalytics) 上有此範例的原始程式碼。

如需技術詳細資料，請針對適用於 .NET 的 SDK 參閱[文字分析參考](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/textanalytics?view=azure-dotnet)。

## <a name="prerequisites"></a>必要條件

* [Visual Studio 2017 或更新版本] 的任何版本
* [適用於 .NET 的文字分析 SDK](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.TextAnalytics)

[!INCLUDE [cognitive-services-text-analytics-signup-requirements](../../../../includes/cognitive-services-text-analytics-signup-requirements.md)]

您也需要註冊時產生的[端點和存取金鑰](../How-tos/text-analytics-how-to-access-key.md)。

## <a name="create-the-visual-studio-solution-and-install-the-sdk"></a>建立 Visual Studio 解決方案並安裝 SDK

1. 建立新的主控台應用程式 (.NET Core) 專案。 [存取 Visual Studio](https://visualstudio.microsoft.com/vs/)。
1. 以滑鼠右鍵按一下方案，然後選取 [管理方案的 NuGet 套件]  。
1. 選取 [瀏覽]  索引標籤。搜尋 **Microsoft.Azure.CognitiveServices.Language.TextAnalytics**。

## <a name="authenticate-your-credentials"></a>驗證您的認證

1. 新增下列 `using` 陳述式至主要類別檔 (預設為 Program.cs)。

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Net.Http;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.CognitiveServices.Language.TextAnalytics;
    using Microsoft.Azure.CognitiveServices.Language.TextAnalytics.Models;
    using Microsoft.Rest;
    ```

2. 建立新的 `ApiKeyServiceClientCredentials` 類別以儲存認證，並對每個要求新增認證。

    ```csharp
    class ApiKeyServiceClientCredentials : ServiceClientCredentials
    {
        private readonly string apiKey;

        public ApiKeyServiceClientCredentials(string apiKey)
        {
            this.apiKey = apiKey;
        }

        public override Task ProcessHttpRequestAsync(HttpRequestMessage request, CancellationToken cancellationToken)
        {
            if (request == null)
            {
                throw new ArgumentNullException("request");
            }
            request.Headers.Add("Ocp-Apim-Subscription-Key", this.apiKey);
            return base.ProcessHttpRequestAsync(request, cancellationToken);
        }
    }
    ```

3. 更新 `Program` 類別。 新增文字分析 API 金鑰的常數成員，並新增另一個服務端點常數成員。 請記得文字分析資源要使用正確的 Azure 位置。

    ```csharp
    //Enter your Text Analytics (TA) API Key (available in Azure Portal -> your TA resource -> Keys)
    private const string ApiKey = "enter-your-textanalytics-api-key-here";
    //You can get the resource location from Azure Portal -> your TA resource -> Overview
    private const string Endpoint = "enter-your-service-endpoint-here"; // For example: "https://<your-location>.api.cognitive.microsoft.com";
    ```
> [!Tip]
> 為了提升生產系統中的祕密安全性，建議使用 [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/quick-create-net)。
>

## <a name="create-a-text-analytics-client"></a>建立文字分析用戶端

在專案的 `Main` 函式中，呼叫您要叫用的範例方法。 傳遞您定義的 `Endpoint` 和 `ApiKey` 參數。

```csharp
    public static void Main(string[] args)
    {
        var credentials = new ApiKeyServiceClientCredentials(ApiKey);
        var client = new TextAnalyticsClient(credentials)
        {
            Endpoint = Endpoint
        };

        // Change the console encoding to display non-ASCII characters.
        Console.OutputEncoding = System.Text.Encoding.UTF8;
        SentimentAnalysisExample(client).Wait();
        // DetectLanguageExample(client).Wait();
        // RecognizeEntitiesExample(client).Wait();
        // KeyPhraseExtractionExample(client).Wait();
        Console.ReadLine();
    }
```

下列各節將說明如何呼叫每個服務功能。

## <a name="perform-sentiment-analysis"></a>執行人氣分析

1. 建立新函式 `SentimentAnalysisExample()`，以取用稍早建立的用戶端。
2. 產生 `MultiLanguageInput` 物件清單，其中包含您要分析的文件。

    ```csharp
    public static async Task SentimentAnalysisExample(TextAnalyticsClient client)
    {
        // The documents to be analyzed. Add the language of the document. The ID can be any value.
        var inputDocuments = new MultiLanguageBatchInput(
            new List<MultiLanguageInput>
            {
                new MultiLanguageInput("en", "1", "I had the best day of my life.")
            });
        //...
    }
    ```

3. 在同一個函式中，呼叫 `client.SentimentAsync()` 並取得結果。 然後逐一查看結果。 輸出每個文件的識別碼和人氣分數。 接近 0 的分數表示負面人氣，而接近 1 的分數則表示正面人氣。

    ```csharp
    var result = await client.SentimentAsync(false, inputDocuments);

    // Printing sentiment results
    foreach (var document in result.Documents)
    {
        Console.WriteLine($"Document ID: {document.Id} , Sentiment Score: {document.Score:0.00}");
    }
    ```

### <a name="output"></a>輸出

```console
Document ID: 1 , Sentiment Score: 0.87
```

## <a name="perform-language-detection"></a>執行語言偵測

1. 建立新函式 `DetectLanguageExample()`，以取用稍早建立的用戶端。
2. 產生包含您文件的 `LanguageInput` 物件清單。

    ```csharp
    public static async Task DetectLanguageExample(TextAnalyticsClient client)
    {

        // The documents to be submitted for language detection. The ID can be any value.
        var inputDocuments = new LanguageBatchInput(
                new List<LanguageInput>
                    {
                        new LanguageInput(id: "1", text: "This is a document written in English.")
                    });
        //...
    }
    ```

3. 在同一個函式中，呼叫 `client.DetectLanguageAsync()` 並取得結果。 然後逐一查看結果。 輸出每個文件的識別碼以及第一個傳回的語言。

    ```csharp
    var langResults = await client.DetectLanguageAsync(false, inputDocuments);

    // Printing detected languages
    foreach (var document in langResults.Documents)
    {
        Console.WriteLine($"Document ID: {document.Id} , Language: {document.DetectedLanguages[0].Name}");
    }
    ```

### <a name="output"></a>輸出

```console
===== LANGUAGE EXTRACTION ======
Document ID: 1 , Language: English
```

## <a name="perform-entity-recognition"></a>執行實體辨識

1. 建立新函式 `RecognizeEntitiesExample()`，以取用稍早建立的用戶端。
2. 產生包含您文件的 `MultiLanguageBatchInput` 物件清單。

    ```csharp
    public static async Task RecognizeEntitiesExample(TextAnalyticsClient client)
    {
        // The documents to be submitted for entity recognition. The ID can be any value.
        var inputDocuments = new MultiLanguageBatchInput(
            new List<MultiLanguageInput>
            {
                new MultiLanguageInput("en", "1", "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800.")
            });
        //...
    }
    ```

3. 在同一個函式中，呼叫 `client.EntitiesAsync()` 並取得結果。 然後逐一查看結果。 輸出每個文件的識別碼。 對於每個偵測到的實體，輸出其維基百科名稱、類型和子類型 (如果存在) 以及原始文字中的位置。

    ```csharp
    var entitiesResult = await client.EntitiesAsync(false, inputDocuments);

    // Printing recognized entities
    foreach (var document in entitiesResult.Documents)
    {
        Console.WriteLine($"Document ID: {document.Id} ");

        Console.WriteLine("\t Entities:");
        foreach (var entity in document.Entities)
        {
            Console.WriteLine($"\t\tName: {entity.Name},\tType: {entity.Type ?? "N/A"},\tSub-Type: {entity.SubType ?? "N/A"}");
            foreach (var match in entity.Matches)
            {
                Console.WriteLine($"\t\t\tOffset: {match.Offset},\tLength: {match.Length},\tScore: {match.EntityTypeScore:F3}");
            }
        }
    }
    ```

### <a name="output"></a>輸出

```console
Document ID: 1
         Entities:
                Name: Microsoft,        Type: Organization,     Sub-Type: N/A
                        Offset: 0,      Length: 9,      Score: 1.000
                Name: Bill Gates,       Type: Person,   Sub-Type: N/A
                        Offset: 25,     Length: 10,     Score: 1.000
                Name: Paul Allen,       Type: Person,   Sub-Type: N/A
                        Offset: 40,     Length: 10,     Score: 0.999
                Name: April 4,  Type: Other,    Sub-Type: N/A
                        Offset: 54,     Length: 7,      Score: 0.800
                Name: April 4, 1975,    Type: DateTime, Sub-Type: Date
                        Offset: 54,     Length: 13,     Score: 0.800
                Name: BASIC,    Type: Other,    Sub-Type: N/A
                        Offset: 89,     Length: 5,      Score: 0.800
                Name: Altair 8800,      Type: Other,    Sub-Type: N/A
                        Offset: 116,    Length: 11,     Score: 0.800
```

## <a name="perform-key-phrase-extraction"></a>執行關鍵片語擷取

1. 建立新函式 `KeyPhraseExtractionExample()`，以取用稍早建立的用戶端。
2. 產生包含您文件的 `MultiLanguageBatchInput` 物件清單。

    ```csharp
    public static async Task KeyPhraseExtractionExample(TextAnalyticsClient client)
    {
        var inputDocuments = new MultiLanguageBatchInput(
                    new List<MultiLanguageInput>
                    {
                        new MultiLanguageInput("en", "1", "My cat might need to see a veterinarian.")
                    });
        //...
    }
    ```

3. 在同一個函式中，呼叫 `client.KeyPhrasesAsync()` 並取得結果。 然後逐一查看結果。 輸出每個文件的識別碼，以及任何偵測到的關鍵片語。

    ```csharp
    var kpResults = await client.KeyPhrasesAsync(false, inputDocuments);

    // Printing keyphrases
    foreach (var document in kpResults.Documents)
    {
        Console.WriteLine($"Document ID: {document.Id} ");

        Console.WriteLine("\t Key phrases:");

        foreach (string keyphrase in document.KeyPhrases)
        {
            Console.WriteLine($"\t\t{keyphrase}");
        }
    }
    ```

### <a name="output"></a>輸出

```console
Document ID: 1
         Key phrases:
                cat
                veterinarian
```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [文字分析與 Power BI](../tutorials/tutorial-power-bi-key-phrases.md)


* [文字分析概觀](../overview.md)
* [常見問題集 (FAQ)](../text-analytics-resource-faq.md)
