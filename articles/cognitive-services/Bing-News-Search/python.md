---
title: 快速入門：使用 Python 來執行新聞搜尋 - Bing 新聞搜尋 REST API
titlesuffix: Azure Cognitive Services
description: 使用此快速入門以運用 Python 來傳送要求給「Bing 新聞搜尋 REST API」，並接收 JSON 回應。
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: quickstart
ms.date: 9/21/2017
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: 8ce8353df9a6f8354c56d9c9115645c0b7f2136a
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/11/2018
ms.locfileid: "53251652"
---
# <a name="quickstart-perform-a-news-search-using-python-and-the-bing-news-search-rest-api"></a>快速入門：使用 Python 和 Bing 新聞搜尋 REST API 來執行新聞搜尋

本逐步解說示範呼叫 Bing 新聞搜尋 API，以及對產生的 JSON 物件進行後處理的簡單範例。 如需詳細資訊，請參閱 [Bing 新聞搜尋文件](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference) \(英文\)。  

您可以按一下 [launch Binder] \(啟動 Binder\) 徽章，在 [MyBinder](https://mybinder.org) \(英文\) 上以 Jupyter Notebook 執行此範例： 

[![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=BingNewsSearchAPI.ipynb)

## <a name="prerequisites"></a>必要條件

您必須有具備 **Bing 搜尋 API** 的[認知服務 API 帳戶](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)。 [免費試用版](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api)即足以供本快速入門使用。 啟用免費試用時，您必須提供存取金鑰。  另請參閱[認知服務定價 - Bing 搜尋 API](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/)。

## <a name="running-the-walkthrough"></a>執行逐步解說
首先將 Bing API 服務的 `subscription_key` 設定為您的 API 金鑰。


```python
subscription_key = None
assert subscription_key
```

接下來，確認 `search_url` 端點正確無誤。 在撰寫本文時，Bing 搜尋 API 只使用一個端點。 如果您遇到授權錯誤，請對照 Azure 儀表板中的 Bing 搜尋端點以仔細檢查此值。


```python
search_url = "https://api.cognitive.microsoft.com/bing/v7.0/news/search"
```

設定 `search_term` 尋找關於 Microsoft 的新聞文章。


```python
search_term = "Microsoft"
```

下列區塊使用 Python 中的 `requests` 程式庫，呼叫 Bing 搜尋 API 並將結果以 JSON 物件的形式傳回。 觀察我們透過 `headers` 字典傳入 API 金鑰，然後透過 `params` 字典搜尋字詞。 若要查看可用來篩選搜尋結果的選項完整清單，請參閱 [REST API](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference) \(英文\) 文件。


```python
import requests

headers = {"Ocp-Apim-Subscription-Key" : subscription_key}
params  = {"q": search_term, "textDecorations": True, "textFormat": "HTML"}
response = requests.get(search_url, headers=headers, params=params)
response.raise_for_status()
search_results = response.json()
```

`search_results` 物件包含相關的新文章以及豐富的中繼資料。 例如，下列程式碼會擷取文章的描述。


```python
descriptions = [article["description"] for article in search_results["value"]]
```

這些說明可以再轉譯為以**粗體**醒目提示搜尋關鍵字的資料表。


```python
from IPython.display import HTML
rows = "\n".join(["<tr><td>{0}</td></tr>".format(desc) for desc in descriptions])
HTML("<table>"+rows+"</table>")
```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [將新聞分頁](paging-news.md)
> [使用裝飾標記醒目提示文字](hit-highlighting.md)

## <a name="see-also"></a>另請參閱 

 [搜尋網路上的新聞](search-the-web.md)  
 [試試看](https://azure.microsoft.com/services/cognitive-services/bing-news-search-api/)
