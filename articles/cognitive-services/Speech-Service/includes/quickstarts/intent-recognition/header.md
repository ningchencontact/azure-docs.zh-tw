---
title: 快速入門：辨識語音、意圖和實體 - 語音服務
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/28/2019
ms.author: erhopf
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: 3b50ba8f2c3d21fb5bb0ab2c26cedb6bda0fab16
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2019
ms.locfileid: "73500738"
---
在本快速入門中，您將使用[語音 SDK](~/articles/cognitive-services/speech-service/speech-sdk.md)，以互動方式從麥克風擷取的音訊資料中辨識語音。 在滿足幾個必要條件之後，從麥克風辨識語音只需採取幾個步驟：
> [!div class="checklist"]
> * 從您的訂用帳戶金鑰和區域建立 ````SpeechConfig```` 物件。
> * 使用上面的 ````SpeechConfig```` 物件來建立 ````IntentRecognizer```` 物件。
> * 使用 ````IntentRecognizer```` 物件，開始針對單一表達進行辨識程序。
> * 檢查所傳回的 ````IntentRecognitionResult````。