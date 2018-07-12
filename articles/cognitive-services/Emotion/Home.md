---
title: 表情 API 概觀 | Microsoft Docs
description: 透過使用認知服務中的表情 API，來利用 Microsoft 最先進的雲端式表情辨識演算法，建置出更加個人化的應用程式。
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: emotion-api
ms.topic: article
ms.date: 02/06/2017
ms.author: anroth
ms.openlocfilehash: 8383370cba3f78060e809f444f4ad3dab7380f4e
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/23/2018
ms.locfileid: "35368630"
---
# <a name="what-is-emotion-api"></a>什麼是表情 API？

> [!IMPORTANT]
> 表情 API 已在 2017 年 10 月 30 日被淘汰。 此功能現在是[臉部 API](https://docs.microsoft.com/en-us/azure/cognitive-services/face/) 的一部分。

歡迎使用 Microsoft 表情 API，它能讓您利用 Microsoft 的雲端式表情辨識演算法，建置出更加個人化的應用程式。

### <a name="emotion-recognition"></a>表情辨識

表情 API Beta 能接受影像作為輸入，並傳回臉部 API 針對影像中每個臉部在各種表情上的信賴度，以及臉部的週框方塊。 能偵測到的表情包括快樂、憂傷、驚訝、憤怒、害怕、蔑視、厭惡或無表其。 這些表情會透過由表情 API 所識別出來的相同基本臉部運算式，以跨文化及全球性的方式進行溝通。 

**解譯結果：** 

解譯表情 API 的結果時，所偵測到的表情應解譯為具有最高分數的表情，因為系統會將分數標準化為加總為一的形式。 使用者可以根據需要，在其應用程式中設定使用較高的信賴度臨界值。 

如需表情偵測的詳細資訊，請參閱 API 參考： 
  * 基本：如果使用者已呼叫臉部 API，則可以提交臉部方框作為輸入並使用基本層。 [API 參考](https://westus.dev.cognitive.microsoft.com/docs/services/5639d931ca73072154c1ce89/operations/56f23eb019845524ec61c4d7)
  * 標準：如果使用者未提交臉部方框，他們應該使用標準模式。  [API 參考](https://westus.dev.cognitive.microsoft.com/docs/services/5639d931ca73072154c1ce89/operations/563b31ea778daf121cc3a5fa)

如需利用表情 API 解譯串流影片的範例，請參閱[如何即時分析影片](https://docs.microsoft.com/azure/cognitive-services/emotion/emotion-api-how-to-topics/howtoanalyzevideo_emotion)。
