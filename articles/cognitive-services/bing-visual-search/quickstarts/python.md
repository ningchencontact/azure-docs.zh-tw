---
title: 快速入門：使用 Bing 圖像式搜尋 REST API 和 Python 取得影像見解
titleSuffix: Azure Cognitive Services
description: 了解如何將影像上傳到 Bing 圖像式搜尋 API 並取得其見解。
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: quickstart
ms.date: 4/02/2019
ms.author: scottwhi
ms.openlocfilehash: 7ec37b4c3bdeb924b3e35dbcb5d07a478611f631
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/05/2019
ms.locfileid: "59047121"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-rest-api-and-python"></a>快速入門：使用 Bing 圖像式搜尋 REST API 和 Python 取得影像見解

使用本快速入門進行您對 Bing 圖像式搜尋 API 的第一次呼叫，並檢視結果。 此 Python 應用程式會將影像上傳至 API，並顯示它傳回的資訊。 雖然此應用程式是以 Python 撰寫的，但 API 是一種與大多數程式設計語言都相容的 RESTful Web 服務。

上傳本機影像時，表單資料必須包含 `Content-Disposition` 標頭。 您必須將其 `name` 參數設為 "image"，而 `filename` 參數則可設為任何字串。 表單的內容包含影像的二進位資料。 您可以上傳的影像大小上限為 1 MB。

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

1. 在您慣用的 IDE 或編輯器中建立新的 Python 專案，以及新增下列 `import` 陳述式：

    ```python
    import requests, json
    ```

2. 為您的訂用帳戶金鑰、端點，以及您上傳影像的路徑，建立變數：

    ```python

    BASE_URI = 'https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch'
    SUBSCRIPTION_KEY = 'your-subscription-key'
    imagePath = 'your-image-path'
    ```

3. 建立字典物件來保存您要求的標頭資訊。 將您的訂用帳戶金鑰繫結到字串 `Ocp-Apim-Subscription-Key` 中，如下所示：

    ```python
    HEADERS = {'Ocp-Apim-Subscription-Key': SUBSCRIPTION_KEY}
    ```

4. 建立包含您影像的另一個字典，這將會在您傳送要求時開啟並上傳：

    ```python
    file = {'image' : ('myfile', open(imagePath, 'rb'))}
    ```

## <a name="parse-the-json-response"></a>剖析 JSON 回應

1. 建立名為 `print_json()` 的方法以用於 API 回應中，以及列印 JSON：

    ```python
    def print_json(obj):
        """Print the object as json"""
        print(json.dumps(obj, sort_keys=True, indent=2, separators=(',', ': ')))
    ```

## <a name="send-the-request"></a>傳送要求

1. 使用 `requests.post()` 將要求傳送至 Bing 圖像式搜尋 API。 包含您端點、標頭和檔案資訊的字串。 使用 `print_json()` 列印 `response.json()`：

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
> [建立圖像式搜尋單頁 Web 應用程式](../tutorial-bing-visual-search-single-page-app.md)
