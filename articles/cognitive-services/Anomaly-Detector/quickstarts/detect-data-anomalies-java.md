---
title: 快速入門：使用 Anomaly Detector REST API 與 Java 偵測時間序列資料中的異常狀況
titleSuffix: Azure Cognitive Services
description: 使用 Anomaly Detector API 來偵測資料序列中的異常狀況 (以批次或串流資料為單位)。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: quickstart
ms.date: 07/26/2019
ms.author: aahi
ms.openlocfilehash: 001d53cbd7e2a57615ea3da71d128bd210a79921
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/26/2019
ms.locfileid: "68565846"
---
# <a name="quickstart-detect-anomalies-in-your-time-series-data-using-the-anomaly-detector-rest-api-and-java"></a>快速入門：使用 Anomaly Detector REST API 與 Java 偵測時間序列資料中的異常狀況

使用本快速入門以開始使用 Anomaly Detector API 的兩個偵測模式，來偵測時間序列資料中的異常狀況。 此 Java 應用程式會傳送包含 JSON 格式時間序列資料的兩個 API 要求，並取得回應。

| API 要求                                        | 應用程式輸出                                                                                                                         |
|----------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| 以批次方式偵測異常狀況                        | JSON 回應包含時間序列資料中每個資料點的異常狀態 (和其他資料)，以及偵測到的任何異常狀況的位置。 |
| 偵測最新資料點的異常狀態 | JSON 回應包含時間序列資料中最新資料點的異常狀態 (和其他資料)。                                                                                                                                         |

 雖然此應用程式是以 Java 撰寫的，但 API 是一種與大多數程式設計語言都相容的 RESTful Web 服務。

## <a name="prerequisites"></a>必要條件

- [Java&trade; 開發套件 (JDK) 7](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) 或更新版本。

- 從 Maven 存放庫匯入這些程式庫
    - [Java 中的 JSON](https://mvnrepository.com/artifact/org.json/json) 套件
    - [Apache HttpClient](https://mvnrepository.com/artifact/org.apache.httpcomponents/httpclient) 套件

- JSON 檔案包含時間序列資料點。 此快速入門的範例資料可以在 [GitHub](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/request-data.json) 上找到。

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

2. 針對您的訂用帳戶金鑰和端點建立變數。 以下是您可以用於異常偵測的 URI。 這些稍後將會附加至您的服務端點，以建立 API 要求 URL。

    |偵測方法  |URI  |
    |---------|---------|
    |批次偵測    | `/anomalydetector/v1.0/timeseries/entire/detect`        |
    |最新資料點上的偵測     | `/anomalydetector/v1.0/timeseries/last/detect`        |

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

3. 讀入 JSON 資料檔案

    ```java
    String requestData = new String(Files.readAllBytes(Paths.get(dataPath)), "utf-8");
    ```

## <a name="create-a-function-to-send-requests"></a>建立傳送要求的函式

1. 建立名為 `sendRequest()` 的新函式，取用上面建立的變數。 然後執行下列步驟。

2. 建立可將要求傳送至 API 的 `CloseableHttpClient` 物件。 藉由結合您的端點和 Anomaly Detector URL，將要求傳送至 `HttpPost` 要求物件。

3. 使用要求的 `setHeader()` 函式，將 `Content-Type` 標頭設定為 `application/json`，並將您的訂用帳戶金鑰新增至 `Ocp-Apim-Subscription-Key` 標頭。

4. 將要求的 `setEntity()` 函式用於要傳送的資料。

5. 使用用戶端的 `execute()` 函式傳送要求，並將它儲存到 `CloseableHttpResponse` 物件。

6. 建立 `HttpEntity` 物件以儲存回應內容。 使用 `getEntity()` 取得內容。 如果回應不是空的，請將它傳回。

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

## <a name="detect-anomalies-as-a-batch"></a>以批次方式偵測異常狀況

1. 建立名為 `detectAnomaliesBatch()` 的方法，以批次方式偵測整個資料的異常狀況。 使用端點、URL、訂用帳戶金鑰和 json 資料呼叫上方建立的 `sendRequest()` 方法。 取得結果，並將它列印到主控台。

2. 如果回應包含 `code` 欄位，則列印錯誤碼和錯誤訊息。

3. 否則，在資料集中尋找異常狀況的位置。 回應的 `isAnomaly` 欄位包含與指定的資料點是否為異常相關的布林值。 取得 JSON 陣列並逐一查看，然後列印任何 `true` 值的索引。 如果有找到，這些值會對應到異常資料點的索引。

```java
static void detectAnomaliesBatch(String requestData) {
    System.out.println("Detecting anomalies as a batch");
    String result = sendRequest(batchDetectionUrl, endpoint, subscriptionKey, requestData);
    if (result != null) {
        System.out.println(result);
        JSONObject jsonObj = new JSONObject(result);
        if (jsonObj.has("code")) {
            System.out.println(String.format("Detection failed. ErrorCode:%s, ErrorMessage:%s", jsonObj.getString("code"), jsonObj.getString("message")));
        } else {
            JSONArray jsonArray = jsonObj.getJSONArray("isAnomaly");
            System.out.println("Anomalies found in the following data positions:");
            for (int i = 0; i < jsonArray.length(); ++i) {
                if (jsonArray.getBoolean(i))
                    System.out.print(i + ", ");
            }
            System.out.println();
        }
    }
}
```

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>偵測最新資料點的異常狀態

* 建立名為 `detectAnomaliesLatest()` 的方法，以偵測資料集中最後一個資料點的異常狀態。 使用端點、URL、訂用帳戶金鑰和 json 資料呼叫上方建立的 `sendRequest()` 方法。 取得結果，並將它列印到主控台。

```java
static void detectAnomaliesLatest(String requestData) {
    System.out.println("Determining if latest data point is an anomaly");
    String result = sendRequest(latestPointDetectionUrl, endpoint, subscriptionKey, requestData);
    System.out.println(result);
}
```

## <a name="load-your-time-series-data-and-send-the-request"></a>載入時間序列資料，並傳送要求

1. 在您應用程式的主要方法中，讀入 JSON 檔案，其中包含將新增至要求的資料。

2. 呼叫上面建立的兩個異常偵測函式。

```java
public static void main(String[] args) throws Exception {
    String requestData = new String(Files.readAllBytes(Paths.get(dataPath)), "utf-8");
    detectAnomaliesBatch(requestData);
    detectAnomaliesLatest(requestData);
}
```

### <a name="example-response"></a>範例回應

成功的回應會以 JSON 格式傳回。 按一下以下連結，在 GitHub 上檢視 JSON 回應：
* [批次偵測回應範例](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/batch-response.json)
* [最新資料點偵測回應範例](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/latest-point-response.json)

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [REST API 參考資料](https://westus2.dev.cognitive.microsoft.com/docs/services/AnomalyDetector/operations/post-timeseries-entire-detect)
