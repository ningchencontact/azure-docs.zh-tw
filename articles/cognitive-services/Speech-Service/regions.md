---
title: 區域 - 語音服務
titlesuffix: Azure Cognitive Services
description: 「語音服務」區域的參考。
services: cognitive-services
author: mahilleb-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: conceptual
ms.date: 01/14/2019
ms.author: mahilleb
ms.custom: seodec18
ms.openlocfilehash: d302fc67225213ad55687797c97c7a2a8a17cc6d
ms.sourcegitcommit: dede0c5cbb2bd975349b6286c48456cfd270d6e9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/16/2019
ms.locfileid: "54329502"
---
# <a name="speech-service-supported-regions"></a>語音服務支援的區域

「語音服務」可讓您的應用程式將音訊轉換為文字、執行語音翻譯，以及將文字轉換為語音。 此服務在多個區域中提供，針對語音 SDK 與 REST API 有唯一的端點。

確定您使用符合您訂用帳戶區域的端點。

## <a name="speech-sdk"></a>語音 SDK

在[語音服務 SDK](speech-sdk.md) 中，會以字串方式指定區域 (例如，在「適用於 C# 的語音 SDK」中，會作為 `SpeechConfig.FromSubscription` 的參數)。

### <a name="speech-recognition-and-translation"></a>語音辨識與翻譯

針對**語音辨識**與 **翻譯**，語音 SDK 在這些區域中可用：

  區域 | 語音 SDK 參數 | 語音自訂入口網站
 ------|-------|--------
 美國西部 | `westus` | https://westus.cris.ai
 美國西部 2 | `westus2` | https://westus2.cris.ai
 美國東部 | `eastus` | https://eastus.cris.ai
 美國東部 2 | `eastus2` | https://eastus2.cris.ai
 東亞 | `eastasia` | https://eastasia.cris.ai
 東南亞 | `southeastasia` | https://southeastasia.cris.ai
 北歐 | `northeurope` | https://northeurope.cris.ai
 西歐 | `westeurope` | https://westeurope.cris.ai


### <a name="intent-recognition"></a>意圖辨識

透過語音 SDK 進行**意圖辨識**的可用區域如下所示：

 全球區域 | 區域 | 語音 SDK 參數
 ------|-------|--------
 亞洲 | 東亞 | `eastasia`
 亞洲 | 東南亞 | `southeastasia`
 澳大利亞 | 澳洲東部 | `australiaeast`
 歐洲 | 北歐 | `northeurope`
 歐洲 | 西歐 | `westeurope`
 北美洲 | 美國東部 | `eastus`
 北美洲 | 美國東部 2 | `eastus2`
 北美洲 | 美國中南部 | `southcentralus`
 北美洲 | 美國中西部 | `westcentralus`
 北美洲 | 美國西部 | `westus`
 北美洲 | 美國西部 2 | `westus2`
 南美洲 | 巴西南部 | `brazilsouth`

這是 [Language Understanding 服務 (LUIS)](/azure/cognitive-services/luis/luis-reference-regions)所支援的發行區域子集。

## <a name="rest-apis"></a>REST API

語音服務也會針對語音轉換文字與文字轉換語音要求公開 REST 端點。

### <a name="speech-to-text"></a>語音轉文字

如需語音轉換文字參考文件，請參閱 [REST API](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis#speech-to-text)。

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-speech-to-text.md)]

### <a name="text-to-speech"></a>文字轉換語音

如需文字轉換語音參考文件，請參閱 [REST API](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis#speech-to-text)。

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-text-to-speech.md)]
