---
title: 快速入門：使用筆跡辨識器 REST API 與 Java 來辨識數位筆跡
titleSuffix: Azure Cognitive Services
description: 使用筆跡辨識器 API 開始辨識數位筆跡筆觸。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: quickstart
ms.date: 09/23/2019
ms.author: aahi
ms.openlocfilehash: 36ff0fe4550b140a722ed25f4e372f7c88581211
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/24/2019
ms.locfileid: "71212678"
---
# <a name="quickstart-recognize-digital-ink-with-the-ink-recognizer-rest-api-and-java"></a>快速入門：使用筆跡辨識器 REST API 與 Java 來辨識數位筆跡

使用本快速入門，開始針對數位筆跡筆觸使用筆跡辨識器 API。 此 Java 應用程式會傳送包含 JSON 格式筆跡筆觸資料的 API 要求，並取得回應。

雖然此應用程式是以 Java 撰寫的，但 API 是一種與大多數程式設計語言都相容的 RESTful Web 服務。

通常您會從數位筆跡應用程式呼叫 API。 本快速入門會從 JSON 檔案針對下列手寫範例傳送筆跡筆觸資料。

![手寫文字的影像](../media/handwriting-sample.jpg)

此快速入門的原始程式碼可以在 [GitHub](https://go.microsoft.com/fwlink/?linkid=2089904) 上找到。

## <a name="prerequisites"></a>必要條件

- [Java&trade; 開發套件 (JDK) 7](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) 或更新版本。

- 從 Maven 存放庫匯入這些程式庫
    - [Java 中的 JSON](https://mvnrepository.com/artifact/org.json/json) 套件
    - [Apache HttpClient](https://mvnrepository.com/artifact/org.apache.httpcomponents/httpclient) 套件

- 此快速入門的範例筆跡筆觸資料可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/java/InkRecognition/quickstart/example-ink-strokes.json) 上找到。

[!INCLUDE [cognitive-services-ink-recognizer-signup-requirements](../../../../includes/cognitive-services-ink-recognizer-signup-requirements.md)]

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
    import java.io.IOException;
    import java.nio.file.Files;
    import java.nio.file.Paths;
    ```

2. 針對您的訂用帳戶金鑰和端點建立變數。 將下面的端點取代為您的筆跡辨識器資源所產生的端點。 將它附加至筆跡辨識器 URI 以連線至 API。

    ```java
    // Replace the subscriptionKey string value with your valid subscription key.
    static final String subscriptionKey = "YOUR_SUBSCRIPTION_KEY";
    // Replace the dataPath string with a path to the JSON formatted ink stroke data file.
    static final String dataPath = "PATH_TO_INK_STROKE_DATA";
    
    static final String endpoint = "https://<your-custom-subdomain>.cognitiveservices.azure.com";
    static final String inkRecognitionUrl = "/inkrecognizer/v1.0-preview/recognize";
    ```

## <a name="create-a-function-to-send-requests"></a>建立傳送要求的函式

1. 建立名為 `sendRequest()` 的新函式，取用上面建立的變數。 然後執行下列步驟。

2. 建立可將要求傳送至 API 的 `CloseableHttpClient` 物件。 藉由結合您的端點和手寫辨識器 URL，將要求傳送至 `HttpPut` 要求物件。

3. 使用要求的 `setHeader()` 函式，將 `Content-Type` 標頭設定為 `application/json`，並將您的訂用帳戶金鑰新增至 `Ocp-Apim-Subscription-Key` 標頭。

4. 將要求的 `setEntity()` 函式用於要傳送的資料。   

5. 使用用戶端的 `execute()` 函式傳送要求，並將它儲存到 `CloseableHttpResponse` 物件。 

6. 建立 `HttpEntity` 物件以儲存回應內容。 使用 `getEntity()` 取得內容。 如果回應不是空的，請將它傳回。
    
    ```java
    static String sendRequest(String apiAddress, String endpoint, String subscriptionKey, String requestData) {
        try (CloseableHttpClient client = HttpClients.createDefault()) {
            HttpPut request = new HttpPut(endpoint + apiAddress);
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
            System.err.println("Exception recognizing ink: " + ex.getMessage());
            ex.printStackTrace();
        }
        return null;
    }
    ```

## <a name="send-an-ink-recognition-request"></a>傳送筆跡辨識要求

建立名為 `recognizeInk()` 的方法以辨識您的筆跡筆觸資料。 使用端點、URL、訂用帳戶金鑰和 json 資料呼叫上方建立的 `sendRequest()` 方法。 取得結果，並將它列印到主控台。

```java
static void recognizeInk(String requestData) {
    System.out.println("Sending an ink recognition request");
    String result = sendRequest(inkRecognitionUrl, endpoint, subscriptionKey, requestData);
    System.out.println(result);
}
```

## <a name="load-your-digital-ink-data-and-send-the-request"></a>載入數位筆跡資料並傳送要求

1. 在您應用程式的主要方法中，讀入 JSON 檔案，其中包含將新增至要求的資料。

2. 呼叫上面建立的筆跡辨識函式。
    
    ```java
    public static void main(String[] args) throws Exception {
        String requestData = new String(Files.readAllBytes(Paths.get(dataPath)), "UTF-8");
        recognizeInk(requestData);
    }
    ```

## <a name="run-the-application-and-view-the-response"></a>執行應用程式並檢視回應

執行應用程式。 成功的回應會以 JSON 格式傳回。 您也可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/java/InkRecognition/quickstart/example-response.json) 上找到 JSON 回應。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [REST API 參考資料](https://go.microsoft.com/fwlink/?linkid=2089907)


若要了解筆跡辨識 API 在數位筆跡應用程式中的運作方式，請看位於 GitHub 上的下列應用程式範例：
* [C# 和通用 Windows 平台 (UWP)](https://go.microsoft.com/fwlink/?linkid=2089803)  
* [C# 和 Windows Presentation Foundation (WPF)](https://go.microsoft.com/fwlink/?linkid=2089804)
* [Javascript 網頁瀏覽器應用程式](https://go.microsoft.com/fwlink/?linkid=2089908)       
* [Java 和 Android 行動裝置應用程式](https://go.microsoft.com/fwlink/?linkid=2089906)
* [Swift 和 iOS 行動裝置應用程式](https://go.microsoft.com/fwlink/?linkid=2089805)
