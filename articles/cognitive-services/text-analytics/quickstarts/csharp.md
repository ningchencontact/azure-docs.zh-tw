---
title: 認知服務、文字分析 API 的 C# 快速入門 | Microsoft Docs
description: 取得資訊和程式碼範例，協助您在 Azure上快速開始使用 Microsoft 認知服務中的文字分析 API。
services: cognitive-services
documentationcenter: ''
author: luiscabrer
ms.service: cognitive-services
ms.component: text-analytics
ms.topic: article
ms.date: 08/30/2018
ms.author: ashmaka
ms.openlocfilehash: 42a682898303b742a17b0a6d4d98c2b9fedf9003
ms.sourcegitcommit: 3d0295a939c07bf9f0b38ebd37ac8461af8d461f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/06/2018
ms.locfileid: "43841689"
---
# <a name="quickstart-for-the-text-analytics-api-with-c"></a>文字分析 API 與 C# 的快速入門 
<a name="HOLTop"></a>

本文示範如何使用[文字分析 API](//go.microsoft.com/fwlink/?LinkID=759711) 與 C# 來偵測語言、分析情感及擷取關鍵片語。 程式碼以能在 .NET Core 應用程式上運作，且參考最少量外部程式庫的方式撰寫，因此您也可以在 Linux 或 MacOS 上執行。

如需 API 的技術文件，請參閱 [API 定義](//go.microsoft.com/fwlink/?LinkID=759346)。

## <a name="prerequisites"></a>必要條件

您必須有具備文字分析 API 的[認知服務 API 帳戶](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)。 您可以使用 *5,000 次交易/月的免費層*來完成此快速入門。

您也必須具備在註冊時產生的[端點和存取金鑰](../How-tos/text-analytics-how-to-access-key.md)。 


## <a name="install-the-nuget-sdk-package"></a>安裝 NuGet SDK 套件
1. 在 Visual Studio 中建立新的主控台解決方案。
1. 以滑鼠右鍵按一下方案，然後選取 [管理方案的 NuGet 套件]。
1. 選取 [包括發行前版本] 核取方塊。
1. 選取 [瀏覽] 索引標籤，搜尋 **Microsoft.Azure.CognitiveServices.Language**。
1. 選取 **Microsoft.Azure.CognitiveServices.Language.TextAnalytics** NuGet 套件並加以安裝。

> [!Tip]
> 雖然您可以直接從 C# 呼叫 [HTTP 端點](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6)，但是 Microsoft.Azure.CognitiveServices.Language SDK 可以讓您更輕鬆地呼叫服務，而不必擔心序列化和還原序列化 JSON。
>
> 以下是實用的連結：
> - [SDK NuGet 頁面](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.TextAnalytics)
> - [SDK 程式碼](https://github.com/Azure/azure-sdk-for-net/tree/psSdkJson6/src/SDKs/CognitiveServices/dataPlane/Language/TextAnalytics)


## <a name="call-the-text-analytics-api-by-using-the-sdk"></a>使用 SDK 呼叫文字分析 API
1. 以下列程式碼取代 Program.cs。 此程式以 3 個區段 (語言擷取、關鍵片語擷取和情感分析) 示範文字分析 API 的功能。
1. 以對您訂用帳戶有效的存取金鑰取代 `Ocp-Apim-Subscription-Key` 標頭值。
1. 將 `Endpoint` 中的位置取代為您註冊的端點。 您可以在 Azure 入口網站資源中尋找端點。 端點的開頭通常為 "https://[region].api.cognitive.microsoft.com"。 僅包含通訊協定和主機名稱。
1. 執行程式。

```csharp
using System;
using Microsoft.Azure.CognitiveServices.Language.TextAnalytics;
using Microsoft.Azure.CognitiveServices.Language.TextAnalytics.Models;
using System.Collections.Generic;
using Microsoft.Rest;
using System.Linq;
using System.Net.Http;
using System.Threading;
using System.Threading.Tasks;

namespace ConsoleApp1
{
    class Program
    {
        /// <summary>
        /// Container for subscription credentials. Make sure to enter your valid key.
        /// </summary>
        class ApiKeyServiceClientCredentials : ServiceClientCredentials
        {
            public override Task ProcessHttpRequestAsync(HttpRequestMessage request, CancellationToken cancellationToken)
            {
                request.Headers.Add("Ocp-Apim-Subscription-Key", "ENTER KEY HERE");
                return base.ProcessHttpRequestAsync(request, cancellationToken);
            }
        }

        static void Main(string[] args)
        {

            // Create a client.
            ITextAnalyticsClient client = new TextAnalyticsClient(new ApiKeyServiceClientCredentials())
            {
                Endpoint = "https://westus.api.cognitive.microsoft.com"
            };

            Console.OutputEncoding = System.Text.Encoding.UTF8;
```

## <a name="detect-language"></a>偵測語言種類

語言偵測 API 會使用[偵測語言方法](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7) \(英文\) 來偵測文字文件的語言。

```csharp
            // Extracting language.
            Console.WriteLine("===== LANGUAGE EXTRACTION ======");

            var result =  client.DetectLanguageAsync(new BatchInput(
                    new List<Input>()
                        {
                          new Input("1", "This is a document written in English."),
                          new Input("2", "Este es un document escrito en Español."),
                          new Input("3", "这是一个用中文写的文件")
                    })).Result;

            // Printing language results.
            foreach (var document in result.Documents)
            {
                Console.WriteLine("Document ID: {0} , Language: {1}", document.Id, document.DetectedLanguages[0].Name);
            }
```

## <a name="extract-key-phrases"></a>擷取關鍵片語

關鍵片語擷取 API 會使用[關鍵片語方法](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6)從文字文件擷取關鍵片語。

```csharp
            // Getting key phrases.
            Console.WriteLine("\n\n===== KEY-PHRASE EXTRACTION ======");

            KeyPhraseBatchResult result2 = client.KeyPhrasesAsync(new MultiLanguageBatchInput(
                        new List<MultiLanguageInput>()
                        {
                          new MultiLanguageInput("ja", "1", "猫は幸せ"),
                          new MultiLanguageInput("de", "2", "Fahrt nach Stuttgart und dann zum Hotel zu Fu."),
                          new MultiLanguageInput("en", "3", "My cat is stiff as a rock."),
                          new MultiLanguageInput("es", "4", "A mi me encanta el fútbol!")
                        })).Result;

            // Printing key phrases.
            foreach (var document in result2.Documents)
            {
                Console.WriteLine("Document ID: {0} ", document.Id);

                Console.WriteLine("\t Key phrases:");

                foreach (string keyphrase in document.KeyPhrases)
                {
                    Console.WriteLine("\t\t" + keyphrase);
                }
            }
```

## <a name="analyze-sentiment"></a>分析人氣

情感分析 API 會使用 [Sentiment 方法](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c9)，偵測一組文字記錄中的情緒態度。

```csharp
            // Analyzing sentiment.
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


            // Printing sentiment results.
            foreach (var document in result3.Documents)
            {
                Console.WriteLine("Document ID: {0} , Sentiment Score: {1:0.00}", document.Id, document.Score);
            }
```

## <a name="identify-linked-entities"></a>識別已連結實體

實體連結 API 會使用[實體連結方法](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/5ac4251d5b4ccd1554da7634) \(英文\) 來識別文字文件中的已知實體。

```csharp
            // Linking entities
            Console.WriteLine("\n\n===== ENTITY LINKING ======");

            EntitiesBatchResult result4 = client.EntitiesAsync(
                    new MultiLanguageBatchInput(
                        new List<MultiLanguageInput>()
                        {
                            new MultiLanguageInput("en", "0", "I really enjoy the new XBox One S. It has a clean look, it has 4K/HDR resolution and it is affordable."),
                            new MultiLanguageInput("en", "1", "The Seattle Seahawks won the Super Bowl in 2014."),
                        })).Result;

            // Printing entity results.
            foreach (var document in result4.Documents)
            {
                Console.WriteLine("Document ID: {0} , Entities: {1}", document.Id, String.Join(", ", document.Entities.Select(entity => entity.Name)));
            }
        }
    }
}
```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [文字分析搭配 Power BI](../tutorials/tutorial-power-bi-key-phrases.md)

## <a name="see-also"></a>另請參閱 

 [文字分析概觀](../overview.md)  
 [常見問題集 (FAQ)](../text-analytics-resource-faq.md)
