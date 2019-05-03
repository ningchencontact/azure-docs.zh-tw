---
title: 快速入門：辨識筆墨辨識器 REST API 與 Node.js 的數位筆跡
description: 您可以使用手寫辨識器 API 來啟動 體認到數位筆墨筆劃。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: article
ms.date: 05/02/2019
ms.author: aahi
ms.openlocfilehash: febc6e72ed40541a230c606a3ec96a8bffef5036
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/02/2019
ms.locfileid: "65026798"
---
# <a name="quickstart-recognize-digital-ink-with-the-ink-recognizer-rest-api-and-javascript"></a>快速入門：辨識與筆跡辨識器 REST API 和 JavaScript 的數位筆跡

使用本快速入門中，若要開始使用數位筆墨筆劃上的筆墨辨識器 API。 此 JavaScript 應用程式傳送 API 要求，其中包含 JSON 格式的筆墨筆劃資料，並顯示回應。

雖然此應用程式以 Javascript 撰寫，並在網頁瀏覽器中執行，API 是 RESTful web 服務相容於大部分的程式設計語言。

通常，您會從數位筆跡應用程式呼叫 API。 本快速入門會傳送下列的手寫範例的筆墨筆劃資料，從 JSON 檔案。

![映像的手寫文字](../media/handwriting-sample.jpg)

此快速入門的原始程式碼可以在 [GitHub](https://go.microsoft.com/fwlink/?linkid=2089502) 上找到。

## <a name="prerequisites"></a>必要條件

- 網頁瀏覽器
- 在本快速入門範例筆墨筆劃資料可於[GitHub](https://go.microsoft.com/fwlink/?linkid=2089502)。


[!INCLUDE [cognitive-services-ink-recognizer-signup-requirements](../../../../includes/cognitive-services-ink-recognizer-signup-requirements.md)]

## <a name="create-a-new-application"></a>建立新的應用程式

1. 在您最愛的 IDE 或編輯器，建立新`.html`檔案。 我們稍後會加入程式碼，則加入基本的 HTML。
    
    ```html
    <!DOCTYPE html>
    <html>
    
        <head>
            <script type="text/javascript">
            </script>
        </head>
        
        <body>
        </body>
    
    </html>
    ```

2. 內`<body>`標記中，新增下列 html:
    1. 顯示 JSON 要求和回應的兩個文字區域。
    2. 按鈕，以呼叫`recognizeInk()`將稍後建立的函式。
    
    ```HTML
    <!-- <body>-->
        <h2>Send a request to the Ink Recognition API</h2>
        <p>Request:</p>
        <textarea id="request" style="width:800px;height:300px"></textarea>
        <p>Response:</p>
        <textarea id="response" style="width:800px;height:300px"></textarea>
        <br>
        <button type="button" onclick="recognizeInk()">Recognize Ink</button>
    <!--</body>-->
    ```

## <a name="load-the-example-json-data"></a>載入範例 JSON 資料

1. 內`<script>`標記中，建立 sampleJson 的變數。 然後建立名為的 JavaScript 函式`openFile()`，便會開啟檔案總管 中，因此您可以選取您的 JSON 檔案。 當`Recognize ink`按一下按鈕時，它將會呼叫此函式，並開始讀取檔案。
2. 使用`FileReader`物件的`onload()`函式，以非同步方式處理檔案。 
    1. 取代任何`\n`或`\r`空字串與檔案中的字元。 
    2. 使用`JSON.parse()`將文字轉換成有效的 JSON
    3. 更新`request`應用程式中的文字方塊。 使用`JSON.stringify()`格式化 JSON 字串。 
    
    ```javascript
    var sampleJson = "";
    function openFile(event) {
        var input = event.target;
    
        var reader = new FileReader();
        reader.onload = function(){
            sampleJson = reader.result.replace(/(\\r\\n|\\n|\\r)/gm, "");
            sampleJson = JSON.parse(sampleJson);
            document.getElementById('request').innerHTML = JSON.stringify(sampleJson, null, 2);
        };
        reader.readAsText(input.files[0]);
    };
    ```

## <a name="send-a-request-to-the-ink-recognizer-api"></a>傳送要求至筆墨辨識器 API

1. 內`<script>`標記中建立函式呼叫`recognizeInk()`。 此函式稍後將會呼叫 API，並以回應更新頁面。 從下列步驟，此函式中加入程式碼。 
        
    ```javascript
    function recognizeInk() {
    // add the code from the below steps here 
    }
    ```

    1. 建立您的端點 URL、 訂用帳戶金鑰，以及範例 JSON 的變數。 然後建立`XMLHttpRequest`傳送 API 要求的物件。 
        
        ```javascript
        // Replace the below URL with the correct one for your subscription. 
        // Your endpoint can be found in the Azure portal. For example: https://westus2.api.cognitive.microsoft.com
        var SERVER_ADDRESS = "YOUR-SUBSCRIPTION-URL";
        var ENDPOINT_URL = SERVER_ADDRESS + "/inkrecognizer/v1.0-preview/recognize";
        // Replace the subscriptionKey string value with your valid subscription key.
        var SUBSCRIPTION_KEY = "YOUR-SUBSCRIPTION-KEY";
        var xhttp = new XMLHttpRequest();
        ```
    2. 建立傳回的函式，如`XMLHttpRequest`物件。 此函式會剖析從成功的要求，API 回應，並顯示應用程式中。 
            
        ```javascript
        function returnFunction(xhttp) {
            var response = JSON.parse(xhttp.responseText);
            console.log("Response: %s ", response);
            document.getElementById('response').innerHTML = JSON.stringify(response, null, 2);
        }
        ```
    3. 建立要求物件的錯誤函式。 此函式會將錯誤記錄至主控台。 
            
        ```javascript
        function errorFunction() {
            console.log("Error: %s, Detail: %s", xhttp.status, xhttp.responseText);
        }
        ```

    4. 建立要求物件的函式`onreadystatechange`屬性。 當要求物件的整備狀態變更時，將會套用上述的傳回和錯誤函式。
            
        ```javascript
        xhttp.onreadystatechange = function () {
            if (this.readyState === 4) {
                if (this.status === 200) {
                    returnFunction(xhttp);
                } else {
                    errorFunction(xhttp);
                }
            }
        };
        ```
    
    5. 傳送 API 要求。 新增您的訂用帳戶金鑰，才能`Ocp-Apim-Subscription-Key`標頭，以及組`content-type`至 `application/json`
    
        ```javascript
        xhttp.open("PUT", ENDPOINT_URL, true);
        xhttp.setRequestHeader("Ocp-Apim-Subscription-Key", SUBSCRIPTION_KEY);
        xhttp.setRequestHeader("content-type", "application/json");
        xhttp.send(JSON.stringify(sampleJson));
        };

## Run the application and view the response

This application can be run within your web browser. A successful response is returned in JSON format. You can also find the JSON response on [GitHub](https://go.microsoft.com/fwlink/?linkid=2089502):

## Next steps

> [!div class="nextstepaction"]
> [REST API reference](https://go.microsoft.com/fwlink/?linkid=2089907)

To see how the Ink Recognition API works in a digital inking app, take a look at the following sample applications on GitHub:
* [C# and Universal Windows Platform(UWP)](https://go.microsoft.com/fwlink/?linkid=2089803)  
* [C# and Windows Presentation Foundation(WPF)](https://go.microsoft.com/fwlink/?linkid=2089804)
* [Javascript web-browser app](https://go.microsoft.com/fwlink/?linkid=2089908)       
* [Java and Android mobile app](https://go.microsoft.com/fwlink/?linkid=2089906)
* [Swift and iOS mobile app](https://go.microsoft.com/fwlink/?linkid=2089805)
