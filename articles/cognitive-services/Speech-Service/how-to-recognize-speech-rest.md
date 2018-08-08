---
title: 使用 REST API 辨識語音
description: 了解如何使用語音服務中的語音轉換文字
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 07/16/2018
ms.author: v-jerkin
ms.openlocfilehash: 15db2d9b872c76d70fd531af07fb55c701e86494
ms.sourcegitcommit: 7ad9db3d5f5fd35cfaa9f0735e8c0187b9c32ab1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/27/2018
ms.locfileid: "39331238"
---
# <a name="recognize-speech-by-using-the-rest-api"></a>使用 REST API 辨識語音

[!include[Selector](../../../includes/cognitive-services-speech-service-how-to-recognize-speech-selector.md)]

您可以使用 REST API 及 HTTP POST 要求辨識簡短的語句。

如果您不是使用 SDK 所支援的語言，則 REST API 是最簡單的語音辨識方式。
您會對服務端點所提出的 HTTP POST 要求，並且在要求主體中傳遞整個語句。
您會收到回應，其中包含已辨識的文字。

> [!NOTE]
> 使用 REST API 時，語句會受限於 15 秒或更短。
> 若要辨識較長的語句，請參閱[語音 SDK](how-to-recognize-speech-csharp.md)。

如需 [REST API] REST API 的詳細資訊，請參閱 [REST API](rest-apis.md#speech-to-text)。 若要查看運作中的 REST API，可從 GitHub 下載 [REST API 範例](https://github.com/Azure-Samples/SpeechToText-REST)。

## <a name="next-steps"></a>後續步驟

- [請參閱 REST API 概觀](rest-apis.md)
