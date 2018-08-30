---
title: 快速入門：在 Python 中使用 SDK 來要求和篩選影像
description: 在本快速入門中，您可以使用 Python 來要求和篩選 Bing 影像搜尋所傳回的影像。
titleSuffix: Azure Image Search SDK Python quickstart
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 02/14/2018
ms.author: v-gedod
ms.openlocfilehash: 4729f103bb9b50d4ff039907db8eb677f3dc290a
ms.sourcegitcommit: a2ae233e20e670e2f9e6b75e83253bd301f5067c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/13/2018
ms.locfileid: "42093802"
---
 # <a name="quickstart-request-and-filter-images-using-the-sdk-and-python"></a>快速入門：使用 SDK 和 Python 來要求和篩選影像

Bing 影像搜尋 SDK 包含用於 Web 查詢以及剖析結果的 REST API 功能。 

[Python Bing 影像搜尋 SDK 範例的原始程式碼](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/image_search_samples.py)可從 Git Hub 取得。

## <a name="application-dependencies"></a>應用程式相依性
若還沒有 Python，請安裝。 SDK 可與 Python 2.7、3.3、3.4、3.5 和 3.6 相容。

Python 開發的一般建議是使用[虛擬環境](https://docs.python.org/3/tutorial/venv.html)。 使用 [venv 模組](https://pypi.python.org/pypi/virtualenv)來安裝和初始化虛擬環境。 您必須安裝適用於 Python 2.7 的 virtualenv。
```
python -m venv mytestenv
```
安裝 Bing 影像搜尋 SDK 相依性：
```
cd mytestenv
python -m pip install azure-cognitiveservices-search-imagesearch
```
## <a name="image-search-client"></a>影像搜尋用戶端
在「搜尋」下取得[認知服務存取金鑰](https://azure.microsoft.com/try/cognitive-services/)。 新增匯入：
```
from azure.cognitiveservices.search.imagesearch import ImageSearchAPI
from azure.cognitiveservices.search.imagesearch.models import ImageType, ImageAspect, ImageInsightModule
from msrest.authentication import CognitiveServicesCredentials

subscription_key = "YOUR-SUBSCRIPTION-KEY"
```
建立 `CognitiveServicesCredentials` 的執行個體，並具現化用戶端：
```
client = ImageSearchAPI(CognitiveServicesCredentials(subscription_key))
```
搜尋查詢 (約塞美提溪谷) 的影像，並篩選出動畫 gif 和寬型外觀。 驗證結果數目，然後列印出第一個結果的 insightsToken、縮圖 URL 和 Web URL。
```
image_results = client.images.search(
        query="Yosemite",
        image_type=ImageType.animated_gif, # Could be the str "AnimatedGif"
        aspect=ImageAspect.wide # Could be the str "Wide"
    )
    print("\r\nSearch images for \"Yosemite\" results that are animated gifs and wide aspect")

    if image_results.value:
        first_image_result = image_results.value[0]
        print("Image result count: {}".format(len(image_results.value)))
        print("First image insights token: {}".format(first_image_result.image_insights_token))
        print("First image thumbnail url: {}".format(first_image_result.thumbnail_url))
        print("First image web search url: {}".format(first_image_result.web_search_url))
    else:
        print("Couldn't find image results!")

```
搜尋 (約塞美提溪谷) 的影像，並篩選出動畫 gif 和寬型外觀。  驗證結果數目。  列印出第一個結果的 `insightsToken`、`thumbnail url` 和 `web url`。
```
image_results = client.images.search(
    query="Yosemite",
    image_type=ImageType.animated_gif, # Could be the str "AnimatedGif"
    aspect=ImageAspect.wide # Could be the str "Wide"
)
print("\r\nSearch images for \"Yosemite\" results that are animated gifs and wide aspect")

if image_results.value:
    first_image_result = image_results.value[0]
    print("Image result count: {}".format(len(image_results.value)))
    print("First image insights token: {}".format(first_image_result.image_insights_token))
    print("First image thumbnail url: {}".format(first_image_result.thumbnail_url))
    print("First image web search url: {}".format(first_image_result.web_search_url))
else:
    print("Couldn't find image results!")

```

取得趨勢結果：
```
trending_result = client.images.trending()
print("\r\nSearch trending images")

# Categorires
if trending_result.categories:
    first_category = trending_result.categories[0]
    print("Category count: {}".format(len(trending_result.categories)))
    print("First category title: {}".format(first_category.title))
    if first_category.tiles:
        first_tile = first_category.tiles[0]
        print("Subcategory tile count: {}".format(len(first_category.tiles)))
        print("First tile text: {}".format(first_tile.query.text))
        print("First tile url: {}".format(first_tile.query.web_search_url))
    else:
        print("Couldn't find subcategory tiles!")
    else:
        print("Couldn't find categories!")

```

## <a name="next-steps"></a>後續步驟

[認知服務 Python SDK 範例](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples)


