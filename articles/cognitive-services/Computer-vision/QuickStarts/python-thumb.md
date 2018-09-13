---
title: 電腦視覺 Python 快速入門建立縮圖 | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: 在本快速入門中，您會在認知服務中使用電腦視覺與 Python，從影像產生縮圖。
services: cognitive-services
author: noellelacharite
manager: nolachar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: quickstart
ms.date: 08/28/2018
ms.author: v-deken
ms.openlocfilehash: bc1d01cd4b8f15ba627d917825ee5205c34f5cdf
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/31/2018
ms.locfileid: "43750372"
---
# <a name="quickstart-generate-a-thumbnail---rest-python"></a>快速入門：產生縮圖 - REST、Python

在本快速入門中，您會使用「電腦視覺」從影像產生縮圖。

您可以使用 [MyBinder](https://mybinder.org) (英文) 上的 Jupyter Notebook，以逐步方式執行本快速入門。 若要啟動 Binder，請選取下列按鈕：

[![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=VisionAPI.ipynb)

## <a name="prerequisites"></a>必要條件

若要使用「電腦視覺」，您需要訂用帳戶金鑰，請參閱[取得訂用帳戶金鑰](../Vision-API-How-to-Topics/HowToSubscribe.md)。

## <a name="intelligently-generate-a-thumbnail"></a>以智慧方式產生縮圖

您可以使用 [Get Thumbnail 方法](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fb) (英文)，產生影像的縮圖。 您指定高度和寬度，可能與輸入影像的外觀比例不同。 電腦視覺會使用智慧型裁剪以智慧方式識別感興趣的區域，並且根據該區域產生裁剪的座標。

若要執行範例，請執行下列步驟：

1. 將下列程式碼複製到新的 Python 指令碼檔案。
1. 將 `<Subscription Key>` 取代為您的有效訂用帳戶金鑰。
1. 必要時，請將 `vision_base_url` 值變更為您取得訂用帳戶金鑰的位置。
1. (選擇性) 將 `image_url` 值變更為其他影像。
1. 執行指令碼。

下列程式碼會使用 Python `requests` 程式庫，來呼叫電腦視覺分析影像 API。 API 金鑰會透過 `headers` 字典傳入。 縮圖大小會透過 `params` 字典傳入。 縮圖會以回應中的位元組陣列形式傳回。

## <a name="get-thumbnail-request"></a>Get Thumbnail 要求

```python
import requests
# If you are using a Jupyter notebook, uncomment the following line.
#%matplotlib inline
import matplotlib.pyplot as plt
from PIL import Image
from io import BytesIO

# Replace <Subscription Key> with your valid subscription key.
subscription_key = "<Subscription Key>"
assert subscription_key

# You must use the same region in your REST call as you used to get your
# subscription keys. For example, if you got your subscription keys from
# westus, replace "westcentralus" in the URI below with "westus".
#
# Free trial subscription keys are generated in the westcentralus region.
# If you use a free trial subscription key, you shouldn't need to change
# this region.
vision_base_url = "https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/"

thumbnail_url = vision_base_url + "generateThumbnail"

# Set image_url to the URL of an image that you want to analyze.
image_url = "https://upload.wikimedia.org/wikipedia/commons/9/94/Bloodhound_Puppy.jpg"

headers = {'Ocp-Apim-Subscription-Key': subscription_key}
params  = {'width': '50', 'height': '50', 'smartCropping': 'true'}
data    = {'url': image_url}
response = requests.post(thumbnail_url, headers=headers, params=params, json=data)
response.raise_for_status()

thumbnail = Image.open(BytesIO(response.content))

# Display the thumbnail.
plt.imshow(thumbnail)
plt.axis("off")

# Verify the thumbnail size.
print("Thumbnail is {0}-by-{1}".format(*thumbnail.size))
```

## <a name="next-steps"></a>後續步驟

探索使用「電腦視覺」在影像中執行光學字元辨識 (OCR)、建立智慧型裁剪縮圖以及偵測、分類、標記和描述視覺特徵 (包括臉部) 的 Python 應用程式。 若要快速地試驗「電腦視覺 API」，請嘗試 [Open API 測試主控台](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console) (英文)。

> [!div class="nextstepaction"]
> [電腦視覺 API Python 教學課程](../Tutorials/PythonTutorial.md)
