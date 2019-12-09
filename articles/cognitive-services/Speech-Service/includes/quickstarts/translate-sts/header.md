---
title: 快速入門：翻譯語音轉換語音 - 語音服務
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
ms.openlocfilehash: 805193c08e57ddc18dfca0a78c6b58cf895baec1
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/04/2019
ms.locfileid: "74817501"
---
在此快速入門中，您將會使用[語音 SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) 來以互動方式將某一種語言的語音翻譯成另一種語言的語音。 在滿足幾個先決條件之後，翻譯語音轉換語音只需要採取六個步驟：
> [!div class="checklist"]
> * 從您的訂用帳戶金鑰和區域建立 ````SpeechTranslationConfig```` 物件。
> * 更新 ````SpeechTranslationConfig```` 物件以指定來源和目標語言。
> * 更新 ````SpeechTranslationConfig```` 物件以指定語音輸出語音名稱。
> * 使用上面的 ````SpeechTranslationConfig```` 物件來建立 ````TranslationRecognizer```` 物件。
> * 使用 ````TranslationRecognizer```` 物件，開始針對單一表達進行辨識程序。
> * 檢查所傳回的 ````TranslationRecognitionResult````。
