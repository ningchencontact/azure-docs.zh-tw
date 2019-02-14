---
title: 資料轉換
titleSuffix: Language Understanding - Azure Cognitive Services
description: 了解在 Language Understanding (LUIS) 中的預測之前如何改變語句
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 01/16/2019
ms.author: diberry
ms.openlocfilehash: bdf2d87e558726937fa4221a0d95179216c66051
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/07/2019
ms.locfileid: "55859195"
---
# <a name="convert-data-format-of-utterances"></a>轉換語句的資料格式
LUIS 會使用認知服務語音服務，在預測之前將口語語句轉換成文字語句。 

## <a name="speech-to-intent-conversion-concepts"></a>語音意圖轉換概念
LUIS 的語音轉換文字功能可讓您將口頭語句傳送到端點並接收 LUIS 預測回應。 此流程整合了[語音](https://docs.microsoft.com/azure/cognitive-services/Speech)服務與 LUIS。 透過[教學課程](../speech-service/how-to-recognize-intents-from-speech-csharp.md)，深入了解語音轉換到意圖的更多資訊。

### <a name="key-requirements"></a>重要需求
您不必為此整合建立 **Bing 語音 API** 金鑰。 在 Azure 入口網站中建立的 **Language Understanding** 金鑰適用於此整合。 請勿使用 LUIS 入門金鑰。

### <a name="pricing-tier"></a>定價層
此整合會使用與一般 Language Understanding 定價層不同的[定價](luis-boundaries.md#key-limits)模型。 

### <a name="quota-usage"></a>配額使用方式
相關資訊請參閱[重要限制](luis-boundaries.md#key-limits)。 

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [使用語音轉換文字](luis-tutorial-speech-to-intent.md)

