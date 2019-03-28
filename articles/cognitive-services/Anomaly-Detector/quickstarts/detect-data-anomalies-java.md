---
title: 快速入門：偵測異常的時間序列資料使用異常偵測器 REST API 與 Java |Microsoft Docs
description: 使用異常偵測器 API 來偵測異常狀況的資料序列視為一個批次，或對串流資料。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: article
ms.date: 03/26/2019
ms.author: aahi
ms.openlocfilehash: 2a6d356bcf65cdb59e84b34cf2a7478de796e61a
ms.sourcegitcommit: fbfe56f6069cba027b749076926317b254df65e5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2019
ms.locfileid: "58473429"
---
# <a name="quickstart-detect-anomalies-in-your-time-series-data-using-the-anomaly-detector-rest-api-and-java"></a>快速入門：偵測異常的時間序列資料使用異常偵測器 REST API 和 Java

使用本快速入門中，若要開始使用異常偵測器 API 的兩個偵測模式來偵測異常的時間序列資料。 此 Java 應用程式會傳送包含 JSON 格式的時間序列資料，兩個 API 要求，並取得回應。

| API 要求                                        | 應用程式輸出                                                                                                                         |
|----------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| 以批次偵測異常行為                        | JSON 回應，包含時間序列資料和任何偵測到的異常狀況的位置中的每個資料點異常狀態 （和其他資料）。 |
| 偵測異常的狀態最新的資料點 | JSON 回應，其中包含最新的資料點，時間序列資料中的異常狀態 （和其他資料）。                                                                                                                                         |

 雖然此應用程式是以 Java 撰寫的，但 API 是一種與大多數程式設計語言都相容的 RESTful Web 服務。

## <a name="prerequisites"></a>必要條件

- [Java&trade;開發 Kit(JDK) 7](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)或更新版本。

- 從 Maven 儲存機制匯入這些程式庫
    - [在 Java 中的 JSON](https://mvnrepository.com/artifact/org.json/json)封裝
    - [Apache HttpClient](https://mvnrepository.com/artifact/org.apache.httpcomponents/httpclient)封裝

- JSON 檔案包含時間序列資料點。 本快速入門中的範例資料都位於[GitHub](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/request-data.json)。

[!INCLUDE [cognitive-services-anomaly-detector-data-requirements](../../../../includes/cognitive-services-anomaly-detector-data-requirements.md)]

[!INCLUDE [cognitive-services-anomaly-detector-signup-requirements](../../../../includes/cognitive-services-anomaly-detector-signup-requirements.md)]

## <a name="create-a-new-application"></a>建立新的應用程式

1. 在您最愛的 IDE 或編輯器中建立新的 Java 專案，並匯入下列程式庫。

    ```java
    import org.apache.http.HttpEntity;
    import org.apache.http.client.methods.CloseableHttpResponse;
    import org.apache.http.client.methods.HttpPost;
    import org.apache.http.entity.StringEntity;
    import org.apache.http.impl.client.CloseableHttpClient;
    import org.apache.http.impl.client.HttpClients;
    import org.apache.http.util.EntityUtils;
    import org.json.JSONArray;
    import org.json.JSONObject;
    import java.io.IOException;
    import java.nio.file.Files;
    import java.nio.file.Paths;
    ```

2. 建立您的訂用帳戶金鑰和您的端點的變數。 以下是您可以用於異常偵測的 Uri。 這些將會附加至您的服務端點，稍後若要建立的 API 要求 Url。

    |偵測方法  |URI  |
    |---------|---------|
    |批次偵測    | `/anomalydetector/v1.0/timeseries/entire/detect`        |
    |在最新的資料點上的偵測     | `/anomalydetector/v1.0/timeseries/last/detect`        |

    ```java
    // Replace the subscriptionKey string value with your valid subscription key.
    static final String subscriptionKey = "[YOUR_SUBSCRIPTION_KEY]";
    //replace the endpoint URL with the correct one for your subscription. Your endpoint can be found in the Azure portal. 
    //For example: https://westus2.api.cognitive.microsoft.com
    static final String endpoint = "[YOUR_ENDPOINT_URL]";
    // Replace the dataPath string with a path to the JSON formatted time series data.
    static final String dataPath = "[PATH_TO_TIME_SERIES_DATA]";
    static final String latestPointDetectionUrl = "/anomalydetector/v1.0/timeseries/last/detect";
    static final String batchDetectionUrl = "/anomalydetector/v1.0/timeseries/entire/detect";
    ```

3. 讀取 JSON 資料檔案

    ```java
    String requestData = new String(Files.readAllBytes(Paths.get(dataPath)), "UTF-8");
    ```

## <a name="create-a-function-to-send-requests"></a>建立傳送要求的函式

1. 建立新的函式呼叫`sendRequest()`採用上面所建立的變數。 然後執行下列步驟。

2. 建立`CloseableHttpClient`可以傳送要求給 API 的物件。 傳送要求至`HttpPost`藉由結合您的端點和異常偵測器 URL 的要求物件。

3. 使用要求`setHeader()`函式來設定`Content-Type`標頭來`application/json`，並新增您的訂用帳戶金鑰，以`Ocp-Apim-Subscription-Key`標頭。

4. 使用要求的`setEntity()`要傳送之資料的函式。   

5. 使用用戶端`execute()`函式以傳送要求，並將它儲存到`CloseableHttpResponse`物件。 

6. 建立`HttpEntity`回應內容儲存的物件。 取得與內容`getEntity()`。 如果回應不是空的請將它傳回。

```java
static String sendRequest(String apiAddress, String endpoint, String subscriptionKey, String requestData) {
    try (CloseableHttpClient client = HttpClients.createDefault()) {
        HttpPost request = new HttpPost(endpoint + apiAddress);
        // Request headers.
        request.setHeader("Content-Type", "application/json");
        request.setHeader("Ocp-Apim-Subscription-Key", subscriptionKey);
        request.setEntity(new StringEntity(requestData));
        try (CloseableHttpResponse response = client.execute(request)) {
            HttpEntity respEntity = response.getEntity();
            if (respEntity != null) {
                return EntityUtils.toString(respEntity, "utf-8");
            }
        } catch (Exception respEx) {
            respEx.printStackTrace();
        }
    } catch (IOException ex) {
        System.err.println("Exception on Anomaly Detector: " + ex.getMessage());
        ex.printStackTrace();
    }
    return null;
}
```

## <a name="detect-anomalies-as-a-batch"></a>以批次偵測異常行為

1. 建立一個方法，叫做`detectAnomaliesBatch()`來偵測異常行為，整個批次的資料。 呼叫`sendRequest()`上方建立端點、 url、 訂用帳戶金鑰和 json 資料的方法。 得到的結果，並列印到主控台。

2. 在資料集中尋找異常狀況的位置。 回應的`isAnomaly`欄位包含與指定的資料點是否為異常狀況相關的布林值。 取得 JSON 陣列，並逐一查看，列印的任何索引`true`值。 如果找不到任何，這些值會對應到的異常資料點索引。

    
    ```java
    static void detectAnomaliesBatch(String requestData) {
        System.out.println("Detecting anomalies as a batch");
        String result = sendRequest(batchDetectionUrl, endpoint, subscriptionKey, requestData);
        if (result != null) {
            System.out.println(result);
            JSONObject jsonObj = new JSONObject(result);
            JSONArray jsonArray = jsonObj.getJSONArray("isAnomaly");
            System.out.println("Anomalies found in the following data positions:");
            for (int i = 0; i < jsonArray.length(); ++i) {
                if (jsonArray.getBoolean(i))
                    System.out.print(i + ", ");
            }
            System.out.println();
        }
    }
    ```

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>偵測異常的狀態最新的資料點

* 建立一個方法，叫做`detectAnomaliesLatest()`來偵測異常狀態，最後一個資料點的資料集內。 呼叫`sendRequest()`上方建立端點、 url、 訂用帳戶金鑰和 json 資料的方法。 得到的結果，並列印到主控台。

```java
static void detectAnomaliesLatest(String requestData) {
    System.out.println("Determining if latest data point is an anomaly");
    String result = sendRequest(latestPointDetectionUrl, endpoint, subscriptionKey, requestData);
    System.out.println(result);
}
```

## <a name="load-your-time-series-data-and-send-the-request"></a>載入時間序列資料，並傳送要求

1. 在您的應用程式的 main 方法，讀取 JSON 檔案包含的資料，將會新增至要求中。

2. 呼叫先前建立的兩個異常偵測函式。
    
    ```java
    public static void main(String[] args) throws Exception {
        String requestData = new String(Files.readAllBytes(Paths.get(dataPath)), "UTF-8");
        detectAnomaliesBatch(requestData);
        detectAnomaliesLatest(requestData);
    }
    ```

### <a name="example-response"></a>範例回應

成功的回應是以 JSON 格式傳回。 按一下下列連結，可在 GitHub 上檢視 JSON 回應：
* [批次偵測回應範例](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/batch-response.json)
* [最新點偵測回應範例](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/latest-point-response.json)

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [REST API 參考資料](https://westus2.dev.cognitive.microsoft.com/docs/services/AnomalyDetector/operations/post-timeseries-entire-detect)