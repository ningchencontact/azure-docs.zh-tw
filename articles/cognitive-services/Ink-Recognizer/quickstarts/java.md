---
title: 快速入門：辨識筆墨辨識器 REST API 與 Java 的數位筆跡
description: 您可以使用手寫辨識器 API 來啟動 體認到數位筆墨筆劃。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: quickstart
ms.date: 05/02/2019
ms.author: aahi
ms.openlocfilehash: 04f2ac17871bbaf0506fe18122507167b23869a7
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67060948"
---
# <a name="quickstart-recognize-digital-ink-with-the-ink-recognizer-rest-api-and-java"></a>快速入門：辨識筆墨辨識器 REST API 與 Java 的數位筆跡

使用本快速入門中，若要開始使用數位筆墨筆劃上的筆墨辨識器 API。 此 Java 應用程式傳送 API 要求，其中包含 JSON 格式的筆墨筆劃資料，並取得回應。

雖然此應用程式是以 Java 撰寫的，但 API 是一種與大多數程式設計語言都相容的 RESTful Web 服務。

通常，您會從數位筆跡應用程式呼叫 API。 本快速入門會傳送下列的手寫範例的筆墨筆劃資料，從 JSON 檔案。

![映像的手寫文字](../media/handwriting-sample.jpg)

此快速入門的原始程式碼可以在 [GitHub](https://go.microsoft.com/fwlink/?linkid=2089904) 上找到。

## <a name="prerequisites"></a>必要條件

- [Java&trade;開發 Kit(JDK) 7](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)或更新版本。

- 從 Maven 儲存機制匯入這些程式庫
    - [在 Java 中的 JSON](https://mvnrepository.com/artifact/org.json/json)封裝
    - [Apache HttpClient](https://mvnrepository.com/artifact/org.apache.httpcomponents/httpclient)封裝

- 在本快速入門範例筆墨筆劃資料可於[GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/java/InkRecognition/quickstart/example-ink-strokes.json)。

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

2. 建立您的訂用帳戶金鑰和您的端點的變數。 以下是您可以使用手寫辨識的 URI。 它將會附加至您的服務端點，稍後若要建立的 API 要求的 URL。

    ```java
    // Replace the subscriptionKey string value with your valid subscription key.
    static final String subscriptionKey = "YOUR_SUBSCRIPTION_KEY";
    // Replace the dataPath string with a path to the JSON formatted ink stroke data file.
    static final String dataPath = "PATH_TO_INK_STROKE_DATA";
    
    static final String endpoint = "https://api.cognitive.microsoft.com";
    static final String inkRecognitionUrl = "/inkrecognizer/v1.0-preview/recognize";
    ```

## <a name="create-a-function-to-send-requests"></a>建立傳送要求的函式

1. 建立新的函式呼叫`sendRequest()`採用上面所建立的變數。 然後執行下列步驟。

2. 建立`CloseableHttpClient`可以傳送要求給 API 的物件。 傳送要求至`HttpPut`藉由結合您的端點和手寫辨識器 URL 的要求物件。

3. 使用要求`setHeader()`函式來設定`Content-Type`標頭`application/json`，並新增您的訂用帳戶金鑰，以`Ocp-Apim-Subscription-Key`標頭。

4. 使用要求的`setEntity()`要傳送之資料的函式。   

5. 使用用戶端`execute()`函式以傳送要求，並將它儲存到`CloseableHttpResponse`物件。 

6. 建立`HttpEntity`回應內容儲存的物件。 取得與內容`getEntity()`。 如果回應不是空的請將它傳回。
    
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

## <a name="send-an-ink-recognition-request"></a>筆墨辨識要求傳送

建立一個方法，叫做`recognizeInk()`辨識筆墨筆劃資料。 呼叫`sendRequest()`上方建立端點、 url、 訂用帳戶金鑰和 json 資料的方法。 得到的結果，並列印到主控台。

```java
static void recognizeInk(String requestData) {
    System.out.println("Sending an ink recognition request");
    String result = sendRequest(inkRecognitionUrl, endpoint, subscriptionKey, requestData);
    System.out.println(result);
}
```

## <a name="load-your-digital-ink-data-and-send-the-request"></a>載入您的數位筆跡資料並傳送要求

1. 在您的應用程式的 main 方法，讀取 JSON 檔案包含的資料，將會新增至要求中。

2. 呼叫先前建立的筆墨辨識函式。
    
    ```java
    public static void main(String[] args) throws Exception {
        String requestData = new String(Files.readAllBytes(Paths.get(dataPath)), "UTF-8");
        recognizeInk(requestData);
    }
    ```

## <a name="run-the-application-and-view-the-response"></a>執行應用程式，以及檢視回應

執行應用程式。 成功的回應是以 JSON 格式傳回。 您也可以在找到 JSON 回應[GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/java/InkRecognition/quickstart/example-response.json)。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [REST API 參考資料](https://go.microsoft.com/fwlink/?linkid=2089907)


若要了解筆跡辨識 API 在數位筆跡應用程式中的運作方式，請看位於 GitHub 上的下列應用程式範例：
* [C# 和通用 Windows 平台 (UWP)](https://go.microsoft.com/fwlink/?linkid=2089803)  
* [C# 和 Windows Presentation Foundation (WPF)](https://go.microsoft.com/fwlink/?linkid=2089804)
* [Javascript 網頁瀏覽器應用程式](https://go.microsoft.com/fwlink/?linkid=2089908)       
* [Java 和 Android 行動裝置應用程式](https://go.microsoft.com/fwlink/?linkid=2089906)
* [Swift 和 iOS 行動裝置應用程式](https://go.microsoft.com/fwlink/?linkid=2089805)
