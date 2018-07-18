---
title: 翻譯的範例 | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: 以下是語音翻譯的範例。
services: cognitive-services
author: wolfma61
manager: onano
ms.service: cognitive-services
ms.technology: Speech
ms.topic: article
ms.date: 06/07/2018
ms.author: wolfma
ms.openlocfilehash: 1151628ed695e74e2196c20b08e33fa5eaf33282
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/27/2018
ms.locfileid: "37028382"
---
# <a name="sample-for-translation"></a>翻譯的範例

> [!NOTE]
> 如需下載這個範例和其他範例的指示，請參閱[語音 SDK 的範例](samples.md)。

[!include[Get a Subscription Key](includes/get-subscription-key.md)]

> [!NOTE]
> 以下所有範例都應該具備下列最上層宣告：
>
> [!code-csharp [Using Statements](~/samples-cognitive-services-speech-sdk/Windows/csharp_samples/translation_samples.cs#toplevel)]
>
> - - -

## <a name="translation-using-the-microphone"></a>使用麥克風的翻譯

下列程式碼片段示範如何將英文的語音輸入翻譯為德文，同時取得已翻譯文字的語音輸出。 它會使用麥克風。

[!code-csharp[Translation Using Microphone](~/samples-cognitive-services-speech-sdk/Windows/csharp_samples/translation_samples.cs#TranslationWithMicrophoneAsync)]

- - -

## <a name="translation-using-file-input"></a>使用檔案輸入的翻譯

下列程式碼片段示範如何從英文的語音輸入翻譯為德文和法文。
它會使用檔案作為輸入。

[!code-csharp[Translation Using File Input](~/samples-cognitive-services-speech-sdk/Windows/csharp_samples/translation_samples.cs#TranslationWithFileAsync)]

- - -

## <a name="sample-source-code"></a>範例原始程式碼

最新版的範例和更多進階的範例位於專用的 [GitHub 存放庫](https://github.com/Azure-Samples/cognitive-services-speech-sdk)中。

## <a name="next-steps"></a>後續步驟

- [語音辨識](./speech-to-text-sample.md)

- [意圖辨識](./intent.md)
