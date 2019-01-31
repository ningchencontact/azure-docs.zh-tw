---
title: 快速入門：使用 Bing 圖像式搜尋 REST API 和 Java 來取得影像見解
titleSuffix: Azure Cognitive Services
description: 了解如何將影像上傳到 Bing 圖像式搜尋 API 並取得其見解。
services: cognitive-services
author: swhite-msft
manager: cgronlun
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: quickstart
ms.date: 5/16/2018
ms.author: scottwhi
ms.openlocfilehash: 67cd215479efb40e3eb7e81c6c519a59b6526f47
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/29/2019
ms.locfileid: "55172029"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-rest-api-and-java"></a>快速入門：使用 Bing 圖像式搜尋 REST API 和 Java 來取得影像見解

使用本快速入門來進行您對 Bing 圖像式搜尋 API 的第一次呼叫並檢視搜尋結果。 這個簡單的 C# 應用程式會將影像上傳至 API，並顯示傳回的相關資訊。 雖然此應用程式是以 Java 撰寫的，但 API 是一種與大多數程式設計語言都相容的 RESTful Web 服務。

上傳本機影像時，表單資料必須包含 Content-Disposition 標頭。 其 `name` 參數必須設定為 "image"，而 `filename` 參數可以設定為任何字串。 表單的內容是影像的二進位檔案。 您可以上傳的影像大小上限為 1 MB。

```
--boundary_1234-abcd
Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"

ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

--boundary_1234-abcd--
```

## <a name="prerequisites"></a>必要條件

* [Java 開發套件 (JDK) 7 或 8](https://aka.ms/azure-jdks)
* [Gson 程式庫](https://github.com/google/gson)
* [Apache HttpComponents](http://hc.apache.org/downloads.cgi)


[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="create-and-initialize-a-project"></a>建立專案並將其初始化

1. 在您最愛的 IDE 或編輯器中建立新的 Java 專案，並匯入下列程式庫。

    ```java
    import java.util.*;
    import java.io.*;
    import com.google.gson.Gson;
    import com.google.gson.GsonBuilder;
    import com.google.gson.JsonObject;
    import com.google.gson.JsonParser;
    
    // HttpClient libraries
    
    import org.apache.http.HttpEntity;
    import org.apache.http.HttpResponse;
    import org.apache.http.client.methods.HttpPost;
    import org.apache.http.entity.ContentType;
    import org.apache.http.entity.mime.MultipartEntityBuilder;
    import org.apache.http.impl.client.CloseableHttpClient;
    import org.apache.http.impl.client.HttpClientBuilder;
    ```

2. 為您的 API 端點、訂用帳戶金鑰以及您影像的路徑，建立變數。 

    ```java
    static String endpoint = "https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch";
    static String subscriptionKey = "your-key-here";
    static String imagePath = "path-to-your-image";
    ```

## <a name="create-the-json-parser"></a>建立 JSON 剖析器

建立一個方法，讓來自 API 的 JSON 回應更容易使用 `JsonParser` 閱讀。

    ```java
    public static String prettify(String json_text) {
            JsonParser parser = new JsonParser();
            JsonObject json = parser.parse(json_text).getAsJsonObject();
            Gson gson = new GsonBuilder().setPrettyPrinting().create();
            return gson.toJson(json);
        }
    ```

## <a name="construct-the-search-request-and-query"></a>建構搜尋要求和查詢

1. 在應用程式的 Main 方法中，使用 `HttpClientBuilder.create().build();` 建立 Http 用戶端。

    ```java
    CloseableHttpClient httpClient = HttpClientBuilder.create().build();
    ```

2. 建立 `HttpEntity` 以將影像上傳至 API。

    ```java
    HttpEntity entity = MultipartEntityBuilder
        .create()
        .addBinaryBody("image", new File(imagePath))
        .build();
    ```

3. 建立您端點的 `httpPost` 物件，並設定要使用您訂用帳戶金鑰的標頭。

    ```java
    HttpPost httpPost = new HttpPost(endpoint);
    httpPost.setHeader("Ocp-Apim-Subscription-Key", subscriptionKey);
    httpPost.setEntity(entity);
    ```

## <a name="receive-and-process-the-json-response"></a>接收及處理 JSON 回應

1. 使用 `HttpClient.execute()` 將要求傳送至 API，並且在 `InputStream` 物件中儲存回應。
    
    ```java
    HttpResponse response = httpClient.execute(httpPost);
    InputStream stream = response.getEntity().getContent();
    ```

2. 儲存 JSON 字串並印出回應。

```java
String json = new Scanner(stream).useDelimiter("\\A").next();
System.out.println("\nJSON Response:\n");
System.out.println(prettify(json));
```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [建置自訂搜尋 Web 應用程式](../tutorial-bing-visual-search-single-page-app.md)
