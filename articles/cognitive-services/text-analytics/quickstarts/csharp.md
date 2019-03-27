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
ms.date: 01/02/2019
ms.author: assafi
ms.openlocfilehash: bc4553df239dbb8b62a31414539b10998cd74f02
ms.sourcegitcommit: f331186a967d21c302a128299f60402e89035a8d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/19/2019
ms.locfileid: "58189643"
---
# <a name="quickstart-using-c-to-call-the-text-analytics-cognitive-service"></a>快速入門：使用 C# 來呼叫文字分析認知服務
<a name="HOLTop"></a>

本文示範如何使用 [文字分析 API](//go.microsoft.com/fwlink/?LinkID=759711)  與 C# 來偵測語言、分析情感及擷取關鍵片語。 程式碼以能在 .NET Core 應用程式上運作，且參考最少量外部程式庫的方式撰寫，因此您也可以在 Linux 或 MacOS 上執行。

如需 API 的技術文件，請參閱 [API 定義](//go.microsoft.com/fwlink/?LinkID=759346)。

## <a name="prerequisites"></a>必要條件

[!INCLUDE [cognitive-services-text-analytics-signup-requirements](../../../../includes/cognitive-services-text-analytics-signup-requirements.md)]

您也必須具備註冊時產生的[端點和存取金鑰](../How-tos/text-analytics-how-to-access-key.md)。

## <a name="install-the-nuget-sdk-package"></a>安裝 NuGet SDK 套件
1. 在 Visual Studio 中建立新的主控台解決方案。
1. 以滑鼠右鍵按一下方案，然後按一下 [管理方案的 NuGet 套件]。
1. 標記 [包括發行前版本] 核取方塊。
1. 選取 [瀏覽] 索引標籤，搜尋 **Microsoft.Azure.CognitiveServices.Language.TextAnalytics**
1. 選取 NuGet 套件並加以安裝。 除非軟體中的錯誤 (bug) 已經解決，否則您目前可能需要 v2.8.0 (3-18-2019)，而非 v3.0.0。

> [!Tip]
>  雖然您可以直接從 C# 呼叫 [HTTP 端點](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6)，但是 Microsoft.Azure.CognitiveServices.Language SDK 可以讓您更輕鬆地呼叫服務，而不必擔心序列化和還原序列化 JSON。
>
> 以下是幾個實用的連結：
> - [SDK NuGet 頁面](<https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.TextAnalytics>)
> - [SDK 程式碼](https://github.com/Azure/azure-sdk-for-net/tree/psSdkJson6/src/SDKs/CognitiveServices/dataPlane/Language/TextAnalytics)

## <a name="call-the-text-analytics-api-using-the-sdk"></a>使用 SDK 呼叫文字分析 API

1. 將 Program.cs 取代為下面提供的程式碼。 此程式以 3 個區段 (語言擷取、關鍵片語擷取和情感分析) 示範文字分析 API 的功能。
1. 以對您訂用帳戶有效的存取金鑰取代 `Ocp-Apim-Subscription-Key` 標頭值。
1. 取代 `Endpoint` 中的區域。 您可以在 [Azure 入口網站](<https://ms.portal.azure.com>)，於文字分析資源的概觀區段中，找到端點。 請只包含您端點的這部分："https://[region].api.cognitive.microsoft.com"。
1. 執行程式。

```csharp
using System;
using Microsoft.Azure.CognitiveServices.Language.TextAnalytics;
using Microsoft.Azure.CognitiveServices.Language.TextAnalytics.Models;
using System.Collections.Generic;
using Microsoft.Rest;
using System.Net.Http;
using System.Threading;
using System.Threading.Tasks;

namespace ConsoleApp1
{
    class Program
    {
        /// <summary>
        /// Container for subscription credentials. Make sure to enter your valid key.
        private const string SubscriptionKey = ""; //Insert your Text Anaytics subscription key

        /// </summary>
        class ApiKeyServiceClientCredentials : ServiceClientCredentials
        {
            public override Task ProcessHttpRequestAsync(HttpRequestMessage request, CancellationToken cancellationToken)
            {
                request.Headers.Add("Ocp-Apim-Subscription-Key", SubscriptionKey);
                return base.ProcessHttpRequestAsync(request, cancellationToken);
            }
        }

        static void Main(string[] args)
        {

            // Create a client.
            ITextAnalyticsClient client = new TextAnalyticsClient(new ApiKeyServiceClientCredentials())
            {
                Endpoint = "https://westus.api.cognitive.microsoft.com"
            }; //Replace 'westus' with the correct region for your Text Analytics subscription

            Console.OutputEncoding = System.Text.Encoding.UTF8;

            // Extracting language
            Console.WriteLine("===== LANGUAGE EXTRACTION ======");

            var result = client.DetectLanguageAsync(new BatchInput(
                    new List<Input>()
                        {
                          new Input("1", "This is a document written in English."),
                          new Input("2", "Este es un document escrito en Español."),
                          new Input("3", "这是一个用中文写的文件")
                    })).Result;

            // Printing language results.
            foreach (var document in result.Documents)
            {
                Console.WriteLine($"Document ID: {document.Id} , Language: {document.DetectedLanguages[0].Name}");
            }

            // Getting key-phrases
            Console.WriteLine("\n\n===== KEY-PHRASE EXTRACTION ======");

            KeyPhraseBatchResult result2 = client.KeyPhrasesAsync(new MultiLanguageBatchInput(
                        new List<MultiLanguageInput>()
                        {
                          new MultiLanguageInput("ja", "1", "猫は幸せ"),
                          new MultiLanguageInput("de", "2", "Fahrt nach Stuttgart und dann zum Hotel zu Fu."),
                          new MultiLanguageInput("en", "3", "My cat is stiff as a rock."),
                          new MultiLanguageInput("es", "4", "A mi me encanta el fútbol!")
                        })).Result;

            // Printing keyphrases
            foreach (var document in result2.Documents)
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

            SentimentBatchResult result3 = client.SentimentAsync(
                    new MultiLanguageBatchInput(
                        new List<MultiLanguageInput>()
                        {
                          new MultiLanguageInput("en", "0", "I had the best day of my life."),
                          new MultiLanguageInput("en", "1", "This was a waste of my time. The speaker put me to sleep."),
                          new MultiLanguageInput("es", "2", "No tengo dinero ni nada que dar..."),
                          new MultiLanguageInput("it", "3", "L'hotel veneziano era meraviglioso. È un bellissimo pezzo di architettura."),
                        })).Result;


            // Printing sentiment results
            foreach (var document in result3.Documents)
            {
                Console.WriteLine($"Document ID: {document.Id} , Sentiment Score: {document.Score:0.00}");
            }


            // Identify entities
            Console.WriteLine("\n\n===== ENTITIES ======");

            EntitiesBatchResultV2dot1 result4 = client.EntitiesAsync(
                    new MultiLanguageBatchInput(
                        new List<MultiLanguageInput>()
                        {
                          new MultiLanguageInput("en", "0", "The Great Depression began in 1929. By 1933, the GDP in America fell by 25%.")
                        })).Result;

            // Printing entities results
            foreach (var document in result4.Documents)
            {
                Console.WriteLine($"Document ID: {document.Id} ");

                Console.WriteLine("\t Entities:");

                foreach (EntityRecordV2dot1 entity in document.Entities)
                {
                    Console.WriteLine($"\t\t{entity.Name}\t\t{entity.WikipediaUrl}\t\t{entity.Type}\t\t{entity.SubType}");
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
Document ID: 3
         Key phrases:
                cat
                rock
Document ID: 4
         Key phrases:
                fútbol

===== SENTIMENT ANALYSIS ======
Document ID: 0 , Sentiment Score: 0.87
Document ID: 1 , Sentiment Score: 0.11
Document ID: 2 , Sentiment Score: 0.44
Document ID: 3 , Sentiment Score: 1.00
```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [文字分析與 Power BI](../tutorials/tutorial-power-bi-key-phrases.md)

## <a name="see-also"></a>另請參閱

 [文字分析概觀](../overview.md)[常見問題集 (FAQ)](../text-analytics-resource-faq.md)

