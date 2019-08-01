---
title: 執行影像作業 - JavaScript
titleSuffix: Azure Cognitive Services
description: 探索使用「Azure 認知服務」中「電腦視覺 API」的基本 JavaScript 應用程式。 在影像中執行 OCR、建立縮圖，以及處理視覺特徵。
services: cognitive-services
author: KellyDF
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/30/2019
ms.author: kefre
ms.custom: seodec18
ms.openlocfilehash: 24ef94b702d11977df4e1ca2dab181f5c14a00df
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/26/2019
ms.locfileid: "68564567"
---
# <a name="use-computer-vision-features-with-the-rest-api-and-javascript"></a>搭配 REST API 和 JavaScript 使用電腦視覺功能

本指南說明 Azure 認知服務電腦視覺 REST API 的功能。

探索使用「電腦視覺 REST API」在影像中執行光學字元辨識 (OCR)、建立智慧型裁剪縮圖以及偵測、分類、標記和描述視覺特徵 (包括臉部) 的 JavaScript 應用程式。 此範例可讓您提交影像 URL 以供分析或處理。 您可以使用此開放原始碼範例作為範本來建置自己的 JavaScript 應用程式，以使用「電腦視覺 REST API」。

已經撰寫 JavaScript 表單應用程式，但還沒有任何「電腦視覺」功能。 在本指南中, 您會新增電腦視覺 REST API 的特定程式碼, 以完成應用程式的功能。

## <a name="prerequisites"></a>先決條件

### <a name="platform-requirements"></a>平台需求

您可以依照本指南中的步驟使用簡單的文字編輯器。

### <a name="subscribe-to-computer-vision-api-and-get-a-subscription-key"></a>訂閱電腦視覺 API 並取得訂用帳戶金鑰

建立範例之前，您必須先訂閱「電腦視覺 API」，這是「Azure 認知服務」的組件。 如需訂用帳戶和金鑰管理詳細資料，請參閱[訂用帳戶](https://azure.microsoft.com/try/cognitive-services/)。 主要和次要金鑰都是有效的, 可在本指南中使用。

## <a name="acquire-incomplete-tutorial-project"></a>取得不完整的教學課程專案

### <a name="download-the-project"></a>下載專案

複製[認知服務 JavaScript 電腦視覺教學課程](https://github.com/Azure-Samples/cognitive-services-javascript-computer-vision-tutorial)，或下載 .zip 檔案，然後將它解壓縮至空白目錄。

如果您想要使用已完成的專案, 並新增所有教學課程程式碼, 您可以使用 [**已完成**] 資料夾中的檔案。

## <a name="add-tutorial-code-to-the-project"></a>將教學課程程式碼加入至專案

JavaScript 應用程式設定後會有六個 .html 檔案，每項功能各有一個檔案。 每個檔案都會示範不同的電腦視覺功能 (分析、OCR 等)。 六個區段沒有相互相關性, 因此您可以將教學課程程式碼新增到一個檔案、全部六個檔案, 或只是幾個檔案。 並且可以將教學課程程式碼以任何順序新增到檔案中。

### <a name="analyze-an-image"></a>分析影像

電腦視覺的分析功能會掃描影像中是否有數千個可辨識的物件、事物、景象和動作。 在分析完成之後，「分析」就會傳回一個 JSON 物件，其中會以描述性標籤、色彩分析、標題等來描述影像。

若要完成應用程式的 [分析] 功能, 請執行下列步驟:

#### <a name="add-the-event-handler-code-for-the-analyze-button"></a>新增 [分析] 按鈕的事件處理常式程式碼

在文字編輯器中開啟 **analyze.html** 檔案，然後找出靠近檔案底部的 **analyzeButtonClick** 函式。

**analyzeButtonClick** 事件處理常式函式會清除表單、顯示 URL 中指定的影像，然後呼叫 **AnalyzeImage** 函式來分析影像。 將以下程式碼複製並貼到 **analyzeButtonClick** 函式中。

```javascript
function analyzeButtonClick() {

    // Clear the display fields.
    $("#sourceImage").attr("src", "#");
    $("#responseTextArea").val("");
    $("#captionSpan").text("");
    
    // Display the image.
    var sourceImageUrl = $("#inputImage").val();
    $("#sourceImage").attr("src", sourceImageUrl);
    
    AnalyzeImage(sourceImageUrl, $("#responseTextArea"), $("#captionSpan"));
}
```

#### <a name="add-the-wrapper-for-the-rest-api-call"></a>新增 REST API 呼叫的包裝函式

**AnalyzeImage** 函式會包裝 REST API 呼叫來分析影像。 成功傳回時，已格式化的 JSON 分析將會顯示在指定的文字區域中，標題則會顯示在指定的範圍中。

將 **AnalyzeImage** 函式程式碼複製並貼到緊接在 **analyzeButtonClick** 函式的下方。

```javascript
/* Analyze the image at the specified URL by using Microsoft Cognitive Services Analyze Image API.
 * @param {string} sourceImageUrl - The URL to the image to analyze.
 * @param {<textarea> element} responseTextArea - The text area to display the JSON string returned
 *                             from the REST API call, or to display the error message if there was 
 *                             an error.
 * @param {<span> element} captionSpan - The span to display the image caption.
 */
function AnalyzeImage(sourceImageUrl, responseTextArea, captionSpan) {
    // Request parameters.
    var params = {
        "visualFeatures": "Categories,Description,Color",
        "details": "",
        "language": "en",
    };
    
    // Perform the REST API call.
    $.ajax({
        url: common.uriBasePreRegion + 
             $("#subscriptionRegionSelect").val() + 
             common.uriBasePostRegion + 
             common.uriBaseAnalyze +
             "?" + 
             $.param(params),
                    
        // Request headers.
        beforeSend: function(jqXHR){
            jqXHR.setRequestHeader("Content-Type","application/json");
            jqXHR.setRequestHeader("Ocp-Apim-Subscription-Key", 
                encodeURIComponent($("#subscriptionKeyInput").val()));
        },
        
        type: "POST",
        
        // Request body.
        data: '{"url": ' + '"' + sourceImageUrl + '"}',
    })
    
    .done(function(data) {
        // Show formatted JSON on webpage.
        responseTextArea.val(JSON.stringify(data, null, 2));
        
        // Extract and display the caption and confidence from the first caption in the description object.
        if (data.description && data.description.captions) {
            var caption = data.description.captions[0];
            
            if (caption.text && caption.confidence) {
                captionSpan.text("Caption: " + caption.text +
                    " (confidence: " + caption.confidence + ").");
            }
        }
    })
    
    .fail(function(jqXHR, textStatus, errorThrown) {
        // Prepare the error string.
        var errorString = (errorThrown === "") ? "Error. " : errorThrown + " (" + jqXHR.status + "): ";
        errorString += (jqXHR.responseText === "") ? "" : (jQuery.parseJSON(jqXHR.responseText).message) ? 
            jQuery.parseJSON(jqXHR.responseText).message : jQuery.parseJSON(jqXHR.responseText).error.message;
        
        // Put the error JSON in the response textarea.
        responseTextArea.val(JSON.stringify(jqXHR, null, 2));
        
        // Show the error message.
        alert(errorString);
    });
}
```

#### <a name="run-the-analyze-function"></a>執行分析函式

儲存 **analyze.html** 檔案，然後在網頁瀏覽器中將其開啟。 將您的訂用帳戶金鑰放入 [訂用帳戶金鑰]  欄位中，然後確認在 [訂用帳戶區域]  中使用的區域正確。 輸入要分析之影像的 URL，然後按一下 [分析影像]  按鈕來分析影像並查看結果。

### <a name="recognize-a-landmark"></a>辨識地標

「電腦視覺」的「地標」功能可分析影像中的自然和人工地標，例如山地或知名的建築物。 在分析完成之後，「地標」就會傳回一個 JSON 物件，其中會識別在影像中找到的地標。

若要完成應用程式的地標功能, 請執行下列步驟:

#### <a name="add-the-event-handler-code-for-the-landmark-button"></a>新增 [地標] 按鈕的事件處理常式程式碼

在文字編輯器中開啟 **landmark.html** 檔案，然後找出靠近檔案底部的 **landmarkButtonClick** 函式。

**landmarkButtonClick** 事件處理常式函式會清除表單、顯示 URL 中指定的影像，然後呼叫 **IdentifyLandmarks** 函式來分析影像。 將以下程式碼複製並貼到 **landmarkButtonClick** 函式中。

```javascript
function landmarkButtonClick() {

    // Clear the display fields.
    $("#sourceImage").attr("src", "#");
    $("#responseTextArea").val("");
    $("#captionSpan").text("");
    
    // Display the image.
    var sourceImageUrl = $("#inputImage").val();
    $("#sourceImage").attr("src", sourceImageUrl);
    
    IdentifyLandmarks(sourceImageUrl, $("#responseTextArea"), $("#captionSpan"));
}
```

#### <a name="add-the-wrapper-for-the-rest-api-call"></a>新增 REST API 呼叫的包裝函式

**IdentifyLandmarks** 函式會包裝 REST API 呼叫來分析影像。 成功傳回時，已格式化的 JSON 分析將會顯示在指定的文字區域中，標題則會顯示在指定的範圍中。

將 **IdentifyLandmarks** 函式程式碼複製並貼到緊接在 **landmarkButtonClick** 函式的下方。

```javascript
/* Identify landmarks in the image at the specified URL by using Microsoft Cognitive Services 
 * Landmarks API.
 * @param {string} sourceImageUrl - The URL to the image to analyze for landmarks.
 * @param {<textarea> element} responseTextArea - The text area to display the JSON string returned
 *                             from the REST API call, or to display the error message if there was 
 *                             an error.
 * @param {<span> element} captionSpan - The span to display the image caption.
 */
function IdentifyLandmarks(sourceImageUrl, responseTextArea, captionSpan) {
    // Request parameters.
    var params = {
        "model": "landmarks"
    };
    
    // Perform the REST API call.
    $.ajax({
        url: common.uriBasePreRegion + 
             $("#subscriptionRegionSelect").val() + 
             common.uriBasePostRegion + 
             common.uriBaseLandmark +
             "?" + 
             $.param(params),
                    
        // Request headers.
        beforeSend: function(jqXHR){
            jqXHR.setRequestHeader("Content-Type","application/json");
            jqXHR.setRequestHeader("Ocp-Apim-Subscription-Key", 
                encodeURIComponent($("#subscriptionKeyInput").val()));
        },
        
        type: "POST",
        
        // Request body.
        data: '{"url": ' + '"' + sourceImageUrl + '"}',
    })
    
    .done(function(data) {
        // Show formatted JSON on webpage.
        responseTextArea.val(JSON.stringify(data, null, 2));
        
        // Extract and display the caption and confidence from the first caption in the description object.
        if (data.result && data.result.landmarks) {
            var landmark = data.result.landmarks[0];
            
            if (landmark.name && landmark.confidence) {
                captionSpan.text("Landmark: " + landmark.name +
                    " (confidence: " + landmark.confidence + ").");
            }
        }
    })
    
    .fail(function(jqXHR, textStatus, errorThrown) {
        // Prepare the error string.
        var errorString = (errorThrown === "") ? "Error. " : errorThrown + " (" + jqXHR.status + "): ";
        errorString += (jqXHR.responseText === "") ? "" : (jQuery.parseJSON(jqXHR.responseText).message) ? 
            jQuery.parseJSON(jqXHR.responseText).message : jQuery.parseJSON(jqXHR.responseText).error.message;
        
        // Put the error JSON in the response textarea.
        responseTextArea.val(JSON.stringify(jqXHR, null, 2));
        
        // Show the error message.
        alert(errorString);
    });
}
```

#### <a name="run-the-landmark-function"></a>執行地標函式

儲存 **landmark.html** 檔案，然後在網頁瀏覽器中將其開啟。 將您的訂用帳戶金鑰放入 [訂用帳戶金鑰]  欄位中，然後確認在 [訂用帳戶區域]  中使用的區域正確。 輸入要分析之影像的 URL，然後按一下 [分析影像]  按鈕來分析影像並查看結果。

### <a name="recognize-celebrities"></a>辨識名人

「電腦視覺」的「名人」功能可分析影像中的知名人物。 在分析完成之後，「名人」就會傳回一個 JSON 物件，其中會識別在影像中找到的名人。

若要完成應用程式的名人功能, 請執行下列步驟:

#### <a name="add-the-event-handler-code-for-the-celebrities-button"></a>新增 [名人] 按鈕的事件處理常式程式碼

在文字編輯器中開啟 **celebrities.html** 檔案，然後找出靠近檔案底部的 **celebritiesButtonClick** 函式。

**celebritiesButtonClick** 事件處理常式函式會清除表單、顯示 URL 中指定的影像，然後呼叫 **IdentifyCelebrities** 函式來分析影像。 將以下程式碼複製並貼到 **celebritiesButtonClick** 函式中。

```javascript
function celebritiesButtonClick() {

    // Clear the display fields.
    $("#sourceImage").attr("src", "#");
    $("#responseTextArea").val("");
    $("#captionSpan").text("");
    
    // Display the image.
    var sourceImageUrl = $("#inputImage").val();
    $("#sourceImage").attr("src", sourceImageUrl);
    
    IdentifyCelebrities(sourceImageUrl, $("#responseTextArea"), $("#captionSpan"));
}
```

#### <a name="add-the-wrapper-for-the-rest-api-call"></a>新增 REST API 呼叫的包裝函式

```javascript
/* Identify celebrities in the image at the specified URL by using Microsoft Cognitive Services 
 * Celebrities API.
 * @param {string} sourceImageUrl - The URL to the image to analyze for celebrities.
 * @param {<textarea> element} responseTextArea - The text area to display the JSON string returned
 *                             from the REST API call, or to display the error message if there was 
 *                             an error.
 * @param {<span> element} captionSpan - The span to display the image caption.
 */
function IdentifyCelebrities(sourceImageUrl, responseTextArea, captionSpan) {
    // Request parameters.
    var params = {
        "model": "celebrities"
    };
    
    // Perform the REST API call.
    $.ajax({
        url: common.uriBasePreRegion + 
             $("#subscriptionRegionSelect").val() + 
             common.uriBasePostRegion + 
             common.uriBaseCelebrities +
             "?" + 
             $.param(params),
                    
        // Request headers.
        beforeSend: function(jqXHR){
            jqXHR.setRequestHeader("Content-Type","application/json");
            jqXHR.setRequestHeader("Ocp-Apim-Subscription-Key", 
                encodeURIComponent($("#subscriptionKeyInput").val()));
        },
        
        type: "POST",
        
        // Request body.
        data: '{"url": ' + '"' + sourceImageUrl + '"}',
    })
    
    .done(function(data) {
        // Show formatted JSON on webpage.
        responseTextArea.val(JSON.stringify(data, null, 2));
        
        // Extract and display the caption and confidence from the first caption in the description object.
        if (data.result && data.result.celebrities) {
            var celebrity = data.result.celebrities[0];
            
            if (celebrity.name && celebrity.confidence) {
                captionSpan.text("Celebrity name: " + celebrity.name +
                    " (confidence: " + celebrity.confidence + ").");
            }
        }
    })
    
    .fail(function(jqXHR, textStatus, errorThrown) {
        // Prepare the error string.
        var errorString = (errorThrown === "") ? "Error. " : errorThrown + " (" + jqXHR.status + "): ";
        errorString += (jqXHR.responseText === "") ? "" : (jQuery.parseJSON(jqXHR.responseText).message) ? 
            jQuery.parseJSON(jqXHR.responseText).message : jQuery.parseJSON(jqXHR.responseText).error.message;
        
        // Put the error JSON in the response textarea.
        responseTextArea.val(JSON.stringify(jqXHR, null, 2));
        
        // Show the error message.
        alert(errorString);
    });
}
```

#### <a name="run-the-celebrities-function"></a>執行名人函式

儲存 **celebrities.html** 檔案，然後在網頁瀏覽器中將其開啟。 將您的訂用帳戶金鑰放入 [訂用帳戶金鑰]  欄位中，然後確認在 [訂用帳戶區域]  中使用的區域正確。 輸入要分析之影像的 URL，然後按一下 [分析影像]  按鈕來分析影像並查看結果。

### <a name="intelligently-generate-a-thumbnail"></a>以智慧方式產生縮圖

「電腦視覺」的「縮圖」功能可從影像產生縮圖。 藉由使用「智慧型裁剪」  功能，「縮圖」功能將可識別影像中的關注區並將縮圖集中在此區域上，以產生更賞心悅目的縮圖影像。

若要完成應用程式的縮圖功能, 請執行下列步驟:

#### <a name="add-the-event-handler-code-for-the-thumbnail-button"></a>新增縮圖按鈕的事件處理常式程式碼

在文字編輯器中開啟 **thumbnail.html** 檔案，然後找出靠近檔案底部的 **thumbnailButtonClick** 函式。

**thumbnailButtonClick** 事件處理常式函式會清除表單、顯示 URL 中指定的影像，然後呼叫 **getThumbnail**函式兩次來建立兩個縮圖，一個經智慧型裁剪，另一個未經智慧型裁剪。 將以下程式碼複製並貼到 **thumbnailButtonClick** 函式中。

```javascript
function thumbnailButtonClick() {

    // Clear the display fields.
    document.getElementById("sourceImage").src = "#";
    document.getElementById("thumbnailImageSmartCrop").src = "#";
    document.getElementById("thumbnailImageNonSmartCrop").src = "#";
    document.getElementById("responseTextArea").value = "";
    document.getElementById("captionSpan").text = "";
    
    // Display the image.
    var sourceImageUrl = document.getElementById("inputImage").value;
    document.getElementById("sourceImage").src = sourceImageUrl;
    
    // Get a smart cropped thumbnail.
    getThumbnail (sourceImageUrl, true, document.getElementById("thumbnailImageSmartCrop"), 
        document.getElementById("responseTextArea"));
    
    // Get a non-smart-cropped thumbnail.
    getThumbnail (sourceImageUrl, false, document.getElementById("thumbnailImageNonSmartCrop"),
        document.getElementById("responseTextArea"));
}
```

#### <a name="add-the-wrapper-for-the-rest-api-call"></a>新增 REST API 呼叫的包裝函式

**getThumbnail** 函式會包裝 REST API 呼叫來分析影像。 成功傳回時，縮圖將會顯示在指定的 img 元素中。

將以下 **getThumbnail** 函式複製並貼到緊接在 **thumbnailButtonClick** 函式的下方。

```javascript
/* Get a thumbnail of the image at the specified URL by using Microsoft Cognitive Services
 * Thumbnail API.
 * @param {string} sourceImageUrl URL to image.
 * @param {boolean} smartCropping Set to true to use the smart cropping feature which crops to the
 *                                more interesting area of an image; false to crop for the center
 *                                of the image.
 * @param {<img> element} imageElement The img element in the DOM which will display the thumbnail image.
 * @param {<textarea> element} responseTextArea - The text area to display the Response Headers returned
 *                             from the REST API call, or to display the error message if there was 
 *                             an error.
 */
function getThumbnail (sourceImageUrl, smartCropping, imageElement, responseTextArea) {
    // Create the HTTP Request object.
    var xhr = new XMLHttpRequest();
    
    // Request parameters.
    var params = "width=100&height=150&smartCropping=" + smartCropping.toString();

    // Build the full URI.
    var fullUri = common.uriBasePreRegion + 
                  document.getElementById("subscriptionRegionSelect").value + 
                  common.uriBasePostRegion + 
                  common.uriBaseThumbnail +
                  "?" + 
                  params;
    
    // Identify the request as a POST, with the URI and parameters.
    xhr.open("POST", fullUri);
    
    // Add the request headers.
    xhr.setRequestHeader("Content-Type","application/json");
    xhr.setRequestHeader("Ocp-Apim-Subscription-Key", 
        encodeURIComponent(document.getElementById("subscriptionKeyInput").value));
    
    // Set the response type to "blob" for the thumbnail image data.
    xhr.responseType = "blob";
    
    // Process the result of the REST API call.
    xhr.onreadystatechange = function(e) {
        if(xhr.readyState === XMLHttpRequest.DONE) {
            
            // Thumbnail successfully created.
            if (xhr.status === 200) {
                // Show response headers.
                var s = JSON.stringify(xhr.getAllResponseHeaders(), null, 2);
                responseTextArea.value = JSON.stringify(xhr.getAllResponseHeaders(), null, 2);
                
                // Show thumbnail image.
                var urlCreator = window.URL || window.webkitURL;
                var imageUrl = urlCreator.createObjectURL(this.response);
                imageElement.src = imageUrl;
            } else {
                // Display the error message. The error message is the response body as a JSON string. 
                // The code in this code block extracts the JSON string from the blob response.
                var reader = new FileReader();
                
                // This event fires after the blob has been read.
                reader.addEventListener('loadend', (e) => {
                    responseTextArea.value = JSON.stringify(JSON.parse(e.srcElement.result), null, 2);
                });
                
                // Start reading the blob as text.
                reader.readAsText(xhr.response);
            }
        }
    }
    
    // Execute the REST API call.
    xhr.send('{"url": ' + '"' + sourceImageUrl + '"}');
}
```

#### <a name="run-the-thumbnail-function"></a>執行縮圖函數

儲存 **thumbnail.html** 檔案，然後在網頁瀏覽器中將其開啟。 將您的訂用帳戶金鑰放入 [訂用帳戶金鑰]  欄位中，然後確認在 [訂用帳戶區域]  中使用的區域正確。 輸入要分析之影像的 URL，然後按一下 [產生縮圖]  按鈕來分析影像並查看結果。

### <a name="read-printed-text-ocr"></a>讀取列印文字 (OCR)

「電腦視覺」的「光學字元辨識」(OCR) 功能可分析列印文字的影像。 在分析完成之後，OCR 就會傳回一個 JSON 物件，其中包含影像中的文字和文字位置。

若要完成應用程式的 OCR 功能, 請執行下列步驟:

### <a name="add-the-event-handler-code-for-the-ocr-button"></a>新增 OCR 按鈕的事件處理常式程式碼

在文字編輯器中開啟 **ocr.html** 檔案，然後找出靠近檔案底部的 **ocrButtonClick** 函式。

**ocrButtonClick** 事件處理常式函式會清除表單、顯示 URL 中指定的影像，然後呼叫 **ReadOcrImage** 函式來分析影像。 將以下程式碼複製並貼到 **ocrButtonClick**函式中。

```javascript
function ocrButtonClick() {

    // Clear the display fields.
    $("#sourceImage").attr("src", "#");
    $("#responseTextArea").val("");
    $("#captionSpan").text("");
    
    // Display the image.
    var sourceImageUrl = $("#inputImage").val();
    $("#sourceImage").attr("src", sourceImageUrl);
    
    ReadOcrImage(sourceImageUrl, $("#responseTextArea"));
}
```

#### <a name="add-the-wrapper-for-the-rest-api-call"></a>新增 REST API 呼叫的包裝函式

**ReadOcrImage** 函式會包裝 REST API 呼叫來分析影像。 成功傳回時，描述文字和文字位置的已格式化 JSON 將會顯示在指定的文字區域中。

將以下 **ReadOcrImage** 函式複製並貼到緊接在 **ocrButtonClick** 函式的下方。

```javascript
/* Recognize and read printed text in an image at the specified URL by using Microsoft Cognitive 
 * Services OCR API.
 * @param {string} sourceImageUrl - The URL to the image to analyze for printed text.
 * @param {<textarea> element} responseTextArea - The text area to display the JSON string returned
 *                             from the REST API call, or to display the error message if there was 
 *                             an error.
 */
function ReadOcrImage(sourceImageUrl, responseTextArea) {
    // Request parameters.
    var params = {
        "language": "unk",
        "detectOrientation ": "true",
    };

    // Perform the REST API call.
    $.ajax({
        url: common.uriBasePreRegion + 
             $("#subscriptionRegionSelect").val() + 
             common.uriBasePostRegion + 
             common.uriBaseOcr +
             "?" + 
             $.param(params),
        
        // Request headers.
        beforeSend: function(jqXHR){
            jqXHR.setRequestHeader("Content-Type","application/json");
            jqXHR.setRequestHeader("Ocp-Apim-Subscription-Key", 
                encodeURIComponent($("#subscriptionKeyInput").val()));
        },
        
        type: "POST",
        
        // Request body.
        data: '{"url": ' + '"' + sourceImageUrl + '"}',
    })
    
    .done(function(data) {
        // Show formatted JSON on webpage.
        responseTextArea.val(JSON.stringify(data, null, 2));
    })
    
    .fail(function(jqXHR, textStatus, errorThrown) {
        // Put the JSON description into the text area.
        responseTextArea.val(JSON.stringify(jqXHR, null, 2));
        
        // Display error message.
        var errorString = (errorThrown === "") ? "Error. " : errorThrown + " (" + jqXHR.status + "): ";
        errorString += (jqXHR.responseText === "") ? "" : (jQuery.parseJSON(jqXHR.responseText).message) ? 
            jQuery.parseJSON(jqXHR.responseText).message : jQuery.parseJSON(jqXHR.responseText).error.message;
        alert(errorString);
    });
}
```

#### <a name="run-the-ocr-function"></a>執行 OCR 函式

儲存 **ocr.html** 檔案，然後在網頁瀏覽器中將其開啟。 將您的訂用帳戶金鑰放入 [訂用帳戶金鑰]  欄位中，然後確認在 [訂用帳戶區域]  中使用的區域正確。 輸入要讀取之文字影像的 URL，然後按一下 [讀取影像]  按鈕來分析影像並查看結果。

### <a name="read-handwritten-text-handwriting-recognition"></a>讀取手寫文字 (手寫辨識)

「電腦視覺」的「手寫辨識」功能可分析手寫文字的影像。 在分析完成之後，「手寫辨識」就會傳回一個 JSON 物件，其中包含影像中的文字和文字位置。

若要完成應用程式的「手寫辨識」功能, 請執行下列步驟:

#### <a name="add-the-event-handler-code-for-the-handwriting-button"></a>新增手寫按鈕的事件處理常式程式碼

在文字編輯器中開啟 **handwriting.html** 檔案，然後找出靠近檔案底部的 **handwritingButtonClick** 函式。

**handwritingButtonClick** 事件處理常式函式會清除表單、顯示 URL 中指定的影像，然後呼叫 **HandwritingImage** 函式來分析影像。

將以下程式碼複製並貼到 **handwritingButtonClick** 函式中。

```javascript
function handwritingButtonClick() {

    // Clear the display fields.
    $("#sourceImage").attr("src", "#");
    $("#responseTextArea").val("");
    
    // Display the image.
    var sourceImageUrl = $("#inputImage").val();
    $("#sourceImage").attr("src", sourceImageUrl);
    
    ReadHandwrittenImage(sourceImageUrl, $("#responseTextArea"));
}
```

#### <a name="add-the-wrapper-for-the-rest-api-call"></a>新增 REST API 呼叫的包裝函式

**ReadHandwrittenImage** 函式會包裝 REST API 呼叫來分析影像。 由於「手寫辨識」是一個費時的程序，因此會使用一個雙步驟程序。 第一個呼叫會提交要處理的影像；第二個呼叫會在處理完成時，擷取所偵測到的文字。

擷取到文字之後，描述文字和文字位置的已格式化 JSON 將會顯示在指定的文字區域中。

將以下 **ReadHandwrittenImage** 函式複製並貼到緊接在 **handwritingButtonClick** 函式的下方。

```javascript
/* Recognize and read text from an image of handwriting at the specified URL by using Microsoft 
 * Cognitive Services Recognize Handwritten Text API.
 * @param {string} sourceImageUrl - The URL to the image to analyze for handwriting.
 * @param {<textarea> element} responseTextArea - The text area to display the JSON string returned
 *                             from the REST API call, or to display the error message if there was 
 *                             an error.
 */
function ReadHandwrittenImage(sourceImageUrl, responseTextArea) {
    // Request parameters.
    var params = {
        "handwriting": "true",
    };

    // This operation requires two REST API calls. One to submit the image for processing,
    // the other to retrieve the text found in the image. 
    //
    // Perform the first REST API call to submit the image for processing.
    $.ajax({
        url: common.uriBasePreRegion + 
             $("#subscriptionRegionSelect").val() + 
             common.uriBasePostRegion + 
             common.uriBaseHandwriting +
             "?" + 
             $.param(params),
        
        // Request headers.
        beforeSend: function(jqXHR){
            jqXHR.setRequestHeader("Content-Type","application/json");
            jqXHR.setRequestHeader("Ocp-Apim-Subscription-Key", 
                encodeURIComponent($("#subscriptionKeyInput").val()));
        },
        
        type: "POST",
        
        // Request body.
        data: '{"url": ' + '"' + sourceImageUrl + '"}',
    })
    
    .done(function(data, textStatus, jqXHR) {
        // Show progress.
        responseTextArea.val("Handwritten image submitted.");
        
        // Note: The response may not be immediately available. Handwriting Recognition is an
        // async operation that can take a variable amount of time depending on the length
        // of the text you want to recognize. You may need to wait or retry this GET operation.
        //
        // Try once per second for up to ten seconds to receive the result.
        var tries = 10;
        var waitTime = 100;
        var taskCompleted = false;
        
        var timeoutID = setInterval(function () { 
            // Limit the number of calls.
            if (--tries <= 0) {
                window.clearTimeout(timeoutID);
                responseTextArea.val("The response was not available in the time allowed.");
                return;
            }

            // The "Operation-Location" in the response contains the URI to retrieve the recognized text.
            var operationLocation = jqXHR.getResponseHeader("Operation-Location");
            
            // Perform the second REST API call and get the response.
            $.ajax({
                url: operationLocation,
                
                // Request headers.
                beforeSend: function(jqXHR){
                    jqXHR.setRequestHeader("Content-Type","application/json");
                    jqXHR.setRequestHeader("Ocp-Apim-Subscription-Key",
                        encodeURIComponent($("#subscriptionKeyInput").val()));
                },
                
                type: "GET",
            })
            
            .done(function(data) {
                // If the result is not yet available, return.
                if (data.status && (data.status === "NotStarted" || data.status === "Running")) {
                    return;
                }
                
                // Show formatted JSON on webpage.
                responseTextArea.val(JSON.stringify(data, null, 2));
                
                // Indicate the task is complete and clear the timer.
                taskCompleted = true;
                window.clearTimeout(timeoutID);
            })
            
            .fail(function(jqXHR, textStatus, errorThrown) {
                // Indicate the task is complete and clear the timer.
                taskCompleted = true;
                window.clearTimeout(timeoutID);
                
                // Display error message.
                var errorString = (errorThrown === "") ? "Error. " : errorThrown + " (" + jqXHR.status + "): ";
                errorString += (jqXHR.responseText === "") ? "" : (jQuery.parseJSON(jqXHR.responseText).message) ? 
                    jQuery.parseJSON(jqXHR.responseText).message : jQuery.parseJSON(jqXHR.responseText).error.message;
                alert(errorString);
            });
        }, waitTime);
    })
    
    .fail(function(jqXHR, textStatus, errorThrown) {
        // Put the JSON description into the text area.
        responseTextArea.val(JSON.stringify(jqXHR, null, 2));
        
        // Display error message.
        var errorString = (errorThrown === "") ? "Error. " : errorThrown + " (" + jqXHR.status + "): ";
        errorString += (jqXHR.responseText === "") ? "" : (jQuery.parseJSON(jqXHR.responseText).message) ? 
            jQuery.parseJSON(jqXHR.responseText).message : jQuery.parseJSON(jqXHR.responseText).error.message;
        alert(errorString);
    });
}
```

#### <a name="run-the-handwriting-function"></a>執行手寫功能

儲存 **handwriting.html** 檔案，然後在網頁瀏覽器中將其開啟。 將您的訂用帳戶金鑰放入 [訂用帳戶金鑰]  欄位中，然後確認在 [訂用帳戶區域]  中使用的區域正確。 輸入要讀取之文字影像的 URL，然後按一下 [讀取影像]  按鈕來分析影像並查看結果。

## <a name="next-steps"></a>後續步驟

在本指南中, 您使用了電腦視覺 REST API 與 JavaScript 來測試許多可用的影像分析功能。 接下來, 請參閱參考檔, 以深入瞭解相關的 Api。

- [電腦視覺 REST API](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa)
