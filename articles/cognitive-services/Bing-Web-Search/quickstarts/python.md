---
title: 呼叫和回應 - Azure 認知服務，Bing Web 搜尋 API 的 Python 快速入門 | Microsoft Docs
description: 取得資訊和程式碼範例，以協助您在 Azure 上的 Microsoft 認知服務中快速開始使用 Bing Web 搜尋 API。
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: article
ms.date: 9/18/2017
ms.author: v-jerkin
ms.openlocfilehash: 8d4df9db60c7a74a5b9e53d4622528c0054b4f19
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/23/2018
ms.locfileid: "35368546"
---
# <a name="call-and-response-your-first-bing-web-search-query-in-python"></a>呼叫和回應：以 Python 撰寫的第一個 Bing Web 搜尋查詢

Bing Web 搜尋 API 提供的體驗與 Bing.com/Search 類似，都會傳回 Bing 判斷與使用者的查詢相關的搜尋結果。 結果可能包括網頁、影像、影片、新聞和實體，以及相關的搜尋查詢、拼字校正、時區、單位轉換、翻譯以及計算。 您取得的結果種類取決於其相關性，以及訂閱的 Bing 搜尋 API 層。

請參閱 [API 參考](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference) \(英文\) 了解 API 的技術詳細資料。

您可以按一下 [launch Binder] \(啟動 Binder\) 徽章，在 [MyBinder](https://mybinder.org) \(英文\) 上以 Jupyter Notebook 執行此範例： 

[![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=BingWebSearchAPI.ipynb)


## <a name="prerequisites"></a>先決條件
您必須有具備 **Bing 搜尋 API** 的[認知服務 API 帳戶](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) \(英文\)。 [免費試用版](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api)即足以供本快速入門使用。 您必須要有啟用免費試用版時所提供的存取金鑰，或者您可以從 Azure 儀表板使用付費訂用帳戶金鑰。

## <a name="running-the-walkthrough"></a>執行逐步解說

將 Bing API 服務的 `subscription_key` 設定為您的 API 金鑰。


```python
subscription_key = None
assert subscription_key
```

接下來，確認 `search_url` 端點正確無誤。 在撰寫本文時，Bing 搜尋 API 只使用一個端點。 如果您遇到授權錯誤，請對照 Azure 儀表板中的 Bing 搜尋端點以仔細檢查此值。


```python
search_url = "https://api.cognitive.microsoft.com/bing/v7.0/search"
```

設定 `search_term` 查詢 Bing 尋找 Microsoft 認知服務。


```python
search_term = "Microsoft Cognitive Services"
```

下列區塊使用 Python 中的 `requests` 程式庫，呼叫 Bing 搜尋 API 並將結果以 JSON 物件的形式傳回。 觀察我們透過 `headers` 字典傳入 API 金鑰，然後透過 `params` 字典搜尋字詞。 若要查看可用來篩選搜尋結果的選項完整清單，請參閱 [REST API](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference) \(英文\) 文件。


```python
import requests

headers = {"Ocp-Apim-Subscription-Key" : subscription_key}
params  = {"q": search_term, "textDecorations":True, "textFormat":"HTML"}
response = requests.get(search_url, headers=headers, params=params)
response.raise_for_status()
search_results = response.json()
```

`search_results` 物件包含搜尋結果，以及豐富的中繼資料，例如相關查詢和頁面。 下列程式碼將查詢所傳回的前幾個頁面格式化。


```python
from IPython.display import HTML

rows = "\n".join(["""<tr>
                       <td><a href=\"{0}\">{1}</a></td>
                       <td>{2}</td>
                     </tr>""".format(v["url"],v["name"],v["snippet"]) \
                  for v in search_results["webPages"]["value"]])
HTML("<table>{0}</table>".format(rows))
```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [Bing Web 搜尋單頁應用程式教學課程](../tutorial-bing-web-search-single-page-app.md)

## <a name="see-also"></a>另請參閱 

[Bing Web 搜尋概觀](../overview.md)  
[試試看](https://azure.microsoft.com/services/cognitive-services/bing-web-search-api/)  
[取得免費試用的存取金鑰](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api)
[Bing Web 搜尋 API 參考](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference)
