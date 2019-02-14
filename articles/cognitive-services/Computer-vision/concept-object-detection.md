---
title: 物件偵測 - 電腦視覺
titleSuffix: Azure Cognitive Services
description: 使用電腦視覺 API 偵測物件的相關概念。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 12/03/2018
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: ee85e6bd171fc9415e5c7606d6e18a7a22fa6570
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/07/2019
ms.locfileid: "55866911"
---
# <a name="object-detection"></a>物件偵測

物件偵測與[標記功能](concept-tagging-images.md)類似，但 API 會傳回每個所找到物件的週框方塊座標 (以像素為單位)。 例如，如果影像包含狗、貓或人物，「偵測」作業就會列出這些物件及其在影像中的座標。 您可以使用此功能來處理影像中物件間的關聯性。 此功能也可讓您判斷影像中是否有多個相同標記的執行個體。

偵測 API 會根據在影像中識別到的物件或生物來套用標記。 請注意，用於標記的分類法和用於物件偵測的分類法之間沒有正式關聯性。 就概念而言，偵測 API 只會尋找物件和生物，而標記 API 也可以包含與環境相關的字詞，例如「室內」，但這無法使用週框方塊來加以當地語系化。

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

請務必注意物件偵測功能的限制，以避免或降低誤判 (遺失物件) 和限制細節的影響。
* 通常不會偵測到太小的物件 (低於影像的 5%)。
* 通常不會偵測到排列過於緊密的物件 (例如堆疊的盤子)。
* 無法依品牌或產品名稱區分物件 (例如貨架上有不同種類的汽水)。 不過，您可以使用[品牌偵測](concept-brand-detection.md)功能，從影像中取得品牌資訊。

## <a name="use-the-api"></a>使用 API
物件偵測功能是[分析影像](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) API 的一部分。 您可以透過原生 SDK 或 REST 呼叫來呼叫此 API。 您取得完整的 JSON 回應時，只需要對於 `"objects"` 一節的內容剖析字串。

* [快速入門：分析影像 (.NET SDK)](./quickstarts-sdk/csharp-analyze-sdk.md)
* [快速入門：分析影像 (REST API)](./quickstarts/csharp-analyze.md)