---
title: 快速入門：使用筆跡辨識器 REST API 與 C# 來辨識數位筆跡
titleSuffix: Azure Cognitive Services
description: 使用筆跡辨識器 API 開始辨識數位筆跡筆觸。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: quickstart
ms.date: 05/02/2019
ms.author: aahi
ms.openlocfilehash: 6a1b4ab43a7d87ac1162a7f0a3556d6bc3bfbfab
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/10/2019
ms.locfileid: "67721255"
---
# <a name="quickstart-recognize-digital-ink-with-the-ink-recognizer-rest-api-and-c"></a>快速入門：使用筆跡辨識器 REST API 與 C# 來辨識數位筆跡

使用本快速入門，開始將數位筆跡筆觸傳送到筆跡辨識器 API。 此 C# 應用程式會傳送包含 JSON 格式筆跡筆觸資料的 API 要求，並取得回應。

雖然此應用程式是以 C# 撰寫的，但 API 是一種與大多數程式設計語言都相容的 RESTful Web 服務。

通常您會從數位筆跡應用程式呼叫 API。 本快速入門會從 JSON 檔案針對下列手寫範例傳送筆跡筆觸資料。

![手寫文字的影像](../media/handwriting-sample.jpg)

此快速入門的原始程式碼可以在 [GitHub](https://go.microsoft.com/fwlink/?linkid=2089502) 上找到。

## <a name="prerequisites"></a>必要條件

- 任何一版的 [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/)。
- [Newtonsoft.Json](https://www.newtonsoft.com/json)
    - 若要在 Visual Studio 中安裝 Newtonsoft.Json 作為 NuGet 套件：
        1. 以滑鼠右鍵按一下 [Solution Manager] 
        2. 按一下 [管理 NuGet 套件...] 
        3. 然後搜尋 `Newtonsoft.Json` 並安裝該套件。
- 如果您使用 Linux/MacOS，則可以使用 [Mono](https://www.mono-project.com/) 來執行此應用程式。

- 此快速入門的範例筆跡筆觸資料可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/InkRecognition/quickstart/example-ink-strokes.json) 上找到。

[!INCLUDE [cognitive-services-ink-recognizer-signup-requirements](../../../../includes/cognitive-services-ink-recognizer-signup-requirements.md)]


## <a name="create-a-new-application"></a>建立新的應用程式

1. 在 Visual Studio 中，建立新的主控台解決方案並新增下列套件。 

    ```csharp
    using System;
    using System.IO;
    using System.Net;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Text;
    using System.Threading.Tasks;
    using Newtonsoft.Json;
    using Newtonsoft.Json.Linq;
    ```

2. 針對您的訂用帳戶金鑰和端點建立變數。 以下是您可以用於手寫辨識的 URI。 該 URI 稍後會附加至您的服務端點，以建立 API 要求 URL。

    ```csharp
    // Replace the subscriptionKey string with your valid subscription key.
    const string subscriptionKey = "YOUR_SUBSCRIPTION_KEY";

    // Replace the dataPath string with a path to the JSON formatted ink stroke data.
    const string dataPath = @"PATH-TO-INK-STROKE-DATA"; 

    // URI information for ink recognition:
    const string endpoint = "https://api.cognitive.microsoft.com";
    const string inkRecognitionUrl = "/inkrecognizer/v1.0-preview/recognize";
    ```

## <a name="create-a-function-to-send-requests"></a>建立傳送要求的函式

1. 建立名為 `Request` 的新非同步函式，取用上方建立的變數。

2. 使用 `HttpClient` 物件設定用戶端的安全性通訊協定和標頭資訊。 請務必將您的訂用帳戶金鑰新增至 `Ocp-Apim-Subscription-Key` 標頭。 然後建立要求的 `StringContent` 物件。
 
3. 以 `PutAsync()` 傳送要求。 如果要求成功，則傳回回應。  
    
    ```csharp
    static async Task<string> Request(string apiAddress, string endpoint, string subscriptionKey, string requestData){
        
        using (HttpClient client = new HttpClient { BaseAddress = new Uri(apiAddress) }){
            System.Net.ServicePointManager.SecurityProtocol = SecurityProtocolType.Tls12 | SecurityProtocolType.Tls11 | SecurityProtocolType.Tls;
            client.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
            client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", subscriptionKey);

            var content = new StringContent(requestData, Encoding.UTF8, "application/json");
            var res = await client.PutAsync(endpoint, content);
            if (res.IsSuccessStatusCode){
                return await res.Content.ReadAsStringAsync();
            }
            else{
                return $"ErrorCode: {res.StatusCode}";
            }
        }
    }
    ```

## <a name="send-an-ink-recognition-request"></a>傳送筆跡辨識要求

1. 建立名為 `recognizeInk()` 的新函式。 使用您的端點、訂用帳戶金鑰、API 的 URL，以及數位筆跡筆觸資料呼叫 `Request()` 函式，進而建構要求並加以傳送。

2. 將 JSON 物件還原序列化，並將它寫入到主控台。 
    
    ```csharp
    static void recognizeInk(string requestData){

        //construct the request
        var result = Request(
            endpoint,
            inkRecognitionUrl,
            subscriptionKey,
            requestData).Result;

        dynamic jsonObj = Newtonsoft.Json.JsonConvert.DeserializeObject(result);
        System.Console.WriteLine(jsonObj);
    }
    ```

## <a name="load-your-digital-ink-data"></a>載入您的數位筆跡資料

建立名為 `LoadJson()` 的函式以載入筆跡資料 JSON 檔案。 使用 `StreamReader` 和 `JsonTextReader` 來建立 `JObject` 並將它傳回。
    
```csharp
public static JObject LoadJson(string fileLocation){

    var jsonObj = new JObject();

    using (StreamReader file = File.OpenText(fileLocation))
    using (JsonTextReader reader = new JsonTextReader(file)){
        jsonObj = (JObject)JToken.ReadFrom(reader);
    }
    return jsonObj;
}
```

## <a name="send-the-api-request"></a>傳送 API 要求

1. 在您應用程式的主要方法中，使用上面建立的函式來載入 JSON 資料。 

2. 呼叫上面所建立的 `recognizeInk()`函式。 使用 `System.Console.ReadKey()`，讓主控台視窗在執行應用程式後保持開啟。
    
    ```csharp
    static void Main(string[] args){

        var requestData = LoadJson(dataPath);
        string requestString = requestData.ToString(Newtonsoft.Json.Formatting.None);
        recognizeInk(requestString);
        System.Console.WriteLine("\nPress any key to exit ");
        System.Console.ReadKey();
        }
    ```

## <a name="run-the-application-and-view-the-response"></a>執行應用程式並檢視回應

執行應用程式。 成功的回應會以 JSON 格式傳回。 您也可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/InkRecognition/quickstart/example-response.json) 上找到 JSON 回應。


## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [REST API 參考資料](https://go.microsoft.com/fwlink/?linkid=2089907)


若要了解筆跡辨識 API 在數位筆跡應用程式中的運作方式，請看位於 GitHub 上的下列應用程式範例：
* [C# 和通用 Windows 平台 (UWP)](https://go.microsoft.com/fwlink/?linkid=2089803)  
* [C# 和 Windows Presentation Foundation (WPF)](https://go.microsoft.com/fwlink/?linkid=2089804)
* [Javascript 網頁瀏覽器應用程式](https://go.microsoft.com/fwlink/?linkid=2089908)       
* [Java 和 Android 行動裝置應用程式](https://go.microsoft.com/fwlink/?linkid=2089906)
* [Swift 和 iOS 行動裝置應用程式](https://go.microsoft.com/fwlink/?linkid=2089805)
