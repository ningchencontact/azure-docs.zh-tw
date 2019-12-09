---
title: 快速入門：翻譯語音轉換文字 - 語音服務
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 09/20/2019
ms.author: yulili
ms.openlocfilehash: cd45657beacd04eb2376af3e4297eae051157778
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/04/2019
ms.locfileid: "74816856"
---
在本快速入門中，您將會使用[語音 SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) 以互動方式來將某一種語言的語音翻譯成另一種語言的文字。 在滿足幾個必要條件之後，翻譯語音轉換文字只需要採取五個步驟：
> [!div class="checklist"]
> * 從您的訂用帳戶金鑰和區域建立 ````SpeechConfig```` 物件。
> * 更新 ````SpeechConfig```` 物件以指定來源和目標語言。
> * 使用上面的 ````SpeechConfig```` 物件來建立 ````TranslationRecognizer```` 物件。
> * 使用 ````TranslationRecognizer```` 物件，開始針對單一表達進行辨識程序。
> * 檢查所傳回的 ````TranslationRecognitionResult````。
