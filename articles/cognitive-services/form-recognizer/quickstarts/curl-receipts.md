---
title: 快速入門：使用 cURL 擷取收據資料 - 表單辨識器
titleSuffix: Azure Cognitive Services
description: 在本快速入門中，您將搭配使用表單辨識器 REST API 和 cURL 來擷取銷售收據影像上的資料。
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 07/01/2019
ms.author: pafarley
ms.openlocfilehash: f8edb27e52d843d9a765aed8da9b75417cf357d1
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/26/2019
ms.locfileid: "68552583"
---
# <a name="quickstart-extract-receipt-data-using-the-form-recognizer-rest-api-with-curl"></a>快速入門：搭配使用表單辨識器 REST API 和 cURL 來擷取收據資料

在本快速入門中，您將搭配使用 Azure 表單辨識器 REST API 與 cURL 來擷取及識別銷售收據上的相關資訊。

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

## <a name="prerequisites"></a>必要條件
若要完成此快速入門，您必須：
- 有權存取表單辨識器的有限存取預覽版。 若要存取此預覽服務，請先填寫並提交[表單辨識器存取要求](https://aka.ms/FormRecognizerRequestAccess)表單。
- 已安裝 [cURL](https://curl.haxx.se/windows/)。
- 收據影像的 URL。 您可以使用本快速入門的[範例影像](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/contoso-receipt.png?raw=true)。

## <a name="create-a-form-recognizer-resource"></a>建立表單辨識器資源

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="analyze-a-receipt"></a>分析收據

若要開始分析收據，請使用下列 cURL 命令呼叫**分析收據** API。 執行命令之前，請進行下列變更：

1. 將 `<Endpoint>` 取代為您從表單辨識器訂用帳戶金鑰中取得的端點。 您可以在表單辨識器的資源 [概觀]  索引標籤上找到此項目。
1. 將 `<your receipt URL>` 取代為收據影像的 URL 位址。
1. 將 `<subscription key>` 取代為您在先前的步驟中複製的訂用帳戶金鑰。

```bash
curl -i -X POST "https://<Endpoint>/formrecognizer/v1.0-preview/prebuilt/receipt/asyncBatchAnalyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>" --data-ascii "{ \"url\": \"<your receipt URL>\"}"
```

您會收到 `202 (Success)` 回應，其中包含 **Operation-Location** 標頭。 此標頭的值會包含可用來查詢作業狀態並取得結果的作業識別碼。 在下列範例中，`operations/` 之後的字串就是作業識別碼。

```console
https://cognitiveservice/formrecognizer/v1.0-preview/prebuilt/receipt/operations/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

## <a name="get-the-receipt-results"></a>取得收據結果

呼叫**分析收據** API 之後，您可以呼叫**取得收據結果** API 來取得作業狀態並擷取資料。

1. 將 `<operationId>` 取代為先前步驟中的作業識別碼。
1. 將 `<subscription key>` 取代為訂用帳戶金鑰。

```bash
curl -X GET "https://<Endpoint>/formrecognizer/v1.0-preview/prebuilt/receipt/operations/<operationId>" -H "Ocp-Apim-Subscription-Key: <subscription key>"
```

### <a name="examine-the-response"></a>檢查回應

您會收到 `200 (Success)` 回應及 JSON 輸出。 第一個 `"status"` 欄位會指出作業的狀態。 如果作業已完成，`"recognitionResults"` 欄位會包含擷取自收據的每一行文字，而 `"understandingResults"` 欄位會包含收據中最相關部分的索引鍵/值資訊。 如果作業未完成，`"status"` 的值將會是 `"Running"` 或 `"NotStarted"`，此時您應該以手動方式或透過指令碼再次呼叫 API。 我們建議您在每個呼叫之前間隔一秒以上的時間。

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

在本快速入門中，您已搭配使用表單辨識器 REST API 和 cURL 來擷取銷售收據上的內容。 接下來，請參閱參考文件來深入探索表單辨識器 API。

> [!div class="nextstepaction"]
> [REST API 參考文件](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api/operations/AnalyzeReceipt)
