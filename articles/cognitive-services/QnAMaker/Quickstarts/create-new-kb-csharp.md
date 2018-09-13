---
title: 建立新的知識庫 - 快速入門 C# - 適用於 Microsoft QnA Maker API (V4) - Azure 認知服務 | Microsoft Docs
description: 使用 C# 建立知識庫，以保存您的常見問題集或產品手冊，以便開始使用 QnA Maker。
services: cognitive-services
author: noellelacharite
manager: nolachar
ms.service: cognitive-services
ms.technology: qna-maker
ms.topic: quickstart
ms.date: 06/15/2018
ms.author: nolachar
ms.openlocfilehash: 16d17a335bb84212497010b3eeb930582fa86dc4
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/28/2018
ms.locfileid: "43750251"
---
# <a name="create-a-new-knowledge-base-in-c"></a>使用 C# 建立新的知識庫

本快速入門會逐步引導您，以程式設計方式建立範例 QnA Maker 知識庫，該知識庫會出現在認知服務 API 帳戶的 Azure 儀表板中。

以下提供兩個範例常見問題集 URL (字串知識庫形式的 'urls')。 QnA Maker 會從半結構化的內容 (就像常見問題集) 中自動擷取問題和答案，本[資料來源](../Concepts/data-sources-supported.md)文件中有更詳細的說明。 您也可以在此快速入門中，使用您自己的常見問題集 URL。

## <a name="prerequisites"></a>必要條件

如果您慣用的 IDE 是 Visual Studio，您需要 [Visual Studio 2017](https://www.visualstudio.com/downloads/) 以在 Windows 上執行此程式碼範例。 (可使用免費的 Community Edition)。

您必須具備已選擇 **QnA Maker** 作為資源的[認知服務 API 帳戶](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)。 您將需要來自 [Azure 儀表板](https://portal.azure.com/#create/Microsoft.CognitiveServices)中新 API 帳戶的付費訂用帳戶金鑰。 若要擷取您的金鑰，請選取儀表板中 [資源管理] 下方的 [金鑰]。 這兩個金鑰都適用於本快速入門。

![Azure 儀表板服務金鑰](../media/sub-key.png)

## <a name="create-knowledge-base"></a>建立知識庫

以下程式碼使用 [Create](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff) 方法來建立新的知識庫。

1. 在您慣用的 IDE 中，建立新的 .NET Framework C# 主控台應用程式。
2. 新增下方提供的程式碼。
3. 以您的有效訂用帳戶金鑰取代 `key` 值。
4. 執行程式。

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Text;
using System.Threading;
using System.Threading.Tasks;

// NOTE: Install the Newtonsoft.Json NuGet package.
using Newtonsoft.Json;

namespace QnAMaker
{
    class Program
    {
        // Represents the various elements used to create HTTP request URIs
        // for QnA Maker operations.
        static string host = "https://westus.api.cognitive.microsoft.com";
        static string service = "/qnamaker/v4.0";
        static string method = "/knowledgebases/create";

        // NOTE: Replace this value with a valid QnA Maker subscription key.
        static string key = "YOUR SUBSCRIPTION KEY HERE";

        /// <summary>
        /// Defines the data source used to create the knowledge base.
        /// The data source includes a QnA pair, with metadata, 
        /// the URL for the QnA Maker FAQ article, and 
        /// the URL for the Azure Bot Service FAQ article.
        /// </summary>
        static string kb = @"
{
  'name': 'QnA Maker FAQ',
  'qnaList': [
    {
      'id': 0,
      'answer': 'You can use our REST APIs to manage your knowledge base. See here for details: https://westus.dev.cognitive.microsoft.com/docs/services/58994a073d9e04097c7ba6fe/operations/58994a073d9e041ad42d9baa',
      'source': 'Custom Editorial',
      'questions': [
        'How do I programmatically update my knowledge base?'
      ],
      'metadata': [
        {
          'name': 'category',
          'value': 'api'
        }
      ]
    }
  ],
  'urls': [
    'https://docs.microsoft.com/en-in/azure/cognitive-services/qnamaker/faqs',
    'https://docs.microsoft.com/en-us/bot-framework/resources-bot-framework-faq'
  ],
  'files': []
}
";

        /// <summary>
        /// Represents the HTTP response returned by an HTTP request.
        /// </summary>
        public struct Response
        {
            public HttpResponseHeaders headers;
            public string response;

            public Response(HttpResponseHeaders headers, string response)
            {
                this.headers = headers;
                this.response = response;
            }
        }

        /// <summary>
        /// Formats and indents JSON for display.
        /// </summary>
        /// <param name="s">The JSON to format and indent.</param>
        /// <returns>A string containing formatted and indented JSON.</returns>
        static string PrettyPrint(string s)
        {
            return JsonConvert.SerializeObject(JsonConvert.DeserializeObject(s), Formatting.Indented);
        }

        /// <summary>
        /// Asynchronously sends a POST HTTP request.
        /// </summary>
        /// <param name="uri">The URI of the HTTP request.</param>
        /// <param name="body">The body of the HTTP request.</param>
        /// <returns>A <see cref="System.Threading.Tasks.Task{TResult}(QnAMaker.Program.Response)"/> 
        /// object that represents the HTTP response."</returns>
        async static Task<Response> Post(string uri, string body)
        {
            using (var client = new HttpClient())
            using (var request = new HttpRequestMessage())
            {
                request.Method = HttpMethod.Post;
                request.RequestUri = new Uri(uri);
                request.Content = new StringContent(body, Encoding.UTF8, "application/json");
                request.Headers.Add("Ocp-Apim-Subscription-Key", key);

                var response = await client.SendAsync(request);
                var responseBody = await response.Content.ReadAsStringAsync();
                return new Response(response.Headers, responseBody);
            }
        }

        /// <summary>
        /// Asynchronously sends a GET HTTP request.
        /// </summary>
        /// <param name="uri">The URI of the HTTP request.</param>
        /// <returns>A <see cref="System.Threading.Tasks.Task{TResult}(QnAMaker.Program.Response)"/> 
        /// object that represents the HTTP response."</returns>
        async static Task<Response> Get(string uri)
        {
            using (var client = new HttpClient())
            using (var request = new HttpRequestMessage())
            {
                request.Method = HttpMethod.Get;
                request.RequestUri = new Uri(uri);
                request.Headers.Add("Ocp-Apim-Subscription-Key", key);

                var response = await client.SendAsync(request);
                var responseBody = await response.Content.ReadAsStringAsync();
                return new Response(response.Headers, responseBody);
            }
        }

        /// <summary>
        /// Creates a knowledge base.
        /// </summary>
        /// <param name="kb">The data source for the knowledge base.</param>
        /// <returns>A <see cref="System.Threading.Tasks.Task{TResult}(QnAMaker.Program.Response)"/> 
        /// object that represents the HTTP response."</returns>
        /// <remarks>The method constructs the URI to create a knowledge base in QnA Maker, and then
        /// asynchronously invokes the <see cref="QnAMaker.Program.Post(string, string)"/> method
        /// to send the HTTP request.</remarks>
        async static Task<Response> PostCreateKB(string kb)
        {
            // Builds the HTTP request URI.
            string uri = host + service + method;
            
            // Writes the HTTP request URI to the console, for display purposes.
            Console.WriteLine("Calling " + uri + ".");

            // Asynchronously invokes the Post(string, string) method, using the
            // HTTP request URI and the specified data source.
            return await Post(uri, kb);
        }

        /// <summary>
        /// Gets the status of the specified QnA Maker operation.
        /// </summary>
        /// <param name="operation">The QnA Maker operation to check.</param>
        /// <returns>A <see cref="System.Threading.Tasks.Task{TResult}(QnAMaker.Program.Response)"/> 
        /// object that represents the HTTP response."</returns>
        /// <remarks>The method constructs the URI to get the status of a QnA Maker operation, and
        /// then asynchronously invokes the <see cref="QnAMaker.Program.Get(string)"/> method
        /// to send the HTTP request.</remarks>
        async static Task<Response> GetStatus(string operation)
        {
            // Builds the HTTP request URI.
            string uri = host + service + operation;

            // Writes the HTTP request URI to the console, for display purposes.
            Console.WriteLine("Calling " + uri + ".");

            // Asynchronously invokes the Get(string) method, using the
            // HTTP request URI.
            return await Get(uri);
        }

        /// <summary>
        /// Creates a knowledge base, periodically checking status 
        /// until the knowledge base is created.
        /// </summary>
        async static void CreateKB()
        {
            try
            {
                // Starts the QnA Maker operation to create the knowledge base.
                var response = await PostCreateKB(kb);

                // Retrieves the operation ID, so the operation's status can be
                // checked periodically.
                var operation = response.headers.GetValues("Location").First();

                // Displays the JSON in the HTTP response returned by the 
                // PostCreateKB(string) method.
                Console.WriteLine(PrettyPrint(response.response));

                // Iteratively gets the state of the operation creating the
                // knowledge base. Once the operation state is set to something other
                // than "Running" or "NotStarted", the loop ends.
                var done = false;
                while (true != done)
                {
                    // Gets the status of the operation.
                    response = await GetStatus(operation);

                    // Displays the JSON in the HTTP response returned by the
                    // GetStatus(string) method.
                    Console.WriteLine(PrettyPrint(response.response));

                    // Deserialize the JSON into key-value pairs, to retrieve the
                    // state of the operation.
                    var fields = JsonConvert.DeserializeObject<Dictionary<string, string>>(response.response);

                    // Gets and checks the state of the operation.
                    String state = fields["operationState"];
                    if (state.CompareTo("Running") == 0 || state.CompareTo("NotStarted") == 0)
                    {
                        // QnA Maker is still creating the knowledge base. The thread is 
                        // paused for a number of seconds equal to the Retry-After header value,
                        // and then the loop continues.
                        var wait = response.headers.GetValues("Retry-After").First();
                        Console.WriteLine("Waiting " + wait + " seconds...");
                        Thread.Sleep(Int32.Parse(wait) * 1000);
                    }
                    else
                    {
                        // QnA Maker has completed creating the knowledge base. 
                        done = true;
                    }
                }
            }
            catch
            {
                // An error occurred while creating the knowledge base. Ensure that
                // you included your QnA Maker subscription key where directed in the sample.
                Console.WriteLine("An error occurred while creating the knowledge base.");
            }
            finally
            {
                Console.WriteLine("Press any key to continue.");
            }

        }

        static void Main(string[] args)
        {
            // Invoke the CreateKB() method to create a knowledge base, periodically 
            // checking the status of the QnA Maker operation until the 
            // knowledge base is created.
            CreateKB();

            // The console waits for a key to be pressed before closing.
            Console.ReadLine();
        }
    }
}
  
```

## <a name="understand-what-qna-maker-returns"></a>了解 QnA Maker 會傳回的項目

成功的回應會以 JSON 格式傳回，如下列範例所示。 您的結果可能會稍有不同。 如果最後呼叫傳回「成功」狀態，代表您的知識庫已成功建立。 若要進行疑難排解，請參閱 QnA Maker API 的[取得作業詳細資料](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/operations_getoperationdetails) (英文)。

```json
Calling https://westus.api.cognitive.microsoft.com/qnamaker/v4.0/knowledgebases/create.
{
  "operationState": "NotStarted",
  "createdTimestamp": "2018-06-25T10:30:15Z",
  "lastActionTimestamp": "2018-06-25T10:30:15Z",
  "userId": "0d85ec291c204197a70cfec51725cd22",
  "operationId": "d9d40918-01bd-49f4-88b4-129fbc434c94"
}
Calling https://westus.api.cognitive.microsoft.com/qnamaker/v4.0/operations/d9d40918-01bd-49f4-88b4-129fbc434c94.
{
  "operationState": "Running",
  "createdTimestamp": "2018-06-25T10:30:15Z",
  "lastActionTimestamp": "2018-06-25T10:30:15Z",
  "userId": "0d85ec291c184197a70cfeb51025cd22",
  "operationId": "d9d40918-01bd-49f4-88b4-129fbc434c94"
}
Waiting 30 seconds...
Calling https://westus.api.cognitive.microsoft.com/qnamaker/v4.0/operations/d9d40918-01bd-49f4-88b4-129fbc434c94.
{
  "operationState": "Running",
  "createdTimestamp": "2018-06-25T10:30:15Z",
  "lastActionTimestamp": "2018-06-25T10:30:15Z",
  "userId": "0d85ec221c284197a70gfeb51725cd22",
  "operationId": "d9d40918-01bd-49f4-88b4-129fbc434c94"
}
Waiting 30 seconds...
Calling https://westus.api.cognitive.microsoft.com/qnamaker/v4.0/operations/d9d40918-01bd-49f4-88b4-129fbc434c94.
{
  "operationState": "Succeeded",
  "createdTimestamp": "2018-06-25T10:30:15Z",
  "lastActionTimestamp": "2018-06-25T10:30:51Z",
  "resourceLocation": "/knowledgebases/1d9eb2a1-de2a-4709-91b2-f6ea8afb6fb9",
  "userId": "0d85ec294c284197a70cfeb51775cd22",
  "operationId": "d9d40918-01bd-49f4-88b4-129fbc434c94"
}
Press any key to continue.
```

一旦建立您的知識庫，您可以在 QnA Maker 入口網站的[我的知識庫](https://www.qnamaker.ai/Home/MyServices) (英文) 頁面中檢視。 選取您的知識庫名稱 (例如 QnA Maker 常見問題集) 以進行檢視。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [QnA Maker (V4) REST API 參考](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff)
