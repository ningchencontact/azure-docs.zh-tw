---
title: 快速入門：Bing 圖像式搜尋 SDK Python
titleSuffix: Azure Cognitive Services
description: 設定圖像式搜尋 SDK Python 主控台應用程式。
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-visual-search
ms.topic: quickstart
ms.date: 06/11/2018
ms.author: v-gedod
ms.openlocfilehash: 9f2a6d9b75ccf704862d169b96ea1a1f2edb9815
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/28/2018
ms.locfileid: "52445679"
---
# <a name="quickstart-bing-visual-search-sdk-python"></a>快速入門：Bing 圖像式搜尋 SDK Python

Bing 圖像式搜尋 SDK 使用 Web 要求和剖析結果的 REST API 功能。
[Python Bing 圖像式搜尋 SDK 範例的原始程式碼](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/visual_search_samples.py)可從 Git Hub 取得。

程式碼案例記錄在下列標題底下：
* [圖像式搜尋用戶端](#client)
* [完成主控台應用程式](#complete-console)
* [包含 cropArea 的影像二進位檔發佈](#binary-crop)
* [KnowledgeRequest 參數](#knowledge-req)
* [標記、動作和 actionType](#tags-actions)

## <a name="application-dependencies"></a>應用程式相依性
* 為使用本快速入門，您必須在 S9 定價層上啟用訂用帳戶，如[認知服務定價 - Bing 搜尋 API](https://azure.microsoft.com/en-us/pricing/details/cognitive-services/search-api/) 中所示。 

若要在 Azure 入口網站中啟用訂用帳戶：
1. 在 Azure 入口網站頂端顯示 `Search resources, services, and docs` 的文字方塊中輸入 'BingSearchV7'。  
2. 在下拉式清單中的 Marketplace 下選取 `Bing Search v7`。
3. 輸入新資源的 `Name`。
4. 選取 `Pay-As-You-Go` 訂用帳戶。
5. 選取 `S9` 定價層。
6. 按一下 `Enable` 來啟用訂用帳戶。
 
* 若還沒有 Python，請安裝。 SDK 可與 Python 2.7、3.3、3.4、3.5 和 3.6 相容。
* Python 開發的一般建議是使用[虛擬環境](https://docs.python.org/3/tutorial/venv.html)。 使用 [venv 模組](https://pypi.python.org/pypi/virtualenv) 安裝和初始化虛擬環境。 安裝適用於 Python 2.7 的 virtualenv。
```
python -m venv mytestenv
```
安裝 Bing 圖像式搜尋 SDK 相依項目：
```
cd mytestenv
python -m pip install azure-cognitiveservices-search-visualsearch
```

<a name="client"></a> 
## <a name="visual-search-client"></a>圖像式搜尋用戶端
若要建立 `VisualSearchAPI` 用戶端的執行個體，請匯入下列的程式庫：
```
import http.client, urllib.parse
import json
import os.path
from azure.cognitiveservices.search.visualsearch import VisualSearchAPI
from azure.cognitiveservices.search.visualsearch.models import (
    VisualSearchRequest,
    CropArea,
    ImageInfo,
    Filters,
    KnowledgeRequest,
)
```
以有效的訂用帳戶金鑰取代 subscriptionKey 字串值。
```
subscription_key = 'YOUR-VISUAL-SEARCH-ACCESS-KEY'
```
然後，將用戶端具現化：
```
var client = new WebSearchAPI(new ApiKeyServiceClientCredentials("YOUR-ACCESS-KEY"));
```
使用用戶端來搜尋影像和剖析結果：
```
PATH = 'C:\\Users\\USER\\azure-cognitive-samples\\mytestenv\\TestImages\\'
image_path = os.path.join(PATH, "image.jpg")

with open(image_path, "rb") as image_fd:

    # You need to pass the serialized form of the model
    knowledge_request = json.dumps(VisualSearchRequest().serialize())

    print("\r\nSearch visual search request with binary of dog image")
    result = client.images.visual_search(image=image_fd, knowledge_request=knowledge_request)
        
    if not result:
        print("No visual search result data.")

        # Visual Search results
    if result.image.image_insights_token:
        print("Uploaded image insights token: {}".format(result.image.image_insights_token))
    else:
        print("Couldn't find image insights token!")

    # List of tags
    if result.tags:
        first_tag = result.tags[0]
        print("Visual search tag count: {}".format(len(result.tags)))

        # List of actions in first tag
        if first_tag.actions:
            first_tag_action = first_tag.actions[0]
            print("First tag action count: {}".format(len(first_tag.actions)))
            print("First tag action type: {}".format(first_tag_action.action_type))
        else:
            print("Couldn't find tag actions!")
    else:
        print("Couldn't find image tags!")

```

<a name="complete-console"></a> 
## <a name="complete-console-application"></a>完成主控台應用程式

下列主控台應用程式會執行先前定義的查詢和剖析結果：
```
import http.client, urllib.parse
import json
import os.path

# Replace the subscriptionKey string value with your valid subscription key.
subscription_key = 'YOUR-VISUAL-SEARCH-ACCESS-KEY'

PATH = 'C:\\Users\\v-gedod\\azure-cognitive-samples\\mytestenv\\TestImages\\'

from azure.cognitiveservices.search.visualsearch import VisualSearchAPI
from azure.cognitiveservices.search.visualsearch.models import (
    VisualSearchRequest,
    CropArea,
    ImageInfo,
    Filters,
    KnowledgeRequest,)

from msrest.authentication import CognitiveServicesCredentials

client = VisualSearchAPI(CognitiveServicesCredentials(subscription_key))

image_path = os.path.join(PATH, "image.jpg")

with open(image_path, "rb") as image_fd:

    # You need to pass the serialized form of the model
    knowledge_request = json.dumps(VisualSearchRequest().serialize())

    print("\r\nSearch visual search request with binary of dog image")
    result = client.images.visual_search(image=image_fd, knowledge_request=knowledge_request)
        
    if not result:
        print("No visual search result data.")

        # Visual Search results
    if result.image.image_insights_token:
        print("Uploaded image insights token: {}".format(result.image.image_insights_token))
    else:
        print("Couldn't find image insights token!")

    # List of tags
    if result.tags:
        first_tag = result.tags[0]
        print("Visual search tag count: {}".format(len(result.tags)))

        # List of actions in first tag
        if first_tag.actions:
            first_tag_action = first_tag.actions[0]
            print("First tag action count: {}".format(len(first_tag.actions)))
            print("First tag action type: {}".format(first_tag_action.action_type))
        else:
            print("Couldn't find tag actions!")
    else:
        print("Couldn't find image tags!")


# Uncomment these methods to include code under the following headings of this documentation.
#search_image_binary_with_crop_area(client, subscription_key, PATH)
#search_url_with_filters(client, subscription_key)
#search_insights_token_with_crop_area(client, subscription_key)

```

Bing 搜尋範例會示範 SDK 的各種功能。  將下列函式新增至先前定義的 `VisualSrchSDK` 類別。

<a name="binary-crop"></a>
## <a name="image-binary-post-with-croparea"></a>包含 cropArea 的影像二進位檔發佈

下列程式碼會將影像二進位檔傳送至 Post 要求的內容，並且包含 cropArea 物件。  然後輸出 imageInsightsToken、標記數目、動作數目和第一個 actionType。

```
def search_image_binary_with_crop_area(client, sub_key, file_path):

    #client = VisualSearchAPI(CognitiveServicesCredentials(sub_key))

    image_path = os.path.join(file_path, "image.jpg")
    with open(image_path, "rb") as image_fd:

        crop_area = CropArea(top=0.1,bottom=0.5,left=0.1,right=0.9)
        knowledge_request = VisualSearchRequest(image_info=ImageInfo(crop_area=crop_area))

        # You need to pass the serialized form of the model
        knowledge_request = json.dumps(knowledge_request.serialize())

        print("\r\nSearch visual search request with binary of dog image")
        result = client.images.visual_search(image=image_fd, knowledge_request=knowledge_request)

        if not result:
            print("No visual search result data.")
            return

        # Visual Search results
        if result.image.image_insights_token:
            print("Uploaded image insights token: {}".format(result.image.image_insights_token))
        else:
            print("Couldn't find image insights token!")

        # List of tags
        if result.tags:
            first_tag = result.tags[0]
            print("Visual search tag count: {}".format(len(result.tags)))

            # List of actions in first tag
            if first_tag.actions:
                first_tag_action = first_tag.actions[0]
                print("First tag action count: {}".format(len(first_tag.actions)))
                print("First tag action type: {}".format(first_tag_action.action_type))
            else:
                print("Couldn't find tag actions!")
        else:
            print("Couldn't find image tags!")


```
<a name="knowledge-req"></a>
## <a name="knowledgerequest-parameter"></a>KnowledgeRequest 參數

下列程式碼會將影像 URL 傳送至 `knowledgeRequest` 參數中，並且包含 \"site:pinterest.com\" 篩選。 然後，它會列出 `imageInsightsToken`、標記數目、動作數目和第一個 actionType。
```
def search_url_with_filters(client_in, sub_key):

    client = client_in

    image_url = "https://images.unsplash.com/photo-1512546148165-e50d714a565a?w=600&q=80"
    filters = Filters(site="pinterest.com")

    knowledge_request = VisualSearchRequest(
        image_info=ImageInfo(url=image_url),
        knowledge_request=KnowledgeRequest(filters=filters)
    )

    # You need to pass the serialized form of the model
    knowledge_request = json.dumps(knowledge_request.serialize())

    print("\r\nSearch visual search request with url of dog image")
    result = client.images.visual_search(knowledge_request=knowledge_request)

    if not result:
        print("No visual search result data.")
        return

    # Visual Search results
    if result.image.image_insights_token:
        print("Uploaded image insights token: {}".format(result.image.image_insights_token))
    else:
        print("Couldn't find image insights token!")

    # List of tags
    if result.tags:
        first_tag = result.tags[0]
        print("Visual search tag count: {}".format(len(result.tags)))

        # List of actions in first tag
        if first_tag.actions:
            first_tag_action = first_tag.actions[0]
            print("First tag action count: {}".format(len(first_tag.actions)))
            print("First tag action type: {}".format(first_tag_action.action_type))
        else:
            print("Couldn't find tag actions!")
    else:
        print("Couldn't find image tags!")

```
<a name="tags-actions"></a>
## <a name="tags-actions-and-actiontype"></a>標記、動作和 actionType

下列程式碼會將影像見解權杖傳送至 knowledgeRequest 參數中，並且包含 cropArea 物件。 然後輸出 imageInsightsToken、標記數目、動作數目和第一個 actionType。

```
    client = client_in

    image_insights_token = "bcid_CA6BDBEA28D57D52E0B9D4B254F1DF0D*ccid_6J+8V1zi*thid_R.CA6BDBEA28D57D52E0B9D4B254F1DF0D"
    crop_area = CropArea(top=0.1,bottom=0.5,left=0.1,right=0.9)

    knowledge_request = VisualSearchRequest(
        image_info=ImageInfo(
            image_insights_token=image_insights_token,
            crop_area=crop_area
        ),
    )

    # You need to pass the serialized form of the model
    knowledge_request = json.dumps(knowledge_request.serialize())

    print("\r\nSearch visual search request with URL of dog image")
    result = client.images.visual_search(knowledge_request=knowledge_request)

    if not result:
        print("No visual search result data.")
        return

    # Visual Search results
    if result.image.image_insights_token:
        print("Uploaded image insights token: {}".format(result.image.image_insights_token))
    else:
        print("Couldn't find image insights token!")

    # List of tags
    if result.tags:
        first_tag = result.tags[0]
        print("Visual search tag count: {}".format(len(result.tags)))

        # List of actions in first tag
        if first_tag.actions:
            first_tag_action = first_tag.actions[0]
            print("First tag action count: {}".format(len(first_tag.actions)))
            print("First tag action type: {}".format(first_tag_action.action_type))
        else:
            print("Couldn't find tag actions!")
    else:
        print("Couldn't find image tags!")
```

## <a name="next-steps"></a>後續步驟

[認知服務 .NET SDK 範例](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)。