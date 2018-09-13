---
title: 電腦視覺 API cURL 快速入門分析本機影像 | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: 在本快速入門中，您會在認知服務中使用電腦視覺與 cURL，分析本機影像。
services: cognitive-services
author: noellelacharite
manager: nolachar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: quickstart
ms.date: 08/28/2018
ms.author: v-deken
ms.openlocfilehash: 93ca3ea6eee3743dfd0c25c9514375ae63a531ee
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/31/2018
ms.locfileid: "43750637"
---
# <a name="quickstart-analyze-a-local-image---rest-curl"></a>快速入門：分析本機影像 - REST、cURL

在本快速入門中，您會使用「電腦視覺」來分析要擷取視覺功能的本機影像。 若要分析遠端影像，請參閱[使用 cURL 來分析遠端影像](curl-analyze.md)。

## <a name="prerequisites"></a>必要條件

若要使用「電腦視覺」，您需要訂用帳戶金鑰，請參閱[取得訂用帳戶金鑰](../Vision-API-How-to-Topics/HowToSubscribe.md)。

## <a name="analyze-a-local-image"></a>分析本機影像

這個範例類似於[使用 cURL 分析遠端影像](curl-analyze.md)，不同之處在於要分析的影像是在本機從磁碟讀取。 需要進行三項變更：

- 將內容類型變更為 `"Content-Type: application/octet-stream"`。
- 將 `-d` 參數變更為 `--data-binary`。
- 使用下列語法指定要分析的影像：`@C:/Pictures/ImageToAnalyze.jpg`。

若要執行範例，請執行下列步驟：

1. 請將下列程式碼複製到編輯器中。
1. 將 `<Subscription Key>` 換成您的有效訂用帳戶金鑰。
1. 必要時，請將要求 URL (`https://westcentralus.api.cognitive.microsoft.com/vision/v2.0`) 變更為使用您取得訂用帳戶金鑰的位置。
1. 使用您想要分析的本機影像取代 `<Image To Analyze>`。
1. (選擇性) 變更回應語言 (`language=en`)。
1. 在已安裝 cURL 的電腦上開啟命令視窗。
1. 將程式碼貼入視窗中，然後執行命令。

>[!NOTE]
>您必須在 REST 呼叫中使用與用來取得訂用帳戶金鑰相同的位置。 例如，如果您已從 westus 取得訂用帳戶金鑰，請將下列 URL 中的 "westcentralus" 取代為 "westus"。

```json
curl -H "Ocp-Apim-Subscription-Key: <Subscription Key>" -H "Content-Type: application/octet-stream" "https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/analyze?visualFeatures=Categories,Description&details=Landmarks&language=en" --data-binary <Image To Analyze>
```

## <a name="analyze-image-response"></a>Analyze Image 回應

成功的回應會以 JSON 格式傳回，例如：

```json
{
  "categories": [
    {
      "name": "outdoor_water",
      "score": 0.9921875,
      "detail": {
        "landmarks": []
      }
    }
  ],
  "description": {
    "tags": [
      "nature",
      "water",
      "waterfall",
      "outdoor",
      "rock",
      "mountain",
      "rocky",
      "grass",
      "hill",
      "covered",
      "hillside",
      "standing",
      "side",
      "group",
      "walking",
      "white",
      "man",
      "large",
      "snow",
      "grazing",
      "forest",
      "slope",
      "herd",
      "river",
      "giraffe",
      "field"
    ],
    "captions": [
      {
        "text": "a large waterfall over a rocky cliff",
        "confidence": 0.916458423253597
      }
    ]
  },
  "requestId": "b6e33879-abb2-43a0-a96e-02cb5ae0b795",
  "metadata": {
    "height": 959,
    "width": 1280,
    "format": "Jpeg"
  }
}
```

## <a name="next-steps"></a>後續步驟

探索用來分析影像、偵測名人和地標、建立縮圖及擷取印刷和手寫文字的「電腦視覺 API」。 若要快速地試驗「電腦視覺 API」，請嘗試 [Open API 測試主控台](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console) (英文)。

> [!div class="nextstepaction"]
> [探索電腦視覺 API](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44) (英文)
