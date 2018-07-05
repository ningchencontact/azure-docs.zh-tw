---
title: 表情 API 適用於 Android 的 Java 快速入門 | Microsoft Docs
description: 取得資訊和程式碼範例，以協助您搭配適用於 Android 的 Java 快速開始使用認知服務中的表情 API。
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: emotion-api
ms.topic: article
ms.date: 05/23/2017
ms.author: anroth
ms.openlocfilehash: 0e7d3991b195a83a8b87e306b3b34fbed2098581
ms.sourcegitcommit: 0fa8b4622322b3d3003e760f364992f7f7e5d6a9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/27/2018
ms.locfileid: "37018021"
---
# <a name="emotion-api-java-for-android-quick-start"></a>表情 API 適用於 Android 的 Java 快速入門

> [!IMPORTANT]
> 影片 API 預覽將於 2017 年 10 月 30 日結束。 請改為試用新的[影片索引器 API 預覽](https://azure.microsoft.com/services/cognitive-services/video-indexer/)，以輕鬆影片中擷取深入解析，並增強內容探索體驗，像是偵測說出口的話、臉部、人物及表情而得來的搜尋結果。 [深入了解](https://docs.microsoft.com/azure/cognitive-services/video-indexer/video-indexer-overview)。

本文提供資訊和程式碼範例，以協助您快速開始使用表情 API Android 用戶端程式庫中的[表情辨識方法](https://westus.dev.cognitive.microsoft.com/docs/services/5639d931ca73072154c1ce89/operations/563b31ea778daf121cc3a5fa) \(英文\)。 範例能示範如何使用 Java 來辨識人員所表現出來的表情。 

## <a name="prerequisites"></a>先決條件
* 從[這裡](https://github.com/Microsoft/Cognitive-emotion-android) \(英文\) 取得表情 API 適用於 Android 的 Java SDK
* 從[這裡](https://azure.microsoft.com/try/cognitive-services/)取得免費的訂用帳戶金鑰

## <a name="recognize-emotions-java-for-android-example-request"></a>辨識表情適用於 Android 的 Java 範例要求

```java
// // This sample uses the Apache HTTP client from HTTP Components (http://hc.apache.org/httpcomponents-client-ga/)
import java.net.URI;
import org.apache.http.HttpEntity;
import org.apache.http.HttpResponse;
import org.apache.http.client.HttpClient;
import org.apache.http.client.methods.HttpPost;
import org.apache.http.entity.StringEntity;
import org.apache.http.client.utils.URIBuilder;
import org.apache.http.impl.client.DefaultHttpClient;
import org.apache.http.util.EntityUtils;

public class Main
{
    public static void main(String[] args)
    {
        HttpClient httpClient = new DefaultHttpClient();

        try
        {
            // NOTE: You must use the same region in your REST call as you used to obtain your subscription keys.
            //   For example, if you obtained your subscription keys from westcentralus, replace "westus" in the 
            //   URL below with "westcentralus".
            URIBuilder uriBuilder = new URIBuilder("https://westus.api.cognitive.microsoft.com/emotion/v1.0/recognize");

            URI uri = uriBuilder.build();
            HttpPost request = new HttpPost(uri);

            // Request headers. Replace the example key below with your valid subscription key.
            request.setHeader("Content-Type", "application/json");
            request.setHeader("Ocp-Apim-Subscription-Key", "13hc77781f7e4b19b5fcdd72a8df7156");

            // Request body. Replace the example URL below with the URL of the image you want to analyze.
            StringEntity reqEntity = new StringEntity("{ \"url\": \"http://example.com/images/test.jpg\" }");
            request.setEntity(reqEntity);

            HttpResponse response = httpClient.execute(request);
            HttpEntity entity = response.getEntity();

            if (entity != null)
            {
                System.out.println(EntityUtils.toString(entity));
            }
        }
        catch (Exception e)
        {
            System.out.println(e.getMessage());
        }
    }
}
```

## <a name="recognize-emotions-sample-response"></a>辨識表情範例回應
成功呼叫將會傳回臉部項目的陣列，以及其相關聯的表情分數，依臉部矩形大小以遞減方式排序。 空的回應表示沒有偵測到任何臉部。 表情項目包含下列欄位：
* faceRectangle - 臉部於影像中的矩形位置。
* scores - 影像中每個臉部的表情分數。 

```json
application/json 
[
  {
    "faceRectangle": {
      "left": 68,
      "top": 97,
      "width": 64,
      "height": 97
    },
    "scores": {
      "anger": 0.00300731952,
      "contempt": 5.14648448E-08,
      "disgust": 9.180124E-06,
      "fear": 0.0001912825,
      "happiness": 0.9875571,
      "neutral": 0.0009861537,
      "sadness": 1.889955E-05,
      "surprise": 0.008229999
    }
  }
]
