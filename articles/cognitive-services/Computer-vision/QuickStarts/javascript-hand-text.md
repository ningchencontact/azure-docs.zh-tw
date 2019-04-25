---
title: 快速入門：擷取手寫文字 - JavaScript
titleSuffix: Azure Cognitive Services
description: 在此快速入門中，您可以使用電腦視覺 API 搭配 JavaScript 擷取影像中的手寫文字。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 03/04/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 969f7dc585e7ca518936636bbdcd859747ff089b
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2019
ms.locfileid: "60007027"
---
# <a name="quickstart-extract-handwritten-text-using-the-rest-api-and-javascript-in-computer-vision"></a>快速入門：在電腦視覺中使用 REST API 與 JavaScript 擷取手寫文字

在此快速入門中，您將使用電腦視覺 API 的 REST API 來擷取影像中的手寫文字。 您可以使用[批次讀取](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/2afb498089f74080d7ef85eb) API 與[讀取作業結果](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/5be108e7498a4f9ed20bf96d) API，偵測影像中的手寫文字，然後將辨識出的字元擷取到電腦可使用的字元資料流中。

> [!IMPORTANT]
> 不同於 [OCR](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc) 方法，[批次讀取](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/2afb498089f74080d7ef85eb)方法會以非同步方式執行。 這個方法不會在成功回應的主體中傳回任何資訊。 「批次讀取」方法會改為在 `Operation-Content` 回應標頭欄位的值中傳回 URI。 您接著可以呼叫這個 URI (它代表[讀取作業結果](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/5be108e7498a4f9ed20bf96d)方法)，檢查狀態並傳回「辨識文字」方法呼叫的結果。

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/ai/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cognitive-services) 。

## <a name="prerequisites"></a>必要條件

您必須有電腦視覺的訂用帳戶金鑰。 您可以從[試用認知服務](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision)取得免費試用的金鑰。 或者，依照[建立認知服務帳戶](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)中的指示訂閱電腦視覺並取得金鑰。

## <a name="create-and-run-the-sample"></a>建立並執行範例

若要建立並執行範例，請執行下列步驟：

1. 將下列程式碼複製到文字編輯器。
1. 視需要在程式碼中進行下列變更：
    1. 將 `subscriptionKey` 的值取代為您的訂用帳戶金鑰。
    1. 如有需要，請從您取得訂用帳戶金鑰的 Azure 區域，將 `uriBase` 的值取代為[批次讀取](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/2afb498089f74080d7ef85eb)方法的端點 URL。
    1. (選擇性) 將 `inputImage` 控制項的 `value` 屬性值取代為您要從中擷取手寫文字之不同影像的 URL。
1. 將程式碼儲存為副檔名為 `.html` 的檔案。 例如： `get-handwriting.html`。
1. 開啟瀏覽器視窗。
1. 在瀏覽器中，將檔案拖放到瀏覽器視窗中。
1. 當瀏覽器中顯示該網頁時，選擇 [讀取影像] 按鈕。

```html
<!DOCTYPE html>
<html>
<head>
    <title>Handwriting Sample</title>
    <script src="https://ajax.googleapis.com/ajax/libs/jquery/1.9.0/jquery.min.js"></script>
</head>
<body>

<script type="text/javascript">
    function processImage() {
        // **********************************************
        // *** Update or verify the following values. ***
        // **********************************************

        // Replace <Subscription Key> with your valid subscription key.
        var subscriptionKey = "<Subscription Key>";

        // You must use the same Azure region in your REST API method as you used to
        // get your subscription keys. For example, if you got your subscription keys
        // from the West US region, replace "westcentralus" in the URL
        // below with "westus".
        //
        // Free trial subscription keys are generated in the "westus" region.
        // If you use a free trial subscription key, you shouldn't need to change
        // this region.
        var uriBase =
            "https://westus.api.cognitive.microsoft.com/vision/v2.0/read/core/asyncBatchAnalyze";

        // Request parameter.
        var params = {
            "mode": "Handwritten",
        };

        // Display the image.
        var sourceImageUrl = document.getElementById("inputImage").value;
        document.querySelector("#sourceImage").src = sourceImageUrl;

        // This operation requires two REST API calls. One to submit the image
        // for processing, the other to retrieve the text found in the image.
        //
        // Make the first REST API call to submit the image for processing.
        $.ajax({
            url: uriBase + "?" + $.param(params),

            // Request headers.
            beforeSend: function(jqXHR){
                jqXHR.setRequestHeader("Content-Type","application/json");
                jqXHR.setRequestHeader("Ocp-Apim-Subscription-Key", subscriptionKey);
            },

            type: "POST",

            // Request body.
            data: '{"url": ' + '"' + sourceImageUrl + '"}',
        })

        .done(function(data, textStatus, jqXHR) {
            // Show progress.
            $("#responseTextArea").val("Handwritten text submitted. " +
                "Waiting 10 seconds to retrieve the recognized text.");

            // Note: The response may not be immediately available. Handwriting
            // recognition is an asynchronous operation that can take a variable
            // amount of time depending on the length of the text you want to
            // recognize. You may need to wait or retry the GET operation.
            //
            // Wait ten seconds before making the second REST API call.
            setTimeout(function () {
                // "Operation-Location" in the response contains the URI
                // to retrieve the recognized text.
                var operationLocation = jqXHR.getResponseHeader("Operation-Location");

                // Make the second REST API call and get the response.
                $.ajax({
                    url: operationLocation,

                    // Request headers.
                    beforeSend: function(jqXHR){
                        jqXHR.setRequestHeader("Content-Type","application/json");
                        jqXHR.setRequestHeader(
                            "Ocp-Apim-Subscription-Key", subscriptionKey);
                    },

                    type: "GET",
                })

                .done(function(data) {
                    // Show formatted JSON on webpage.
                    $("#responseTextArea").val(JSON.stringify(data, null, 2));
                })

                .fail(function(jqXHR, textStatus, errorThrown) {
                    // Display error message.
                    var errorString = (errorThrown === "") ? "Error. " :
                        errorThrown + " (" + jqXHR.status + "): ";
                    errorString += (jqXHR.responseText === "") ? "" :
                        (jQuery.parseJSON(jqXHR.responseText).message) ?
                            jQuery.parseJSON(jqXHR.responseText).message :
                            jQuery.parseJSON(jqXHR.responseText).error.message;
                    alert(errorString);
                });
            }, 10000);
        })

        .fail(function(jqXHR, textStatus, errorThrown) {
            // Put the JSON description into the text area.
            $("#responseTextArea").val(JSON.stringify(jqXHR, null, 2));

            // Display error message.
            var errorString = (errorThrown === "") ? "Error. " :
                errorThrown + " (" + jqXHR.status + "): ";
            errorString += (jqXHR.responseText === "") ? "" :
                (jQuery.parseJSON(jqXHR.responseText).message) ?
                    jQuery.parseJSON(jqXHR.responseText).message :
                    jQuery.parseJSON(jqXHR.responseText).error.message;
            alert(errorString);
        });
    };
</script>
<h1>Read handwritten image:</h1>
Enter the URL to an image of handwritten text, then click
the <strong>Read image</strong> button.
<br><br>
Image to read:
<input type="text" name="inputImage" id="inputImage"
    value="https://upload.wikimedia.org/wikipedia/commons/thumb/d/dd/Cursive_Writing_on_Notebook_paper.jpg/800px-Cursive_Writing_on_Notebook_paper.jpg" />
<button onclick="processImage()">Read image</button>
<br><br>
<div id="wrapper" style="width:1020px; display:table;">
    <div id="jsonOutput" style="width:600px; display:table-cell;">
        Response:
        <br><br>
        <textarea id="responseTextArea" class="UIInput"
                  style="width:580px; height:400px;"></textarea>
    </div>
    <div id="imageDiv" style="width:420px; display:table-cell;">
        Source image:
        <br><br>
        <img id="sourceImage" width="400" />
    </div>
</div>
</body>
</html>
```

## <a name="examine-the-response"></a>檢查回應

成功的回應會以 JSON 的形式傳回。 範例網頁會在瀏覽器視窗中剖析並顯示成功的回應，如下列範例所示：

```json
{
  "status": "Succeeded",
  "recognitionResults": [
    {
      "page": 1,
      "clockwiseOrientation": 349.59,
      "width": 3200,
      "height": 3200,
      "unit": "pixel",
      "lines": [
        {
          "boundingBox": [202,618,2047,643,2046,840,200,813],
          "text": "Our greatest glory is not",
          "words": [
            {
              "boundingBox": [204,627,481,628,481,830,204,829],
              "text": "Our"
            },
            {
              "boundingBox": [519,628,1057,630,1057,832,518,830],
              "text": "greatest"
            },
            {
              "boundingBox": [1114,630,1549,631,1548,833,1114,832],
              "text": "glory"
            },
            {
              "boundingBox": [1586,631,1785,632,1784,834,1586,833],
              "text": "is"
            },
            {
              "boundingBox": [1822,632,2115,633,2115,835,1822,834],
              "text": "not"
            }
          ]
        },
        {
          "boundingBox": [420,1273,2954,1250,2958,1488,422,1511],
          "text": "but in rising every time we fall",
          "words": [
            {
              "boundingBox": [423,1269,634,1268,635,1507,424,1508],
              "text": "but"
            },
            {
              "boundingBox": [667,1268,808,1268,809,1506,668,1507],
              "text": "in"
            },
            {
              "boundingBox": [874,1267,1289,1265,1290,1504,875,1506],
              "text": "rising"
            },
            {
              "boundingBox": [1331,1265,1771,1263,1772,1502,1332,1504],
              "text": "every"
            },
            {
              "boundingBox": [1812, 1263, 2178, 1261, 2179, 1500, 1813, 1502],
              "text": "time"
            },
            {
              "boundingBox": [2219, 1261, 2510, 1260, 2511, 1498, 2220, 1500],
              "text": "we"
            },
            {
              "boundingBox": [2551, 1260, 3016, 1258, 3017, 1496, 2552, 1498],
              "text": "fall"
            }
          ]
        },
        {
          "boundingBox": [1612, 903, 2744, 935, 2738, 1139, 1607, 1107],
          "text": "in never failing ,",
          "words": [
            {
              "boundingBox": [1611, 934, 1707, 933, 1708, 1147, 1613, 1147],
              "text": "in"
            },
            {
              "boundingBox": [1753, 933, 2132, 930, 2133, 1144, 1754, 1146],
              "text": "never"
            },
            {
              "boundingBox": [2162, 930, 2673, 927, 2674, 1140, 2164, 1144],
              "text": "failing"
            },
            {
              "boundingBox": [2703, 926, 2788, 926, 2790, 1139, 2705, 1140],
              "text": ",",
              "confidence": "Low"
            }
          ]
        }
      ]
    }
  ]
}
```

## <a name="clean-up-resources"></a>清除資源

不再需要檔案時，請將它刪除。

## <a name="next-steps"></a>後續步驟

探索使用「電腦視覺」在影像中執行光學字元辨識 (OCR)、建立智慧型裁剪縮圖以及偵測、分類、標記和描述視覺特徵 (包括臉部) 的 JavaScript 應用程式。 若要快速地試驗電腦視覺 API，請嘗試 [Open API 測試主控台](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console)。

> [!div class="nextstepaction"]
> [電腦視覺 API JavaScript 教學課程](../Tutorials/javascript-tutorial.md)
