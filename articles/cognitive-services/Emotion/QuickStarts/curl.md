---
title: 表情 API cURL 快速入門 | Microsoft Docs
description: 取得資訊和程式碼範例，以協助您搭配 cURL 快速開始使用認知服務中的表情 API。
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: emotion-api
ms.topic: article
ms.date: 05/23/2017
ms.author: anroth
ms.openlocfilehash: a7ca2cac718797462bb4dc889b3f1361b252435e
ms.sourcegitcommit: 0fa8b4622322b3d3003e760f364992f7f7e5d6a9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/27/2018
ms.locfileid: "37021093"
---
# <a name="emotion-api-curl-quick-start"></a>表情 API cURL 快速入門

> [!IMPORTANT]
> 影片 API 預覽將於 2017 年 10 月 30 日結束。 請試用新的[影片索引器 API 預覽](https://azure.microsoft.com/services/cognitive-services/video-indexer/)，以輕鬆地從影片中擷取見解，並增強內容探索體驗，像是偵測話語、臉部、人物及表情而得來的搜尋結果。 [深入了解](https://docs.microsoft.com/azure/cognitive-services/video-indexer/video-indexer-overview)。

本文提供資訊和程式碼範例，以協助您搭配 cURL 快速開始使用[表情 API 辨識方法](https://westus.dev.cognitive.microsoft.com/docs/services/5639d931ca73072154c1ce89/operations/563b31ea778daf121cc3a5fa)來辨識出影像中一或多個人員所表現出來的表情。 

## <a name="prerequisite"></a>必要條件
* 從[這裡](https://azure.microsoft.com/try/cognitive-services/)取得免費的訂用帳戶金鑰

## <a name="recognize-emotions-curl-example-request"></a>辨識表情 cURL 範例要求

> [!NOTE]
> 您必須在 REST 呼叫中使用與用來取得訂用帳戶金鑰相同的位置。 例如，如果您已從 westcentralus 取得訂用帳戶金鑰，請將下列 URL 中的 "westus" 取代為 "westcentralus"。

```json
@ECHO OFF

curl -v -X POST "https://westus.api.cognitive.microsoft.com/emotion/v1.0/recognize"
-H "Content-Type: application/json"
-H "Ocp-Apim-Subscription-Key: {subscription key}"

--data-ascii "{body}" 
```

## <a name="recognize-emotions-sample-response"></a>辨識表情範例回應
成功呼叫將會傳回臉部項目的陣列，以及其相關聯的表情分數，依臉部矩形大小以遞減方式排序。 空的回應表示沒有偵測到任何臉部。 表情項目包含下列欄位：
* faceRectangle - 臉部於影像中的矩形位置。
* scores - 影像中每個臉部的表情分數。 

```json
application/json 
[
  {
    "faceRectangle": {
      "left": 68,
      "top": 97,
      "width": 64,
      "height": 97
    },
    "scores": {
      "anger": 0.00300731952,
      "contempt": 5.14648448E-08,
      "disgust": 9.180124E-06,
      "fear": 0.0001912825,
      "happiness": 0.9875571,
      "neutral": 0.0009861537,
      "sadness": 1.889955E-05,
      "surprise": 0.008229999
    }
  }
]

