---
title: 快速入門：合成語音 - 語音服務
titleSuffix: Azure Cognitive Services
description: 了解如何使用語音 SDK 合成語音
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 09/20/2019
ms.author: yulili
ms.openlocfilehash: 263a8e98c4c029728272c020efe00e82be20f5e2
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2019
ms.locfileid: "73500516"
---
在本快速入門中，您將使用[語音 SDK](~/articles/cognitive-services/speech-service/speech-sdk.md)，將文字轉換為合成語音。 滿足幾個必要條件之後，將合成的語音轉譯為預設說話者只需要四個步驟：
> [!div class="checklist"]
> * 從您的訂用帳戶金鑰和區域建立 ````SpeechConfig```` 物件。
> * 使用上面的 ````SpeechConfig```` 物件來建立 ````SpeechSynthesizer```` 物件。
> * 使用 ````SpeechSynthesizer```` 物件讀出文字。
> * 檢查對於錯誤傳回的 ````SpeechSynthesisResult````。
