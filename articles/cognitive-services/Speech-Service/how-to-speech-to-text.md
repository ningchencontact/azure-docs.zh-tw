---
title: 使用語音轉換文字 | Microsoft Docs
description: 了解如何使用語音服務中的語音轉換文字
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
manager: noellelacharite
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 05/07/2018
ms.author: v-jerkin
ms.openlocfilehash: 878a31992415b1f8688afcfb186fcd94ce2567b4
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/07/2018
ms.locfileid: "35371016"
---
# <a name="use-speech-to-text-in-the-speech-service"></a>使用語音服務中的「語音轉換文字」

使用 [語音轉換文字] 應用程式的方式有兩種。

| 方法 | 說明 |
|-|-|
| [SDK](speech-sdk.md) | 適用於 C/C++、C# 和 Java * 開發人員的最簡單方式 |
| [REST](rest-apis.md) | 使用 HTTP POST 要求辨識簡短的語句 | 

\* *Java SDK 是 [語音裝置 SDK](speech-devices-sdk.md) 的一部分。*

## <a name="using-the-sdk"></a>使用 SDK

[語音 SDK](speech-sdk.md) 提供最簡單的方式，在您的應用程式中使用完整功能的 [語音轉換文字]。

1. 建立語音處理站，並提供語音服務訂用帳戶金鑰或授權權杖。 您也可以在此時設定一些選項，例如辨識語言或自有語音辨識模型的自訂端點。

2. 從處理站取得辨識器。 有不同類型的辨識器可以使用。 每個辨識器類型都可以使用您裝置的預設麥克風、音訊串流或檔案中的音訊。

    辨識器 | 函式
    -|-
    語音辨識器|提供語音的文字轉譯
    意圖辨識器|在辨識之後透過 [LUIS](https://docs.microsoft.com/azure/cognitive-services/luis/) 衍生喇叭意圖\*
    翻譯辨識器|將轉譯的文字翻譯為另一種語言 (請參閱[語音翻譯](how-to-translate-speech.md))

    \* *對於意圖辨識，您必須在為意圖辨識器建立語音處理站時，使用個別的 LUIS 訂用帳戶金鑰。*
    
4. 繫結事件以便執行非同步作業 (如有需要)。 當辨識器具有過渡期和最終結果時，它會接著呼叫事件處理常式。 否則，您的應用程式會收到最終轉譯結果。

5. 開始辨識。

### <a name="sdk-samples"></a>SDK 範例

如需最新的範例集，請參閱[認知服務語音 SDK 範例 GitHub 存放庫](https://aka.ms/csspeech/samples) (英文)。

## <a name="using-the-rest-api"></a>使用 REST API

如果您不是使用 SDK 所支援的語言，則 REST API 是最簡單的語音辨識方式。 您會對服務端點所提出的 HTTP POST 要求，並且在要求主體中傳遞整個語句。 您會收到回應，其中包含已辨識的文字。

> [!NOTE]
> 使用 REST API 時，語句會受限於 15 秒或更短。


如需 [REST API] REST API 的詳細資訊，請參閱 [REST API](rest-apis.md#speech-to-text)。 若要查看運作中的 REST API，可從 GitHub 下載 [REST API 範例](https://github.com/Azure-Samples/SpeechToText-REST)。

## <a name="next-steps"></a>後續步驟

- [取得語音試用版訂用帳戶](https://azure.microsoft.com/try/cognitive-services/)
- [如何以 C# 辨識語音](quickstart-csharp-windows.md)
