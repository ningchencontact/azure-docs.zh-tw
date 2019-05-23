---
title: 快速入門：偵測異常的時間序列資料使用異常偵測器 REST API 和C#|Microsoft Docs
description: 使用異常偵測器 API 來偵測異常狀況的資料序列視為一個批次，或對串流資料。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: article
ms.date: 03/26/2019
ms.author: aahi
ms.openlocfilehash: 04b331f3b63ad6400b4bb8efcd053d04ac88989b
ms.sourcegitcommit: 1fbc75b822d7fe8d766329f443506b830e101a5e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/14/2019
ms.locfileid: "65595835"
---
# <a name="quickstart-detect-anomalies-in-your-time-series-data-using-the-anomaly-detector-rest-api-and-c"></a>快速入門：偵測異常的時間序列資料使用異常偵測器 REST API 和C# 

使用本快速入門中，若要開始使用異常偵測器 API 的兩個偵測模式來偵測異常的時間序列資料。 這個C#應用程式會傳送兩個 API 要求，其中包含 JSON 格式的時間序列資料，並取得回應。

| API 要求                                        | 應用程式輸出                                                                                                                         |
|----------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| 以批次偵測異常行為                        | JSON 回應，包含時間序列資料和任何偵測到的異常狀況的位置中的每個資料點異常狀態 （和其他資料）。 |
| 偵測異常的狀態最新的資料點 | JSON 回應，其中包含最新的資料點，時間序列資料中的異常狀態 （和其他資料）。                                                                                                                                         |

 雖然此應用程式是以 C# 撰寫的，但 API 是一種與大多數程式設計語言都相容的 RESTful Web 服務。

## <a name="prerequisites"></a>必要條件

- 任何版本的[Visual Studio 2017 或更新版本](https://visualstudio.microsoft.com/downloads/)，

- [Json.NET](https://www.newtonsoft.com/json) 架構 (以 NuGet 套件形式提供)。 若要在 Visual Studio 中的 NuGet 套件形式安裝 Newtonsoft.Json:
    
    1. 以滑鼠右鍵按一下您的專案中**方案總管 中**。
    2. 選取 **管理 NuGet 封裝**。
    3. 搜尋*Newtonsoft.Json*並安裝套件。

- 如果您使用 Linux/MacOS，可以使用在執行此應用程式[Mono](https://www.mono-project.com/)。

- JSON 檔案包含時間序列資料點。 本快速入門中的範例資料都位於[GitHub](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/request-data.json)。

[!INCLUDE [cognitive-services-anomaly-detector-data-requirements](../../../../includes/cognitive-services-anomaly-detector-data-requirements.md)]

[!INCLUDE [cognitive-services-anomaly-detector-signup-requirements](../../../../includes/cognitive-services-anomaly-detector-signup-requirements.md)]

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
    ```

2. 建立您的訂用帳戶金鑰和您的端點的變數。 以下是您可以用於異常偵測的 Uri。 這些將會附加至您的服務端點，稍後若要建立的 API 要求 Url。

    |偵測方法  |URI  |
    |---------|---------|
    |批次偵測    | `/anomalydetector/v1.0/timeseries/entire/detect`        |
    |在最新的資料點上的偵測     | `/anomalydetector/v1.0/timeseries/last/detect`        |
    
    ```csharp
    // Replace the subscriptionKey string value with your valid subscription key.
    const string subscriptionKey = "[YOUR_SUBSCRIPTION_KEY]";
    // Replace the endpoint URL with the correct one for your subscription. 
    // Your endpoint can be found in the Azure portal. For example: https://westus2.api.cognitive.microsoft.com
    const string endpoint = "[YOUR_ENDPOINT_URL]";
    // Replace the dataPath string with a path to the JSON formatted time series data.
    const string dataPath = "[PATH_TO_TIME_SERIES_DATA]";
    const string latestPointDetectionUrl = "/anomalydetector/v1.0/timeseries/last/detect";
    const string batchDetectionUrl = "/anomalydetector/v1.0/timeseries/entire/detect";
    ```

## <a name="create-a-function-to-send-requests"></a>建立傳送要求的函式

1. 建立新的非同步函式，呼叫`Request`採用上面所建立的變數。

2. 設定用戶端的安全性通訊協定和標頭資訊使用`HttpClient`物件。 請務必新增您的訂用帳戶金鑰，以`Ocp-Apim-Subscription-Key`標頭。 然後建立`StringContent`要求物件。

3. 傳送要求和`PostAsync()`，然後傳回回應。

```csharp
static async Task<string> Request(string apiAddress, string endpoint, string subscriptionKey, string requestData){
    using (HttpClient client = new HttpClient { BaseAddress = new Uri(apiAddress) }){
        System.Net.ServicePointManager.SecurityProtocol = SecurityProtocolType.Tls12 | SecurityProtocolType.Tls11 | SecurityProtocolType.Tls;
        client.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
        client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", subscriptionKey);

        var content = new StringContent(requestData, Encoding.UTF8, "application/json");
        var res = await client.PostAsync(endpoint, content);
        return await res.Content.ReadAsStringAsync();
    }
}
```

## <a name="detect-anomalies-as-a-batch"></a>以批次偵測異常行為

1. 建立新的函式呼叫`detectAnomaliesBatch()`。 建構要求，並將它傳送呼叫`Request()`與您的端點、 訂用帳戶金鑰、 批次的異常偵測的 URL 和時間序列資料的函式。

2. 還原序列化 JSON 物件，並寫入主控台。

3. 如果回應包含`code`欄位中，列印的錯誤碼和錯誤訊息。 

4. 否則資料集中尋找異常狀況的位置。 回應的`isAnomaly`欄位包含布林值陣列，其中的每個都表示資料點是否為異常狀況。 將其轉換成字串陣列，與回應物件`ToObject<bool[]>()`函式。 逐一查看陣列，並列印的任何索引`true`值。 如果找不到任何，這些值會對應到的異常資料點索引。

```csharp
static void detectAnomaliesBatch(string requestData){
    System.Console.WriteLine("Detecting anomalies as a batch");

    var result = Request(
        endpoint,
        batchDetectionUrl,
        subscriptionKey,
        requestData).Result;

    dynamic jsonObj = Newtonsoft.Json.JsonConvert.DeserializeObject(result);
    System.Console.WriteLine(jsonObj);

    if (jsonObj["code"] != null){
        System.Console.WriteLine($"Detection failed. ErrorCode:{jsonObj["code"]}, ErrorMessage:{jsonObj["message"]}");
    }
    else{
        bool[] anomalies = jsonObj["isAnomaly"].ToObject<bool[]>();
        System.Console.WriteLine("\nAnomalies detected in the following data positions:");
        for (var i = 0; i < anomalies.Length; i++){
            if (anomalies[i])
            {
                System.Console.Write(i + ", ");
            }
        }
    }
}
```

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>偵測異常的狀態最新的資料點

1. 建立新的函式呼叫`detectAnomaliesLatest()`。 建構要求，並將它傳送呼叫`Request()`與您的端點、 訂用帳戶金鑰、 最新的點異常偵測的 URL 和時間序列資料的函式。

2. 還原序列化 JSON 物件，並寫入主控台。

```csharp
static void detectAnomaliesLatest(string requestData){
    System.Console.WriteLine("\n\nDetermining if latest data point is an anomaly");
    var result = Request(
        endpoint,
        latestPointDetectionUrl,
        subscriptionKey,
        requestData).Result;

    dynamic jsonObj = Newtonsoft.Json.JsonConvert.DeserializeObject(result);
    System.Console.WriteLine(jsonObj);
}
```

## <a name="load-your-time-series-data-and-send-the-request"></a>載入時間序列資料，並傳送要求

1. 在您的應用程式的 main 方法，載入您的 JSON 時間序列資料，與`File.ReadAllText()`。 

2. 呼叫先前建立的異常偵測函式。 使用`System.Console.ReadKey()`來執行應用程式後，將主控台視窗保持開啟。

```csharp
static void Main(string[] args){

    var requestData = File.ReadAllText(dataPath);

    detectAnomaliesBatch(requestData);
    detectAnomaliesLatest(requestData);

    System.Console.ReadKey();
}
```

### <a name="example-response"></a>範例回應

成功的回應是以 JSON 格式傳回。 按一下下列連結，可在 GitHub 上檢視 JSON 回應：
* [批次偵測回應範例](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/batch-response.json)
* [最新點偵測回應範例](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/latest-point-response.json)

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [REST API 參考資料](https://westus2.dev.cognitive.microsoft.com/docs/services/AnomalyDetector/operations/post-timeseries-entire-detect)
