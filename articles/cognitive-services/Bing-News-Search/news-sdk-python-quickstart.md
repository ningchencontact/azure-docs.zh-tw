---
title: 快速入門：執行新聞搜尋 - 適用於 Python 的 Bing 新聞搜尋 SDK
titleSuffix: Azure Cognitive Services
description: 使用此快速入門以運用「適用於 Python 的 Bing 新聞搜尋 SDK」來搜尋新聞，並處理回應。
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: quickstart
ms.date: 01/10/2019
ms.author: v-gedod
ms.custom: seodec2018
ms.openlocfilehash: 453da9f5765a0cf9bfdf58026a6d7a2d5a1a2d9f
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/29/2019
ms.locfileid: "55197189"
---
# <a name="quickstart-perform-a-news-search-with-the-bing-news-search-sdk-for-python"></a>快速入門：使用適用於 Python 的 Bing 新聞搜尋 SDK 來執行新聞搜尋

透過本快速入門開始使用適用於 Python 的 Bing 新聞搜尋 SDK 來搜尋新聞。 雖然 Bing 新聞搜尋具有與大部分程式設計語言相容的 REST API，但 SDK 會提供簡單的方法，將服務整合到您的應用程式。 此範例的原始程式碼可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/news_search_samples.py) 上找到。

## <a name="prerequisites"></a>必要條件

* [Python](https://www.python.org/) 2.x 或 3.x

建議使用[虛擬環境](https://docs.python.org/3/tutorial/venv.html)來開發 Python。 您可以使用 [venv 模組](https://pypi.python.org/pypi/virtualenv)來安裝和初始化虛擬環境。 您必須安裝適用於 Python 2.7 的 virtualenv。 您可以使用下列命令建立虛擬環境：

```console
python -m venv mytestenv
```

您可以使用下列命令安裝 Bing 新聞搜尋 SDK 相依性：
    
```console
python -m pip install azure-cognitiveservices-search-newssearch
```

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../includes/cognitive-services-bing-news-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>建立應用程式並將其初始化

1. 在您最愛的 IDE 或編輯器中建立新的 Python 檔案，並匯入下列程式庫。 建立訂用帳戶金鑰變數和搜尋字詞。

    ```python
    from azure.cognitiveservices.search.newssearch import NewsSearchAPI
    from msrest.authentication import CognitiveServicesCredentials
    subscription_key = "YOUR-SUBSCRIPTION-KEY"
    search_term = "Quantum Computing"
    ```

## <a name="initialize-the-client-and-send-a-request"></a>初始化用戶端並傳送要求

1. 建立 `CognitiveServicesCredentials` 執行個體。 具現化用戶端：
    
    ```python
    client = NewsSearchAPI(CognitiveServicesCredentials(subscription_key))
    ```

2. 將搜尋查詢傳送至 Bing 新聞搜尋 API，儲存回應。

    ```python
    news_result = client.news.search(query=search_term, market="en-us", count=10)
    ```

## <a name="parse-the-response"></a>剖析回應

如果找到任何搜尋結果，請列印第一個網頁結果：

```python
if news_result.value:
    first_news_result = news_result.value[0]
    print("Total estimated matches value: {}".format(news_result.total_estimated_matches))
    print("News result count: {}".format(len(news_result.value)))
    print("First news name: {}".format(first_news_result.name))
    print("First news url: {}".format(first_news_result.url))
    print("First news description: {}".format(first_news_result.description))
    print("First published time: {}".format(first_news_result.date_published))
    print("First news provider: {}".format(first_news_result.provider[0].name))
else:
    print("Didn't see any news result data..")
```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
[建立單頁 Web 應用程式](tutorial-bing-news-search-single-page-app.md)
