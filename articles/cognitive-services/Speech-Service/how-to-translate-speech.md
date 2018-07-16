---
title: 利用語音服務翻譯語音 | Microsoft Docs
description: 了解如何使用語音服務中的語音翻譯。
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
manager: noellelacharite
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 05/07/2018
ms.author: v-jerkin
ms.openlocfilehash: 7f39f284998489574049d82c44b3d3a0a3797adb
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/07/2018
ms.locfileid: "35371030"
---
# <a name="translate-speech-using-speech-service"></a>利用語音服務翻譯語音

[語音 SDK](speech-sdk.md) 是在您的應用程式中使用語音翻譯的最簡單方式。 SDK 提供服務的完整功能。 執行語音翻譯的基本程序包括下列步驟：

1. 建立語音處理站，並提供語音服務訂用帳戶金鑰或授權權杖。 您也會設定來源和目標翻譯語言，以及指定是否要輸出文字或語音。

2. 從處理站取得辨識器。 如需翻譯，選取翻譯辨識器。 (其他辨識器適用於「語音轉換文字」。)根據您使用的音訊來源，翻譯辨識器有各種類型。

4. 繫結事件以便執行非同步作業 (如有需要)。 當辨識器具有過渡期和最終結果時，它會呼叫事件處理常式。 否則，您的應用程式會收到最終翻譯結果。

5. 開始辨識及翻譯。

# <a name="sdk-samples"></a>SDK 範例

如需最新的範例集，請參閱[認知服務語音 SDK 範例 GitHub 存放庫](https://aka.ms/csspeech/samples) (英文)。

# <a name="next-steps"></a>後續步驟

- [取得語音試用版訂用帳戶](https://azure.microsoft.com/try/cognitive-services/)
- [以 C# 辨識語音](quickstart-csharp-windows.md)
