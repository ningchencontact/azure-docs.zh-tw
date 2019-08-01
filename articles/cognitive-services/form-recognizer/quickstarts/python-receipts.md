---
title: 快速入門：使用 Python 擷取收據資料 - 表單辨識器
titleSuffix: Azure Cognitive Services
description: 在本快速入門中，您將搭配使用表單辨識器 REST API 和 Python 來擷取銷售收據影像上的資料。
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 07/01/2019
ms.author: pafarley
ms.openlocfilehash: e3c5583f38f7a7f5a3654bfdd27620593175cf58
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/26/2019
ms.locfileid: "68562660"
---
# <a name="quickstart-extract-receipt-data-using-the-form-recognizer-rest-api-with-python"></a>快速入門：搭配使用表單辨識器 REST API 和 Python 來擷取收據資料

在本快速入門中，您將搭配使用 Azure 表單辨識器 REST API 與 Python 來擷取及識別銷售收據上的相關資訊。

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

## <a name="prerequisites"></a>必要條件
若要完成此快速入門，您必須：
- 有權存取表單辨識器的有限存取預覽版。 若要存取此預覽服務，請先填寫並提交[表單辨識器存取要求](https://aka.ms/FormRecognizerRequestAccess)表單。
- 已安裝 [Python](https://www.python.org/downloads/) (如果您想要在本機執行此範例)。
- 收據影像的 URL。 您可以使用本快速入門的[範例影像](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/contoso-receipt.png?raw=true)。

## <a name="create-a-form-recognizer-resource"></a>建立表單辨識器資源

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="analyze-a-receipt"></a>分析收據

若要開始分析收據，請使用下列 Python 指令碼呼叫**分析收據** API。 執行指令碼之前，請進行下列變更：

1. 將 `<Endpoint>` 取代為您從表單辨識器訂用帳戶金鑰中取得的端點。 您可以在表單辨識器的資源 [概觀]  索引標籤上找到此項目。
1. 將 `<your receipt URL>` 取代為收據影像的 URL 位址。
1. 將 `<subscription key>` 取代為您在先前的步驟中複製的訂用帳戶金鑰。

    ```python
    import http.client, urllib.request, urllib.parse, urllib.error, base64

    source = r"<your receipt URL>"
    body = {"url":source}
    body = json.dumps(body)

    headers = {
        # Request headers
        'Content-Type': 'application/json',
        'Ocp-Apim-Subscription-Key': '<subscription key>',
    }

    try:
        conn = http.client.HTTPSConnection('<Endpoint>')
        conn.request("POST", "/formrecognizer/v1.0-preview/prebuilt/receipt/asyncBatchAnalyze", body, headers)
        response = conn.getresponse()
        data = response.read()
        operationURL = "" + response.getheader("Operation-Location")
        print ("Operation-Location header: " + operationURL)
        conn.close()
    except Exception as e:
        print(e)
        exit()
    ```

1. 將程式碼儲存在副檔名為 .py 的檔案中。 例如：form-recognizer-receipts.py  。
1. 開啟命令提示字元視窗。
1. 出現提示時，使用 `python` 命令執行範例。 例如： `python form-recognizer-receipts.py` 。

您會收到包含 **Operation-Location** 標頭的 `202 (Success)` 回應，而指令碼會將其輸出至主控台。 此標頭會包含可用來查詢作業狀態並取得分析結果的作業識別碼。 在下列範例值中，`operations/` 之後的字串就是作業識別碼。

```console
https://cognitiveservice/formrecognizer/v1.0-preview/prebuilt/receipt/operations/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

## <a name="get-the-receipt-results"></a>取得收據結果

呼叫**分析收據** API 之後，您可以呼叫**取得收據結果** API 來取得作業狀態並擷取資料。 將下列程式碼新增到 Python 指令碼底部。 這會擷取作業識別碼值，並將其傳遞至新的 API 呼叫。 作業是非同步的，因此此指令碼會定時呼叫 API，直到有結果為止。 我們建議的間隔為一秒以上。

```python
operationId = operationURL.split("operations/")[1]

conn = http.client.HTTPSConnection('<Endpoint>')
while True:
    try:
        conn.request("GET", f"/formrecognizer/v1.0-preview/prebuilt/receipt/operations/{operationId}", "", headers)
        responseString = conn.getresponse().read().decode('utf-8')
        responseDict = json.loads(responseString)
        conn.close()
        print(responseString)
        if 'status' in responseDict and responseDict['status'] not in ['NotStarted','Running']:
            break
        time.sleep(1)
    except Exception as e:
        print(e)
        exit()
```

1. 儲存指令碼。
1. 再次使用 `python` 命令執行範例。 例如： `python form-recognize-analyze.py` 。

### <a name="examine-the-response"></a>檢查回應

指令碼會將回應輸出到主控台，直到分析作業完成。 接著，指令碼會將已擷取的文字資料以 JSON 格式輸出。 `"recognitionResults"` 欄位會包含擷取自收據的每一行文字，而 `"understandingResults"` 欄位會包含收據中最相關部分的索引鍵/值資訊。

請參閱下列收據影像和其對應的 JSON 輸出。 為了方便閱讀，我們已將輸出縮短。

![來自 Contoso 商店的收據](../media/contoso-receipt.png)

```json
{
  "status": "Succeeded",
  "recognitionResults": [{
    "page": 1,
    "clockwiseOrientation": 0.36,
    "width": 1688,
    "height": 3000,
    "unit": "pixel",
    "lines": [{
      "boundingBox": [616, 291, 1050, 278, 1053, 384, 620, 397],
      "text": "Contoso",
      "words": [{
        "boundingBox": [619, 292, 1051, 284, 1052, 382, 620, 398],
        "text": "Contoso"
      }]
    }, {
      "boundingBox": [322, 588, 501, 600, 497, 655, 318, 643],
      "text": "Contoso",
      "words": [{
        "boundingBox": [330, 590, 501, 602, 499, 654, 326, 644],
        "text": "Contoso"
      }]
    },
    ...
    ]
  }],
  "understandingResults": [{
    "pages": [1],
    "fields": {
      "Subtotal": {
        "valueType": "numberValue",
        "value": 1098.99,
        "text": "1098.99",
        "elements": [{
          "$ref": "#/recognitionResults/0/lines/14/words/1"
        }]
      },
      "Total": {
        "valueType": "numberValue",
        "value": 1203.39,
        "text": "1203.39",
        "elements": [{
          "$ref": "#/recognitionResults/0/lines/18/words/1"
        }]
      },
      "Tax": {
        "valueType": "numberValue",
        "value": 104.4,
        "text": "$104.40",
        "elements": [{
          "$ref": "#/recognitionResults/0/lines/16/words/0"
        }, {
          "$ref": "#/recognitionResults/0/lines/16/words/1"
        }]
      },
      "MerchantAddress": {
        "valueType": "stringValue",
        "value": "123 Main Street Redmond, WA 98052",
        "text": "123 Main Street Redmond, WA 98052",
        "elements": [{
          "$ref": "#/recognitionResults/0/lines/2/words/0"
        }, {
          "$ref": "#/recognitionResults/0/lines/2/words/1"
        }, {
          "$ref": "#/recognitionResults/0/lines/2/words/2"
        }, {
          "$ref": "#/recognitionResults/0/lines/3/words/0"
        }, {
          "$ref": "#/recognitionResults/0/lines/3/words/1"
        }, {
          "$ref": "#/recognitionResults/0/lines/3/words/2"
        }]
      },
      "MerchantName": {
        "valueType": "stringValue",
        "value": "Contoso",
        "text": "Contoso",
        "elements": [{
          "$ref": "#/recognitionResults/0/lines/1/words/0"
        }]
      },
      "MerchantPhoneNumber": {
        "valueType": "stringValue",
        "value": null,
        "text": "123-456-7890",
        "elements": [{
          "$ref": "#/recognitionResults/0/lines/4/words/0"
        }]
      },
      "TransactionDate": {
        "valueType": "stringValue",
        "value": "2019-06-10",
        "text": "6/10/2019",
        "elements": [{
          "$ref": "#/recognitionResults/0/lines/5/words/0"
        }]
      },
      "TransactionTime": {
        "valueType": "stringValue",
        "value": "13:59:00",
        "text": "13:59",
        "elements": [{
          "$ref": "#/recognitionResults/0/lines/5/words/1"
        }]
      }
    }
  }]
}
```

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已搭配使用表單辨識器 REST API 和 Python 來定型模型，並在範例案例中加以執行。 接下來，請參閱參考文件來深入探索表單辨識器 API。

> [!div class="nextstepaction"]
> [REST API 參考文件](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api/operations/AnalyzeReceipt)
