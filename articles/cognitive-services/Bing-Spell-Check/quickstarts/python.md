---
title: 快速入門：使用 Bing 拼字檢查 REST API 和 Python 進行拼字檢查
titlesuffix: Azure Cognitive Services
description: 開始使用 Bing 拼字檢查 REST API 來檢查拼字和文法。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 02/20/2019
ms.author: aahi
ms.openlocfilehash: e95006c6448bf1179d33bcd00c16d6e4246db148
ms.sourcegitcommit: 24906eb0a6621dfa470cb052a800c4d4fae02787
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2019
ms.locfileid: "56887313"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-rest-api-and-python"></a>快速入門：使用 Bing 拼字檢查 REST API 和 Python 進行拼字檢查

使用本快速入門，第一次呼叫 Bing 拼字檢查 REST API。 這個簡單的 Python 應用程式會將要求傳送至 API，並傳回建議的更正清單。 雖然此應用程式是以 Python 撰寫的，但 API 是一種與大多數程式設計語言都相容的 RESTful Web 服務。 您可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/python/Search/BingEntitySearchv7.py) 上找到此應用程式的原始程式碼

## <a name="prerequisites"></a>必要條件

* Python [3.x](https://www.python.org)

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]


## <a name="initialize-the-application"></a>初始化應用程式

1. 在您慣用的 IDE 或編輯器中建立新的 Python 專案，以及新增下列匯入陳述式。

   ```python
   import requests
   import json
   ```

2. 為您要進行拼字檢查的文字、您的訂用帳戶金鑰和 Bing 拼字檢查端點建立變數。

    ```python
    api_key = "enter-your-key-here"
    example_text = "Hollo, wrld" # the text to be spell-checked
    endpoint = "https://api.cognitive.microsoft.com/bing/v7.0/SpellCheck"
    ```

## <a name="create-the-parameters-for-the-request"></a>建立要求的參數

1. 建立以 `text` 作為索引鍵、並以您的文字作為值的新字典。

    ```python
    data = {'text': example_text}
    ```

2. 新增要求的參數。 將 `mkt` 參數設為您的市場，並將 `mode` 設為 `proof`。 

    ```python
    params = {
        'mkt':'en-us',
        'mode':'proof'
        }
    ```

3. 將 `Content-Type` 標頭和您的訂用帳戶金鑰新增至 `Ocp-Apim-Subscription-Key` 標頭。

    ```python
    headers = {
        'Content-Type': 'application/x-www-form-urlencoded',
        'Ocp-Apim-Subscription-Key': api_key,
        }
    ```

## <a name="send-the-request-and-read-the-response"></a>傳送要求並讀取回應

1. 使用要求程式庫傳送 POST 要求。

    ```python
    response = requests.post(endpoint, headers=headers, params=params, data=data)
    ```

2. 取得 JSON 回應，並加以列印。
    
    ```python
    json_response = response.json()
    print(json.dumps(json_response, indent=4))
    ```

## <a name="example-json-response"></a>範例 JSON 回應

如以下範例所示，成功的回應會以 JSON 格式來傳回： 

```json
{
   "_type": "SpellCheck",
   "flaggedTokens": [
      {
         "offset": 0,
         "token": "Hollo",
         "type": "UnknownToken",
         "suggestions": [
            {
               "suggestion": "Hello",
               "score": 0.9115257530801
            },
            {
               "suggestion": "Hollow",
               "score": 0.858039839213461
            },
            {
               "suggestion": "Hallo",
               "score": 0.597385084464481
            }
         ]
      },
      {
         "offset": 7,
         "token": "wrld",
         "type": "UnknownToken",
         "suggestions": [
            {
               "suggestion": "world",
               "score": 0.9115257530801
            }
         ]
      }
   ]
}
```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [建立單頁 Web 應用程式](../tutorials/spellcheck.md)

- [什麼是 Bing 拼字檢查 API？](../overview.md)
- [Bing 拼字檢查 API v7 參考](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v7-reference)
