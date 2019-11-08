---
title: 快速入門：辨識來自音訊檔案的語音 - 語音服務
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 10/28/2019
ms.author: erhopf
ms.openlocfilehash: bbd3880ea679dc5fc86c0fc1ece101ca3fca74d9
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2019
ms.locfileid: "73500818"
---
在本快速入門中，您將使用[語言 SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) 辨識音訊檔案中的語音。 在滿足幾個必要條件之後，辨識檔案中的語音只需要五個步驟：
> [!div class="checklist"]
> * 從您的訂用帳戶金鑰和區域建立 ````SpeechConfig```` 物件。
> * 建立指定 .WAV 檔案名稱的 ````AudioConfig```` 物件。
> * 使用上面的 ````SpeechConfig```` 和 ````AudioConfig```` 物件建立 ````SpeechRecognizer```` 物件。
> * 使用 ````SpeechRecognizer```` 物件，開始針對單一表達進行辨識程序。
> * 檢查所傳回的 ````SpeechRecognitionResult````。
