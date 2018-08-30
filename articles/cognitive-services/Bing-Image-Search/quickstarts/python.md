---
title: 快速入門：在 Python 中針對 Bing 影像搜尋 API 使用 REST API 來傳送搜尋查詢
description: 在本快速入門中，您可以使用 Python 傳送搜尋查詢給 Bing 搜尋 API，以取得相關影像清單。
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 9/21/2017
ms.author: v-jerkin
ms.openlocfilehash: bc527ba39b580935f113f56aa63f7bdd283ba304
ms.sourcegitcommit: a2ae233e20e670e2f9e6b75e83253bd301f5067c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/13/2018
ms.locfileid: "42093866"
---
# <a name="quickstart-send-search-queries-using-the-rest-api-and-python"></a>快速入門：使用 REST API 和 Python 傳送搜尋查詢

Bing 影像搜尋 API 可讓您將使用者搜尋查詢傳送到 Bing，以提供類似 Bing.com/Images 的體驗，並取回相關影像清單。

本逐步解說示範呼叫 Bing 影像搜尋 API，以及對產生的 JSON 物件進行後處理的簡單範例。 如需詳細資訊，請參閱 [Bing 影像搜尋文件](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference)。

您可以按一下 [launch Binder] \(啟動 Binder\) 徽章，在 [MyBinder](https://mybinder.org) \(英文\) 上以 Jupyter Notebook 執行此範例： 

[![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=BingImageSearchAPI.ipynb)

## <a name="prerequisites"></a>必要條件

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="running-the-walkthrough"></a>執行逐步解說
若要繼續進行本逐步解說，請將 Bing API 服務的 `subscription_key` 設定為您的 API 金鑰。


```python
subscription_key = None
assert subscription_key
```

接下來，確認 `search_url` 端點正確無誤。 在撰寫本文時，Bing 搜尋 API 只使用一個端點。 如果您遇到授權錯誤，請對照 Azure 儀表板中的 Bing 搜尋端點以仔細檢查此值。


```python
search_url = "https://api.cognitive.microsoft.com/bing/v7.0/images/search"
```

設定 `search_term` 尋找小狗的影像。


```python
search_term = "puppies"
```

下列區塊使用 Python 中的 `requests` 程式庫，呼叫 Bing 搜尋 API 並將結果以 JSON 物件的形式傳回。 觀察我們透過 `headers` 字典傳入 API 金鑰，然後透過 `params` 字典搜尋字詞。 若要查看可用來篩選搜尋結果的選項完整清單，請參閱 [REST API](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference) \(英文\) 文件。


```python
import requests

headers = {"Ocp-Apim-Subscription-Key" : subscription_key}
params  = {"q": search_term, "license": "public", "imageType": "photo"}
response = requests.get(search_url, headers=headers, params=params)
response.raise_for_status()
search_results = response.json()
```

`search_results` 物件包含實際的影像，以及豐富的中繼資料，例如相關項目。 例如，下列程式碼可以擷取前 16 個結果的縮圖 URL。


```python
thumbnail_urls = [img["thumbnailUrl"] for img in search_results["value"][:16]]
```

然後，我們可以使用 `PIL` 程式庫下載縮圖影像，並使用 `matplotlib` 程式庫以 $4 \times 4$ 的格線呈現這些影像。


```python
%matplotlib inline
import matplotlib.pyplot as plt
from PIL import Image
from io import BytesIO

f, axes = plt.subplots(4, 4)
for i in range(4):
    for j in range(4):
        image_data = requests.get(thumbnail_urls[i+4*j])
        image_data.raise_for_status()
        image = Image.open(BytesIO(image_data.content))        
        axes[i][j].imshow(image)
        axes[i][j].axis("off")
```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [Bing 影像搜尋單頁應用程式教學課程](../tutorial-bing-image-search-single-page-app.md) (英文)

## <a name="see-also"></a>另請參閱 

[什麼是 Bing 影像搜尋？](../overview.md)  
[試用 Bing 影像搜尋](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/)  
[取得免費試用的存取金鑰](https://azure.microsoft.com/try/cognitive-services/?api=bing-image-search-api)  
[Bing 影像搜尋 API 參考](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference) (英文)
