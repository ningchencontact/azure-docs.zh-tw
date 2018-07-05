---
title: 語音服務區域 | Microsoft Docs
description: 語音服務區域的參考。
services: cognitive-services
author: mahilleb-msft
manager: wolmfa61
ms.service: cognitive-services
ms.technology: speech
ms.topic: article
ms.date: 06/27/2018
ms.author: mahilleb
ms.openlocfilehash: a201cc043f673e2285ea48950804d97b96f881ed
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/27/2018
ms.locfileid: "37054878"
---
# <a name="regions-and-endpoints-of-the-speech-service"></a>語音服務的區域與端點

> [!NOTE]
> [語音 SDK](speech-sdk.md) 中的區域名稱符合下列網域端點的第一個部分。
> 例如，使用 `westus` 可在語音 SDK 中指定美國西部區域。

## <a name="speech-to-text"></a>語音轉換文字

[!include[](includes/endpoints-speech-to-text.md)]

## <a name="text-to-speech"></a>文字轉語音

[!include[](includes/endpoints-text-to-speech.md)]

## <a name="authentication"></a>驗證

[!include[](includes/endpoints-token-service.md)]

如需取得與重新整理授權權杖的詳細資料，請參閱[這裡](rest-apis.md#authentication)。

## <a name="language-understanding-speech-sdk-only"></a>Language Understanding (僅限語音 SDK)

Language Understanding 服務的區域列於[此處](/azure/cognitive-services/luis/luis-reference-regions)。
在語音 SDK 中，請透過端點網域名稱的第一個部分 (例如，`westus`) 指定這些區域。
