---
title: 表情 API JavaScript 快速入門 | Microsoft Docs
description: 取得資訊和程式碼範例，以協助您搭配 JavaScript 快速開始使用認知服務中的表情 API。
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: emotion-api
ms.topic: article
ms.date: 05/23/2017
ms.author: anroth
ms.openlocfilehash: fb9cc2335582c4ec75ec45635e519346d65d7e08
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/17/2018
ms.locfileid: "39072087"
---
# <a name="emotion-api-javascript-quick-start"></a>表情 API JavaScript 快速入門

> [!IMPORTANT]
> 影片 API 預覽將於 2017 年 10 月 30 日結束。 請試用新的[影片索引器 API 預覽](https://azure.microsoft.com/services/cognitive-services/video-indexer/)，以輕鬆地從影片中擷取見解，並增強內容探索體驗，像是偵測話語、臉部、人物及表情而得來的搜尋結果。 [深入了解](https://docs.microsoft.com/azure/cognitive-services/video-indexer/video-indexer-overview)。

本文提供資訊和程式碼範例，以協助您搭配 JavaScript 快速開始使用[表情 API 辨識方法](https://westus.dev.cognitive.microsoft.com/docs/services/5639d931ca73072154c1ce89/operations/563b31ea778daf121cc3a5fa) \(英文\) 來辨識出影像中一或多個人員所表現出來的表情。

## <a name="prerequisite"></a>必要條件
* 請從[這裡](https://azure.microsoft.com/try/cognitive-services/)取得免費的訂用帳戶金鑰，或者如果您有 Azure 訂用帳戶，請先建立表情 API 資源，然後從該處取得訂用帳戶金鑰和端點。

![建立表情 API 資源](../Images/create-resource.png)

## <a name="recognize-emotions-javascript-example-request"></a>辨識表情 JavaScript 範例要求

複製下列內容並儲存至檔案，例如 `test.html`。 將要求 `url` 變更為使用您取得訂用帳戶金鑰的位置，並將 "Ocp-Apim-Subscription-Key" 值取代為您的有效訂用帳戶金鑰。 這些可以在 Azure 入口網站中表情 API 資源的 [概觀] 和 [金鑰] 區段中找到。 

![API 端點](../Images/api-url.png)

![API 訂用帳戶金鑰](../Images/keys.png)

將要求主體變更為您要使用影像的位置。 若要執行這個範例，請將該檔案拖曳到您的瀏覽器。

```html
<!DOCTYPE html>
<html>
<head>
    <title>JSSample</title>
    <script src="http://ajax.googleapis.com/ajax/libs/jquery/1.9.0/jquery.min.js"></script>
</head>

<h2>Face Rectangle</h2>
<ul id="faceRectangle">
<!-- Will populate list with response content -->
</ul>

<h2>Emotions</h2>
<ul id="scores">
<!-- Will populate list with response content -->
</ul>

<body>

<script type="text/javascript">
    $(function() {
        // No query string parameters for this API call.
        var params = { };
      
        $.ajax({
            // NOTE: You must use the same location in your REST call as you used to obtain your subscription keys.
            //   For example, if you obtained your subscription keys from westcentralus, replace "westus" in the 
            //   URL below with "westcentralus".
            url: "https://westus.api.cognitive.microsoft.com/emotion/v1.0/recognize?" + $.param(params),
            beforeSend: function(xhrObj){
                // Request headers, also supports "application/octet-stream"
                xhrObj.setRequestHeader("Content-Type","application/json");

                // NOTE: Replace the "Ocp-Apim-Subscription-Key" value with a valid subscription key.
                xhrObj.setRequestHeader("Ocp-Apim-Subscription-Key","<your subscription key>");
            },
            type: "POST",
            // Request body
            data: '{"url": "<your image url>"}',
        }).done(function(data) {
            // Get face rectangle dimensions
            var faceRectangle = data[0].faceRectangle;
            var faceRectangleList = $('#faceRectangle');

            // Append to DOM
            for (var prop in faceRectangle) {
                faceRectangleList.append("<li> " + prop + ": " + faceRectangle[prop] + "</li>");
            }
            
            // Get emotion confidence scores
            var scores = data[0].scores;
            var scoresList = $('#scores');

            // Append to DOM
            for(var prop in scores) {
                scoresList.append("<li> " + prop + ": " + scores[prop] + "</li>")
            }
        }).fail(function(err) {
            alert("Error: " + JSON.stringify(err));
        });
    });
</script>
</body>
</html>
```

## <a name="recognize-emotions-sample-response"></a>辨識表情範例回應
呼叫若成功，則會傳回一系列的臉部資料，以及其相關聯的表情分數，依臉部方框大小以遞減方式排序。 空的回應表示沒有偵測到任何臉部。 表情項目包含下列欄位：
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
