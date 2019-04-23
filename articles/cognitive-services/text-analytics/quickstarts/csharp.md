---
title: 快速入門：使用 C# 來呼叫文字分析 API
titleSuffix: Azure Cognitive Services
description: 取得資訊和程式碼範例，以協助您快速開始使文字分析 API。
services: cognitive-services
author: ashmaka
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 04/12/2019
ms.author: assafi
ms.openlocfilehash: 7051f1c1ce43be7dce5d88a06fccee9d876a70f4
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2019
ms.locfileid: "60010172"
---
# <a name="quickstart-using-c-to-call-the-text-analytics-cognitive-service"></a>快速入門：使用 C# 來呼叫文字分析認知服務
<a name="HOLTop"></a>

本文示範如何使用 [文字分析 API](//go.microsoft.com/fwlink/?LinkID=759711)  與 C# 來偵測語言、分析情感及擷取關鍵片語。 程式碼以能在 .NET Core 應用程式上運作，且參考最少量外部程式庫的方式撰寫，因此您也可以在 Linux 或 MacOS 上執行。 此快速入門的原始程式碼可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/samples/TextAnalytics) 上找到。

如需 API 的技術文件，請參閱 [API 定義](//go.microsoft.com/fwlink/?LinkID=759346)。

## <a name="prerequisites"></a>必要條件

[!INCLUDE [cognitive-services-text-analytics-signup-requirements](../../../../includes/cognitive-services-text-analytics-signup-requirements.md)]

您也必須具備註冊時產生的[端點和存取金鑰](../How-tos/text-analytics-how-to-access-key.md)。

## <a name="install-the-nuget-sdk-package"></a>安裝 NuGet SDK 套件
1. 在 Visual Studio 中使用 `.netcoreapp2.0` 與更新版本建立一個新主控台解決方案。
1. 以滑鼠右鍵按一下方案，然後按一下 [管理方案的 NuGet 套件]。
1. 選取 [瀏覽] 索引標籤，搜尋 **Microsoft.Azure.CognitiveServices.Language.TextAnalytics**

> [!Tip]
>  雖然您可以直接從 C# 呼叫 [HTTP 端點](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c6)，但是 Microsoft.Azure.CognitiveServices.Language SDK 可以讓您更輕鬆地呼叫服務，而不必擔心序列化和還原序列化 JSON。
>
> 以下是幾個實用的連結：
> - [SDK NuGet 頁面](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.TextAnalytics)
> - [SDK 程式碼](https://github.com/Azure/azure-sdk-for-net/tree/psSdkJson6/src/SDKs/CognitiveServices/dataPlane/Language/TextAnalytics)

## <a name="call-the-text-analytics-api-using-the-sdk"></a>使用 SDK 呼叫文字分析 API

1. 將 Program.cs 取代為下面提供的程式碼。 此程式以 3 個區段 (語言擷取、關鍵片語擷取和情感分析) 示範文字分析 API 的功能。
1. 以對您訂用帳戶有效的存取金鑰取代 `Ocp-Apim-Subscription-Key` 標頭值。
1. 取代 `Endpoint` 中的區域。 您可以在 [Azure 入口網站](<https://ms.portal.azure.com>)，於文字分析資源的概觀區段中，找到端點。 請只包含您端點的這部分："https://[region].api.cognitive.microsoft.com"。
1. 執行程式。

```csharp
using System;
using System.Collections.Generic;
using System.Net.Http;
using System.Threading;
using System.Threading.Tasks;

using Microsoft.Azure.CognitiveServices.Language.TextAnalytics;
using Microsoft.Azure.CognitiveServices.Language.TextAnalytics.Models;
using Microsoft.Rest;

namespace ConsoleApp1
{
    class Program
    {
        private const string SubscriptionKey = ""; //Insert your Text Anaytics subscription key

        private class ApiKeyServiceClientCredentials : ServiceClientCredentials
        {
            public override Task ProcessHttpRequestAsync(HttpRequestMessage request, CancellationToken cancellationToken)
            {
                request.Headers.Add("Ocp-Apim-Subscription-Key", SubscriptionKey);
                return base.ProcessHttpRequestAsync(request, cancellationToken);
            }
        }

        static async Task Main(string[] args)
        {

            // Create a client.
            ITextAnalyticsClient client = new TextAnalyticsClient(new ApiKeyServiceClientCredentials())
            {
                Endpoint = "https://westus.api.cognitive.microsoft.com"
            }; //Replace 'westus' with the correct region for your Text Analytics subscription

            Console.OutputEncoding = System.Text.Encoding.UTF8;

            // Extracting language
            Console.WriteLine("===== LANGUAGE EXTRACTION ======");

            var langResults = await client.DetectLanguageAsync(
                false,
                new LanguageBatchInput(
                    new List<LanguageInput>
                        {
                          new LanguageInput(id: "1", text: "This is a document written in English."),
                          new LanguageInput(id: "2", text: "Este es un document escrito en Español."),
                          new LanguageInput(id: "3", text: "这是一个用中文写的文件")
                        }));

            // Printing language results.
            foreach (var document in langResults.Documents)
            {
                Console.WriteLine($"Document ID: {document.Id} , Language: {document.DetectedLanguages[0].Name}");
            }

            // Getting key-phrases
            Console.WriteLine("\n\n===== KEY-PHRASE EXTRACTION ======");

            var kpResults = await client.KeyPhrasesAsync(
                false,
                new MultiLanguageBatchInput(
                    new List<MultiLanguageInput>
                    {
                        new MultiLanguageInput("ja", "1", "猫は幸せ"),
                        new MultiLanguageInput("de", "2", "Fahrt nach Stuttgart und dann zum Hotel zu Fu."),
                        new MultiLanguageInput("en", "3", "My cat is stiff as a rock."),
                        new MultiLanguageInput("es", "4", "A mi me encanta el fútbol!")
                    }));

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

            // Extracting sentiment
            Console.WriteLine("\n\n===== SENTIMENT ANALYSIS ======");

            var sentimentResults = await client.SentimentAsync(
                false,
                new MultiLanguageBatchInput(
                    new List<MultiLanguageInput>
                    {
                        new MultiLanguageInput("en", "1", "I had the best day of my life."),
                        new MultiLanguageInput("en", "2", "This was a waste of my time. The speaker put me to sleep."),
                        new MultiLanguageInput("es", "3", "No tengo dinero ni nada que dar..."),
                        new MultiLanguageInput("it", "4", "L'hotel veneziano era meraviglioso. È un bellissimo pezzo di architettura."),
                    }));


            // Printing sentiment results
            foreach (var document in sentimentResults.Documents)
            {
                Console.WriteLine($"Document ID: {document.Id} , Sentiment Score: {document.Score:0.00}");
            }


            // Identify entities
            Console.WriteLine("\n\n===== ENTITIES ======");

            var entitiesResult = await client.EntitiesAsync(
                false,
                new MultiLanguageBatchInput(
                    new List<MultiLanguageInput>()
                    {
                        new MultiLanguageInput("en", "1", "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800."),
                        new MultiLanguageInput("es", "2", "La sede principal de Microsoft se encuentra en la ciudad de Redmond, a 21 kilómetros de Seattle.")
                    }));

            // Printing entities results
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

            Console.ReadLine();
        }
    }
}
```

## <a name="application-output"></a>應用程式輸出

應用程式會顯示以下資訊：

```console
===== LANGUAGE EXTRACTION ======
Document ID: 1 , Language: English
Document ID: 2 , Language: Spanish
Document ID: 3 , Language: Chinese_Simplified


===== KEY-PHRASE EXTRACTION ======
Document ID: 1
         Key phrases:
                幸せ
Document ID: 2
         Key phrases:
                Stuttgart
                Hotel
                Fahrt
                Fu
Document ID: 3
         Key phrases:
                cat
                rock
Document ID: 4
         Key phrases:
                fútbol


===== SENTIMENT ANALYSIS ======
Document ID: 1 , Sentiment Score: 0.87
Document ID: 2 , Sentiment Score: 0.11
Document ID: 3 , Sentiment Score: 0.44
Document ID: 4 , Sentiment Score: 1.00


===== ENTITIES ======
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
Document ID: 2
         Entities:
                Name: Microsoft,        Type: Organization,     Sub-Type: N/A
                        Offset: 21,     Length: 9,      Score: 1.000
                Name: Redmond (Washington),     Type: Location, Sub-Type: N/A
                        Offset: 60,     Length: 7,      Score: 0.991
                Name: 21 kilómetros,    Type: Quantity, Sub-Type: Dimension
                        Offset: 71,     Length: 13,     Score: 0.800
                Name: Seattle,  Type: Location, Sub-Type: N/A
                        Offset: 88,     Length: 7,      Score: 1.000
```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [文字分析與 Power BI](../tutorials/tutorial-power-bi-key-phrases.md)

## <a name="see-also"></a>另請參閱

 [文字分析概觀](../overview.md)[常見問題集 (FAQ)](../text-analytics-resource-faq.md)

