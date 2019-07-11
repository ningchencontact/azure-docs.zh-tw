---
title: 快速入門：在雙語字典中查詢單字 (Python) - 翻譯工具文字 API
titleSuffix: Azure Cognitive Services
description: 在本快速入門中，您將了解如何搭配使用 Python 和翻譯工具文字 REST API 尋找指定文字的替代翻譯和使用範例。
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: quickstart
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: 019e1d382ca3fed4789d7b8c1498b795e1e3e92d
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/28/2019
ms.locfileid: "67444943"
---
# <a name="quickstart-look-up-words-with-bilingual-dictionary-using-python"></a>快速入門：使用 Python 在雙語字典中查詢單字

在本快速入門中，您將了解如何搭配使用 Python 和翻譯工具文字 REST API 尋找指定文字的替代翻譯和使用範例。

本快速入門需要 [Azure 認知服務帳戶](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)和翻譯工具文字資源。 如果您還沒有帳戶，可以使用[免費試用](https://azure.microsoft.com/try/cognitive-services/)來取得訂用帳戶金鑰。

## <a name="prerequisites"></a>必要條件

本快速入門需要：

* Python 2.7.x 或 3.x
* 翻譯工具文字的 Azure 訂用帳戶金鑰

## <a name="create-a-project-and-import-required-modules"></a>建立專案，並匯入所需的模組

使用您最愛的 IDE 或編輯器，建立新的 Python 專案，或在您桌面上建立新資料夾。 將下列程式碼片段複製到您的專案/資料夾中名為 `dictionary-lookup.py` 的檔案中。

```python
# -*- coding: utf-8 -*-
import os
import requests
import uuid
import json
```

> [!NOTE]
> 如果您未曾使用這些模組，則必須先加以安裝，再執行您的程式。 若要安裝這些套件，請執行：`pip install requests uuid`。

第一個註解會指出您的 Python 解譯器應使用 UTF-8 編碼。 然後，所需的模組會匯入，並從環境變數中讀取您的訂用帳戶金鑰、建構 HTTP 要求、建立唯一的識別碼，並處理翻譯工具文字 API 所傳回的 JSON 回應。

## <a name="set-the-subscription-key-base-url-and-path"></a>設定訂用帳戶金鑰、基底 URL 和路徑

此範例會嘗試從環境變數 `TRANSLATOR_TEXT_KEY` 中讀取您的翻譯工具文字訂用帳戶金鑰。 如果您不熟悉環境變數，您可以將 `subscriptionKey` 設為字串，並註解化條件陳述式。

請將下列程式碼複製到您的專案中：

```python
# Checks to see if the Translator Text subscription key is available
# as an environment variable. If you are setting your subscription key as a
# string, then comment these lines out.
if 'TRANSLATOR_TEXT_KEY' in os.environ:
    subscriptionKey = os.environ['TRANSLATOR_TEXT_KEY']
else:
    print('Environment variable for TRANSLATOR_TEXT_KEY is not set.')
    exit()
# If you want to set your subscription key as a string, uncomment the line
# below and add your subscription key. Then, be sure to delete your "os" import.
# subscriptionKey = 'put_your_key_here'
```

翻譯工具文字全域端點會設定為 `base_url`。 `path` 會設定 `dictionary/lookup` 路由，並指出我們要叫用第 3 版的 API。

`params` 會用來設定來源和輸出語言。 在此範例中，我們會使用英文和西班牙文：`en` 和 `es`。

>[!NOTE]
> 如需關於端點、路由和要求參數的詳細資訊，請參閱[翻譯工具文字 API 3.0：字典查閱](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-dictionary-lookup)。

```python
base_url = 'https://api.cognitive.microsofttranslator.com'
path = '/dictionary/lookup?api-version=3.0'
params = '&from=en&to=es'
constructed_url = base_url + path + params
```

## <a name="add-headers"></a>新增標頭

要驗證要求，最簡單的方式是將您的訂用帳戶金鑰以 `Ocp-Apim-Subscription-Key` 標頭的形式傳入，我們在此範例中即採用此方式。 或者，您可以將訂用帳戶金鑰替換為存取權杖，並將存取權杖連同 `Authorization` 標頭傳入，以驗證您的要求。 如需詳細資訊，請參閱[驗證](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication)。

請將下列程式碼片段複製到您的專案中：

```python
headers = {
    'Ocp-Apim-Subscription-Key': subscriptionKey,
    'Content-type': 'application/json',
    'X-ClientTraceId': str(uuid.uuid4())
}
```

如果您使用認知服務多服務訂用帳戶，您也必須在要求參數中包含 `Ocp-Apim-Subscription-Region`。 [深入了解使用多服務訂用帳戶進行驗證](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication)。 

## <a name="create-a-request-to-find-alternate-translations"></a>建立尋找替代翻譯的要求

定義要尋找翻譯的一或多個字串：

```python
# You can pass more than one object in body.
body = [{
    'text': 'Elephants'
}]
```

接著，我們將使用 `requests` 模組建立 POST 要求。 此作業會採用三個引數：串連的 URL、要求標頭和要求本文：

```python
request = requests.post(constructed_url, headers=headers, json=body)
response = request.json()
```

## <a name="print-the-response"></a>列印回應

最後一個步驟是列印結果。 此程式碼片段會排序索引鍵、設定縮排，並宣告項目和索引鍵分隔符號，以美化結果。

```python
print(json.dumps(response, sort_keys=True, indent=4,
                 ensure_ascii=False, separators=(',', ': ')))
```

## <a name="put-it-all-together"></a>組合在一起

如此，您就建立了一個簡單的程式，會呼叫翻譯工具文字 API 並傳回 JSON 回應。 現在，請執行您的程式：

```console
python alt-translations.py
```

如果想要將您的程式碼與我們的做比較，請使用 [GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-Python) 上提供的完整範例。

## <a name="sample-response"></a>範例回應

```json
[
    {
        "displaySource": "elephants",
        "normalizedSource": "elephants",
        "translations": [
            {
                "backTranslations": [
                    {
                        "displayText": "elephants",
                        "frequencyCount": 1207,
                        "normalizedText": "elephants",
                        "numExamples": 5
                    }
                ],
                "confidence": 1.0,
                "displayTarget": "elefantes",
                "normalizedTarget": "elefantes",
                "posTag": "NOUN",
                "prefixWord": ""
            }
        ]
    }
]
```

## <a name="clean-up-resources"></a>清除資源

如果您已將訂用帳戶金鑰硬式編碼到您的程式中，請務必在完成本快速入門後移除訂用帳戶金鑰。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [瀏覽 GitHub 上的 Python 範例](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-Python) (英文)

## <a name="see-also"></a>另請參閱

了解如何使用翻譯工具文字 API 來執行下列動作：

* [翻譯文字](quickstart-python-translate.md)
* [進行文字音譯](quickstart-python-transliterate.md)
* [從輸入識別語言](quickstart-python-detect.md)
* [取得支援的語言清單](quickstart-python-languages.md)
* [從輸入判斷句子長度](quickstart-python-sentences.md)
