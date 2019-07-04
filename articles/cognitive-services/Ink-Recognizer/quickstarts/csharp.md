---
title: 快速入門：辨識與筆跡的辨識器 REST API 的數位筆跡和C#
description: 您可以使用手寫辨識器 API 來啟動 體認到數位筆墨筆劃。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: quickstart
ms.date: 05/02/2019
ms.author: aahi
ms.openlocfilehash: d661d6eca6e4916946944c48cc2e5411aeaf8f14
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67060986"
---
# <a name="quickstart-recognize-digital-ink-with-the-ink-recognizer-rest-api-and-c"></a>快速入門：辨識與筆跡的辨識器 REST API 的數位筆跡和C#

您可以使用本快速入門來開始傳送數位筆墨筆劃，筆墨辨識器 api。 這個C#應用程式傳送 API 要求，其中包含 JSON 格式的筆墨筆劃資料，並取得回應。

雖然此應用程式是以 C# 撰寫的，但 API 是一種與大多數程式設計語言都相容的 RESTful Web 服務。

通常，您會從數位筆跡應用程式呼叫 API。 本快速入門會傳送下列的手寫範例的筆墨筆劃資料，從 JSON 檔案。

![映像的手寫文字](../media/handwriting-sample.jpg)

此快速入門的原始程式碼可以在 [GitHub](https://go.microsoft.com/fwlink/?linkid=2089502) 上找到。

## <a name="prerequisites"></a>必要條件

- 任何一版的 [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/)。
- [Newtonsoft.Json](https://www.newtonsoft.com/json)
    - 若要在 Visual studio 中的 NuGet 套件形式安裝 Newtonsoft.Json:
        1. 以滑鼠右鍵按一下**Solution Manager**
        2. 按一下 [管理 NuGet 套件...] 
        3. 搜尋`Newtonsoft.Json`並安裝套件
- 如果您使用 Linux/MacOS，可以使用執行此應用程式[Mono](https://www.mono-project.com/)。

- 在本快速入門範例筆墨筆劃資料可於[GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/InkRecognition/quickstart/example-ink-strokes.json)。

[!INCLUDE [cognitive-services-ink-recognizer-signup-requirements](../../../../includes/cognitive-services-ink-recognizer-signup-requirements.md)]


## <a name="create-a-new-application"></a>建立新的應用程式

1. 在 Visual Studio 中建立新的主控台方案，並新增下列套件。 

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

2. 建立您的訂用帳戶金鑰和您的端點的變數。 以下是您可以使用手寫辨識的 URI。 它將會附加至您的服務端點，稍後若要建立的 API 要求 URl。

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

1. 建立新的非同步函式，呼叫`Request`採用上面所建立的變數。

2. 設定用戶端的安全性通訊協定和標頭資訊使用`HttpClient`物件。 請務必新增您的訂用帳戶金鑰，以`Ocp-Apim-Subscription-Key`標頭。 然後建立`StringContent`要求物件。
 
3. 傳送要求和`PutAsync()`。 如果要求成功，傳回回應。  
    
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

## <a name="send-an-ink-recognition-request"></a>筆墨辨識要求傳送

1. 建立新的函式呼叫`recognizeInk()`。 建構要求，並將它傳送呼叫`Request()`函式與您的端點，訂用帳戶金鑰，API，以及數位筆跡筆劃資料的 URL。

2. 還原序列化 JSON 物件，並寫入主控台。 
    
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

## <a name="load-your-digital-ink-data"></a>載入您的數位筆墨資料

建立函式呼叫`LoadJson()`載入筆墨資料的 JSON 檔案。 使用`StreamReader`並`JsonTextReader`建立`JObject`並將它傳回。
    
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

1. 在您的應用程式的 main 方法，載入將 JSON 資料使用上面建立的函式。 

2. 呼叫`recognizeInk()`上面所建立的函式。 使用`System.Console.ReadKey()`來執行應用程式後，將主控台視窗保持開啟。
    
    ```csharp
    static void Main(string[] args){

        var requestData = LoadJson(dataPath);
        string requestString = requestData.ToString(Newtonsoft.Json.Formatting.None);
        recognizeInk(requestString);
        System.Console.WriteLine("\nPress any key to exit ");
        System.Console.ReadKey();
        }
    ```

## <a name="run-the-application-and-view-the-response"></a>執行應用程式，以及檢視回應

執行應用程式。 成功的回應是以 JSON 格式傳回。 您也可以在找到 JSON 回應[GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/InkRecognition/quickstart/example-response.json)。


## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [REST API 參考資料](https://go.microsoft.com/fwlink/?linkid=2089907)


若要了解筆跡辨識 API 在數位筆跡應用程式中的運作方式，請看位於 GitHub 上的下列應用程式範例：
* [C# 和通用 Windows 平台 (UWP)](https://go.microsoft.com/fwlink/?linkid=2089803)  
* [C# 和 Windows Presentation Foundation (WPF)](https://go.microsoft.com/fwlink/?linkid=2089804)
* [Javascript 網頁瀏覽器應用程式](https://go.microsoft.com/fwlink/?linkid=2089908)       
* [Java 和 Android 行動裝置應用程式](https://go.microsoft.com/fwlink/?linkid=2089906)
* [Swift 和 iOS 行動裝置應用程式](https://go.microsoft.com/fwlink/?linkid=2089805)
