---
title: 物件偵測 - 電腦視覺
titleSuffix: Azure Cognitive Services
description: 了解使用量和限制的 Computer Vision API-物件偵測功能相關的概念。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 012ab849c926de332da55361c79c76c5a1311169
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2019
ms.locfileid: "60001842"
---
# <a name="detect-common-objects-in-images"></a>偵測影像中的通用物件

物件偵測與[標記功能](concept-tagging-images.md)類似，但 API 會傳回每個所找到物件的週框方塊座標 (以像素為單位)。 例如，如果影像包含狗、貓或人物，「偵測」作業就會列出這些物件及其在影像中的座標。 您可以使用此功能來處理影像中物件間的關聯性。 还可以确定图像中是否有多个相同标记的实例。

偵測 API 會根據在影像中識別到的物件或生物來套用標記。 目前，沒有任何標記的分類法和物件偵測分類之間的正式關聯性。 从概念上讲，检测 API 仅查找对象和生物，而标记 API 还可以包含诸如“室内”等上下文术语，这些术语不能使用边界框进行本地化。

## <a name="object-detection-example"></a>物件偵測範例

下列 JSON 回應說明了在範例影像中偵測到物件時，電腦視覺傳回的內容。

![在廚房使用 Microsoft Surface 裝置的女性](./Images/windows-kitchen.jpg)

```json
{
   "objects":[
      {
         "rectangle":{
            "x":730,
            "y":66,
            "w":135,
            "h":85
         },
         "object":"kitchen appliance",
         "confidence":0.501
      },
      {
         "rectangle":{
            "x":523,
            "y":377,
            "w":185,
            "h":46
         },
         "object":"computer keyboard",
         "confidence":0.51
      },
      {
         "rectangle":{
            "x":471,
            "y":218,
            "w":289,
            "h":226
         },
         "object":"Laptop",
         "confidence":0.85,
         "parent":{
            "object":"computer",
            "confidence":0.851
         }
      },
      {
         "rectangle":{
            "x":654,
            "y":0,
            "w":584,
            "h":473
         },
         "object":"person",
         "confidence":0.855
      }
   ],
   "requestId":"a7fde8fd-cc18-4f5f-99d3-897dcd07b308",
   "metadata":{
      "width":1260,
      "height":473,
      "format":"Jpeg"
   }
}
```

## <a name="limitations"></a>限制

请务必注意对象检测的限制，以便可以避免或缓解误报（缺少对象）和有限详细信息的影响。

* 如果对象小（小于图像的 5%），则通常不删除对象。
* 如果对象紧密地排列在一起（例如一摞盘子），则通常不会检测到对象。
* 無法依品牌或產品名稱區分物件 (例如貨架上有不同種類的汽水)。 不過，您可以使用[品牌偵測](concept-brand-detection.md)功能，從影像中取得品牌資訊。

## <a name="use-the-api"></a>使用 API

物件偵測功能是[分析影像](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) API 的一部分。 您可以透過原生 SDK 或 REST 呼叫來呼叫此 API。 包含`Objects`中**visualFeatures**查詢參數。 然後，當您取得完整的 JSON 回應時，只是將字串剖析內容的`"objects"`一節。

* [快速入門：分析影像 (.NET SDK)](./quickstarts-sdk/csharp-analyze-sdk.md)
* [快速入門：分析影像 (REST API)](./quickstarts/csharp-analyze.md)