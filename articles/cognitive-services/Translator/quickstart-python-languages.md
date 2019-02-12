---
title: 快速入門：取得支援的語言清單 (Python) - 翻譯工具文字 API
titleSuffix: Azure Cognitive Services
description: 在此快速入門中，您可以取得使用翻譯工具文字 API 搭配 Python 來翻譯、音譯及查閱字典時，所支援的語言清單與範例。
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: quickstart
ms.date: 02/01/2019
ms.author: erhopf
ms.openlocfilehash: 6f52df9166da371b38069138bc4389a9be6b0121
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/04/2019
ms.locfileid: "55692217"
---
# <a name="quickstart-use-the-translator-text-api-to-get-a-list-of-supported-languages-using-python"></a>快速入門：搭配使用翻譯工具文字 API 與 Python 來取得支援的語言清單

在此快速入門中，您將了解如何搭配使用 Python 與翻譯工具文字 REST API 發出 GET 要求，以傳回支援的語言清單。

本快速入門需要 [Azure 認知服務帳戶](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)和翻譯工具文字資源。 如果您還沒有帳戶，可以使用[免費試用](https://azure.microsoft.com/try/cognitive-services/)來取得訂用帳戶金鑰。

## <a name="prerequisites"></a>必要條件

本快速入門需要：

* Python 2.7.x 或 3.x
* 翻譯工具文字的 Azure 訂用帳戶金鑰

## <a name="create-a-project-and-import-required-modules"></a>建立專案，並匯入所需的模組

在您慣用的 IDE 或編輯器建立新的 Python 專案。 然後，將下列程式碼片段複製到您的專案中名為 `get-languages.py` 的檔案。

```python
# -*- coding: utf-8 -*-
import os, requests, uuid, json
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
# below and add your subscription key.
#subscriptionKey = 'put_your_key_here'
```

翻譯工具文字全域端點會設定為 `base_url`。 `path` 會設定 `languages` 路由，並指出我們要叫用第 3 版的 API。

>[!NOTE]
> 如需關於端點、路由和要求參數的詳細資訊，請參閱[翻譯工具文字 API 3.0：語言](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-languages)。

```python
base_url = 'https://api.cognitive.microsofttranslator.com'
path = '/languages?api-version=3.0'
constructed_url = base_url + path
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

## <a name="create-a-request-to-get-a-list-of-supported-languages"></a>建立要求以取得支援的語言清單

我們將使用 `requests` 模組建立 GET 要求。 此作業會採用兩個引數：串連的 URL 和要求標頭：

```python
request = requests.get(constructed_url, headers=headers)
response = request.json()
```

## <a name="print-the-response"></a>列印回應

最後一個步驟是列印結果。 此程式碼片段會排序索引鍵、設定縮排，並宣告項目和索引鍵分隔符號，以美化結果。

```python
print(json.dumps(response, sort_keys=True, indent=4, ensure_ascii=False, separators=(',', ': ')))
```

## <a name="put-it-all-together"></a>組合在一起

如此，您就建立了一個簡單的程式，會呼叫翻譯工具文字 API 並傳回 JSON 回應。 現在，請執行您的程式：

```console
python get-languages.py
```

如果想要將您的程式碼與我們的做比較，請使用 [GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-Python) 上提供的完整範例。

## <a name="sample-response"></a>範例回應

本範例已截斷而僅顯示結果的片段：

```json
{
    "translation": {
        "af": {
            "name": "Afrikaans",
            "nativeName": "Afrikaans",
            "dir": "ltr"
        },
        "ar": {
            "name": "Arabic",
            "nativeName": "العربية",
            "dir": "rtl"
        },
        ...
    },
    "transliteration": {
        "ar": {
            "name": "Arabic",
            "nativeName": "العربية",
            "scripts": [
                {
                    "code": "Arab",
                    "name": "Arabic",
                    "nativeName": "العربية",
                    "dir": "rtl",
                    "toScripts": [
                        {
                            "code": "Latn",
                            "name": "Latin",
                            "nativeName": "اللاتينية",
                            "dir": "ltr"
                        }
                    ]
                },
                {
                    "code": "Latn",
                    "name": "Latin",
                    "nativeName": "اللاتينية",
                    "dir": "ltr",
                    "toScripts": [
                        {
                            "code": "Arab",
                            "name": "Arabic",
                            "nativeName": "العربية",
                            "dir": "rtl"
                        }
                    ]
                }
            ]
        },
      ...
    },
    "dictionary": {
        "af": {
            "name": "Afrikaans",
            "nativeName": "Afrikaans",
            "dir": "ltr",
            "translations": [
                {
                    "name": "English",
                    "nativeName": "English",
                    "dir": "ltr",
                    "code": "en"
                }
            ]
        },
        "ar": {
            "name": "Arabic",
            "nativeName": "العربية",
            "dir": "rtl",
            "translations": [
                {
                    "name": "English",
                    "nativeName": "English",
                    "dir": "ltr",
                    "code": "en"
                }
            ]
        },
      ...
    }
}
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
* [取得替代的翻譯](quickstart-python-dictionary.md)
* [從輸入判斷句子長度](quickstart-python-sentences.md)
