---
title: 如何取得 V3 預測端點
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 10/14/2019
ms.author: diberry
ms.openlocfilehash: bc6e43faca47e360daa8214e6b9f6e9df4a2f130
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2019
ms.locfileid: "73495390"
---
1. 在 LUIS 入口網站的 [管理] 區段 (右上方功能表) 的 [金鑰和端點] 頁面 (左側功能表) 中，選取頁面底部的端點 URL。

    此動作會開啟瀏覽器索引標籤，其中網址列會顯示端點 URL。

    URL 有您的應用程式識別碼、金鑰和位置名稱。 V3 預測端點 URL 如下所示：

    `https://REGION.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/APP-ID/slots/SLOT-NAME/predict?subscription-key=YOUR-KEY&<optional-name-value-pairs>&query=UTTERANCE-TEXT.`

