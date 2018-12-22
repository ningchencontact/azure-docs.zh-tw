---
title: 快速入門：使用 Python SDK 來偵測並框出影像中的臉部
titleSuffix: Azure Cognitive Services
description: 在此快速入門中，您會建立簡單的 Python 指令碼，以使用臉部 API 來偵測並框出遠端影像中的人臉。
services: cognitive-services
author: SteveMSFT
manager: cgronlun
ms.service: cognitive-services
ms.component: face-api
ms.topic: quickstart
ms.date: 11/13/2018
ms.author: sbowles
ms.openlocfilehash: 1e35d650f6fc99bff5bf49e517e2b38fcdc58dde
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/08/2018
ms.locfileid: "53076996"
---
# <a name="quickstart-create-a-python-script-to-detect-and-frame-faces-in-an-image"></a>快速入門：建立 Python 指令碼來偵測並框出影像中的臉部

在此快速入門中，您會建立簡單的 Python 指令碼 (透過 Python SDK)，以使用 Azure 臉部 API 來偵測遠端影像中的人臉。 此應用程式會顯示選取的影像，並在每個偵測到的人臉周圍繪製框架。

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。 

## <a name="prerequisites"></a>必要條件

- 臉部 API 訂用帳戶金鑰。 您可以從[試用認知服務](https://azure.microsoft.com/try/cognitive-services/?api=face-api)取得免費的試用訂用帳戶金鑰。 或是，依照[建立認知服務帳戶](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)中的指示訂閱臉部 API 服務並取得金鑰。
- [Python 2.7+ 或 3.5+](https://www.python.org/downloads/)
- [pip](https://pip.pypa.io/en/stable/installing/) 工具

## <a name="get-the-face-sdk"></a>取得臉部 SDK

透過開啟命令提示字元並執行下列命令，來安裝臉部 Python SDK：

```shell
pip install cognitive_face
```

## <a name="detect-faces-in-an-image"></a>偵測影像中的人臉

建立名為 _FaceQuickstart.py_ 的新 Python 指令碼，並新增下列程式碼。 這是臉部偵測的核心功能。 您必須使用您的金鑰值取代 `<Subscription Key>`。 您可能也需要變更 `BASE_URL` 的值，以使用金鑰的正確區域識別碼 (請參閱[臉部 API 文件](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)以取得所有區域端點的清單)。 **westus** 區域會產生免費試用的訂用帳戶金鑰。 (選擇性) 將 `img_url` 設定為所要使用影像的 URL。

此指令碼會藉由呼叫 **cognitive_face.face.detect** 方法來偵測臉部，此方法會包裝[偵測](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) REST API，並傳回臉部清單。

```python
import cognitive_face as CF

KEY = '<Subscription Key>'  # Replace with a valid subscription key (keeping the quotes in place).
CF.Key.set(KEY)

BASE_URL = 'https://westus.api.cognitive.microsoft.com/face/v1.0/'  # Replace with your regional Base URL
CF.BaseUrl.set(BASE_URL)

# You can use this example JPG or replace the URL below with your own URL to a JPEG image.
img_url = 'https://raw.githubusercontent.com/Microsoft/Cognitive-Face-Windows/master/Data/detection1.jpg'
faces = CF.face.detect(img_url)
print(faces)
```

### <a name="try-the-app"></a>試用應用程式

使用 `python FaceQuickstart.py` 命令執行應用程式。 您應該會在主控台視窗中取得文字回應，如下所示：

```shell
[{'faceId': '26d8face-9714-4f3e-bfa1-f19a7a7aa240', 'faceRectangle': {'top': 124, 'left': 459, 'width': 227, 'height': 227}}]
```

這是所偵測到臉部的清單。 清單中的每個項目都是 **dict** 執行個體，其中 `faceId` 是所偵測臉部的唯一識別碼，而 `faceRectangle` 描述所偵測臉部的位置。 

> [!NOTE]
> 臉部識別碼會在 24 小時後到期；如果您想要長期保留識別碼，則必須明確地儲存臉部資料。

## <a name="draw-face-rectangles"></a>繪製臉部矩形

您可以使用從上一個命令收到的座標，在影像上繪製矩形以視覺方式表示每張臉。 您必須安裝 Pillow (`pip install pillow`) 才能使用 Pillow 影像模組。 在 FaceQuickstart.py 頂端，新增下列程式碼：

```python
import requests
from io import BytesIO
from PIL import Image, ImageDraw
```

然後，在指令碼底部新增下列程式碼。 這會建立用於分析矩形座標的簡單函式，並使用 Pillow 在原始影像上繪製矩形。 然後，它會在預設的影像檢視器中顯示該影像。

```python
#Convert width height to a point in a rectangle
def getRectangle(faceDictionary):
    rect = faceDictionary['faceRectangle']
    left = rect['left']
    top = rect['top']
    bottom = left + rect['height']
    right = top + rect['width']
    return ((left, top), (bottom, right))

#Download the image from the url
response = requests.get(img_url)
img = Image.open(BytesIO(response.content))

#For each face returned use the face rectangle and draw a red box.
draw = ImageDraw.Draw(img)
for face in faces:
    draw.rectangle(getRectangle(face), outline='red')

#Display the image in the users default image browser.
img.show()
```

## <a name="run-the-app"></a>執行應用程式

系統可能會提示您先選取預設影像檢視器。 然後，您應該會看到如下的影像。 您應該也會在主控台視窗中看到列印出來的矩形資料。

![臉部周圍繪有紅色矩形的年輕女性](../images/face-rectangle-result.png)

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已了解臉部 API Python SDK 的基本使用程序，並建立了指令碼來偵測並框出影像中的臉部。 接下來，請在更複雜的範例中探索 Python SDK 的使用方式。 移至 GitHub 上的認知臉部 Python 範例、將範例複製到專案資料夾，然後遵循 README.md 檔案中的指示。

> [!div class="nextstepaction"]
> [認知臉部 Python 範例](https://github.com/Microsoft/Cognitive-Face-Python)