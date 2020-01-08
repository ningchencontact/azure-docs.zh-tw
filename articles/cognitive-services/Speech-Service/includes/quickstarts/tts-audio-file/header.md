---
title: 快速入門：將語音合成至音訊檔案 - 語音服務
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
ms.openlocfilehash: 9b13d8fc3b77426a59dea5399223b79c4bb4b1a1
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/25/2019
ms.locfileid: "75469683"
---
在本快速入門中，您將使用[語音 SDK](~/articles/cognitive-services/speech-service/speech-sdk.md)，將文字轉換為音訊檔案中的合成語音。 在滿足幾個必要條件之後，將語音合成為檔案只需要採取五個步驟：
> [!div class="checklist"]
> * 從您的訂用帳戶金鑰和區域建立 ````SpeechConfig```` 物件。
> * 建立指定 .WAV 檔案名稱的音訊組態物件。
> * 使用上述的設定物件建立 ````SpeechSynthesizer```` 物件。
> * 使用 ````SpeechSynthesizer```` 物件，將您的文字轉換為合成的語音，並將它儲存到指定的音訊檔案。
> * 檢查對於錯誤傳回的 ````SpeechSynthesizer````。
