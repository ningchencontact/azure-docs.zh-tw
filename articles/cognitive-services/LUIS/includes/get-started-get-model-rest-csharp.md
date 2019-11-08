---
title: 在 C# 中使用 REST 呼叫取得模型
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 10/18/2019
ms.author: diberry
ms.openlocfilehash: 503482243f5aa2e7f833257a3a6eb91a3b5c5ec1
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2019
ms.locfileid: "73500706"
---
## <a name="prerequisites"></a>必要條件

* 入門金鑰。
* 匯入來自 cognitive-services-language-understanding GitHub 存放庫的 [TravelAgent](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/quickstarts/change-model/TravelAgent.json) 應用程式。
* 已匯入 TravelAgent 應用程式的 LUIS 應用程式識別碼。 應用程式識別碼會顯示在應用程式儀表板中。
* 應用程式中用來接收表達的版本識別碼。 預設識別碼為 "0.1"。
* [.NET Core V2.2+](https://dotnet.microsoft.com/download)
* [Visual Studio Code](https://code.visualstudio.com/)

## <a name="example-utterances-json-file"></a>範例語句 JSON 檔案

[!INCLUDE [Quickstart explanation of example utterance JSON file](get-started-get-model-json-example-utterances.md)]

## <a name="get-luis-key"></a>取得 LUIS 金鑰

[!INCLUDE [Use authoring key for endpoint](../includes/get-key-quickstart.md)]

## <a name="change-model-programmatically"></a>以程式設計方式變更模型

使用 C# 將機器學習的實體 [API](https://aka.ms/luis-apim-v3-authoring) 新增至應用程式。 

1. 建立以 C# 語言為目標的新主控台應用程式，其專案和資料夾名稱為 `model-with-rest`。 

    ```console
    dotnet new console -lang C# -n model-with-rest
    ```

1. 使用下列 dotnet CLI 命令安裝必要的相依項目。

    ```console
    dotnet add package System.Net.Http
    dotnet add package JsonFormatterPlus
    ```
1. 使用下列程式碼覆寫 Program.cs：

    ```csharp
    using System;
    using System.IO;
    using System.Net.Http;
    using System.Text;
    using System.Threading.Tasks;
    using System.Collections.Generic;
    using System.Linq;
    
    // 3rd party NuGet packages
    using JsonFormatterPlus;
    
    namespace AddUtterances
    {
        class Program
        {
            // NOTE: use your starter key value
            static string authoringKey = "YOUR-KEY";
    
            // NOTE: Replace this endpoint with your starter key endpoint
            // for example, westus.api.cognitive.microsoft.com
            static string endpoint = "YOUR-ENDPOINT";
    
            // NOTE: Replace this with the ID of your LUIS application
            static string appID = "YOUR-APP-ID";
    
            // NOTE: Replace this your version number
            static string appVersion = "0.1";
    
            static string host = String.Format("https://{0}/luis/authoring/v3.0-preview/apps/{1}/versions/{2}/", endpoint, appID, appVersion);
    
            // GET request with authentication
            async static Task<HttpResponseMessage> SendGet(string uri)
            {
                using (var client = new HttpClient())
                using (var request = new HttpRequestMessage())
                {
                    request.Method = HttpMethod.Get;
                    request.RequestUri = new Uri(uri);
                    request.Headers.Add("Ocp-Apim-Subscription-Key", authoringKey);
                    return await client.SendAsync(request);
                }
            }
            // POST request with authentication
            async static Task<HttpResponseMessage> SendPost(string uri, string requestBody)
            {
                using (var client = new HttpClient())
                using (var request = new HttpRequestMessage())
                {
                    request.Method = HttpMethod.Post;
                    request.RequestUri = new Uri(uri);
    
                    if (!String.IsNullOrEmpty(requestBody))
                    {
                        request.Content = new StringContent(requestBody, Encoding.UTF8, "text/json");
                    }
    
                    request.Headers.Add("Ocp-Apim-Subscription-Key", authoringKey);
                    return await client.SendAsync(request);
                }
            }        
            // Add utterances as string with POST request
            async static Task AddUtterances(string utterances)
            {
                string uri = host + "examples";
    
                var response = await SendPost(uri, utterances);
                var result = await response.Content.ReadAsStringAsync();
                Console.WriteLine("Added utterances.");
                Console.WriteLine(JsonFormatter.Format(result));
            }
            // Train app after adding utterances
            async static Task Train()
            {
                string uri = host  + "train";
    
                var response = await SendPost(uri, null);
                var result = await response.Content.ReadAsStringAsync();
                Console.WriteLine("Sent training request.");
                Console.WriteLine(JsonFormatter.Format(result));
            }    
            // Check status of training
            async static Task Status()
            {
                var response = await SendGet(host  + "train");
                var result = await response.Content.ReadAsStringAsync();
                Console.WriteLine("Requested training status.");
                Console.WriteLine(JsonFormatter.Format(result));
            }    
            // Add utterances, train, check status
            static void Main(string[] args)
            {
                string utterances = @"
                [
                    {
                    'text': 'go to Seattle today',
                    'intentName': 'BookFlight',
                    'entityLabels': [
                        {
                        'entityName': 'Location::LocationTo',
                        'startCharIndex': 6,
                        'endCharIndex': 12
                        }
                    ]
                    },
                    {
                        'text': 'a barking dog is annoying',
                        'intentName': 'None',
                        'entityLabels': []
                    }
                ]
                ";            
                AddUtterances(utterances).Wait();
                Train().Wait();
                Status().Wait();
            }
        }
    }
    ```

1. 取代下列值：

    * `YOUR-KEY` 使用您的入門金鑰
    * `YOUR-ENDPOINT` 使用您的端點，例如 `westus2.api.cognitive.microsoft.com`
    * `YOUR-APP-ID` 使用您的應用程式識別碼

1. 建置主控台應用程式。 

    ```console
    dotnet build
    ```

1. 執行主控台應用程式。 主控台輸出會顯示您稍早在瀏覽器視窗中看到的相同 JSON。

    ```console
    dotnet run
    ```

## <a name="luis-keys"></a>LUIS 金鑰

[!INCLUDE [Use authoring key for endpoint](../includes/starter-key-explanation.md)]

## <a name="clean-up-resources"></a>清除資源

您完成本快速入門時，請從檔案系統中刪除檔案。 

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [應用程式的最佳做法](../luis-concept-best-practices.md)