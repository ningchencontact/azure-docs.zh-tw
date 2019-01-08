---
title: 快速入門：使用 Bing 圖像式搜尋 REST API 和 Python 取得影像見解
titleSuffix: Azure Cognitive Services
description: 了解如何將影像上傳到 Bing 圖像式搜尋 API 並取得其見解。
services: cognitive-services
author: swhite-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-visual-search
ms.topic: quickstart
ms.date: 5/16/2018
ms.author: scottwhi
ms.openlocfilehash: 3930de4d8d1f50c0ba6908ea642fc152c29b7371
ms.sourcegitcommit: 21466e845ceab74aff3ebfd541e020e0313e43d9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/21/2018
ms.locfileid: "53744797"
---
# <a name="quickstart-your-first-bing-visual-search-query-in-python"></a>快速入門：使用 Python 的第一個 Bing 圖像式搜尋查詢

使用本快速入門來進行您對 Bing 圖像式搜尋 API 的第一次呼叫並檢視搜尋結果。 這個簡單的 JavaScript 應用程式會將影像上傳至 API，並顯示傳回的相關資訊。 雖然此應用程式是以 JavaScript 撰寫的，但 API 是一種與大多數程式設計語言都相容的 RESTful Web 服務。

上傳本機影像時，POST 表單資料必須包含 Content-Disposition 標頭。 其 `name` 參數必須設定為 "image"，而 `filename` 參數可以設定為任何字串。 表單的內容是影像的二進位檔案。 您可以上傳的影像大小上限為 1 MB。

```
--boundary_1234-abcd
Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"

ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

--boundary_1234-abcd--
```

## <a name="prerequisites"></a>必要條件

* [Python 3.x](https://www.python.org/)


[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="initialize-the-application"></a>初始化應用程式

1. 在您慣用的 IDE 或編輯器中建立新的 Python 專案，以及新增下列匯入陳述式。

    ```python
    import requests, json
    ```

2. 為您的訂用帳戶金鑰、端點，以及您上傳影像的路徑，建立變數。

    ```python

    BASE_URI = 'https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch'
    SUBSCRIPTION_KEY = 'your-subscription-key'
    imagePath = 'your-image-path'
    ```

3. 建立字典物件來保存您要求的標頭資訊。 將您的訂用帳戶金鑰繫結到字串 `Ocp-Apim-Subscription-Key` 中，如下所示。

    ```python
    HEADERS = {'Ocp-Apim-Subscription-Key': SUBSCRIPTION_KEY}
    ```

4. 建立包含您影像的另一個字典，這將會在您傳送要求時開啟並上傳。 

    ```python
    file = {'image' : ('myfile', open(imagePath, 'rb'))}
    ```

## <a name="parse-the-json-response"></a>剖析 JSON 回應

1. 建立一個稱為 `print_json()` 的方法，以便在 API 回應中採用並列印 JSON。

    ```python
    def print_json(obj):
        """Print the object as json"""
        print(json.dumps(obj, sort_keys=True, indent=2, separators=(',', ': ')))
    ```

## <a name="send-the-request"></a>傳送要求

1. 使用 `requests.post()` 將要求傳送至 Bing 圖像式搜尋 API。 包含您端點、標頭和檔案資訊的字串。 使用 `print_json()` 列印 `response.json()`

    ```python
    try:
        response = requests.post(BASE_URI, headers=HEADERS, files=file)
        response.raise_for_status()
        print_json(response.json())
    
    except Exception as ex:
        raise ex
    ```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [建置自訂搜尋 Web 應用程式](../tutorial-bing-visual-search-single-page-app.md)