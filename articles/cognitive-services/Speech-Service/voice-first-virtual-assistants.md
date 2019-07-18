---
title: 自訂語音優先虛擬助理 (預覽)-語音服務
titleSuffix: Azure Cognitive Services
description: 概述使用 Bot Framework 上的 Direct Line Speech 通道和認知服務語音軟體發展工具組 (SDK) 的自訂語音優先虛擬助理的功能、功能和限制。
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: travisw
ms.openlocfilehash: c9b85895e701ff74e025d6f7895bc32513bc29a1
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2019
ms.locfileid: "67806894"
---
# <a name="about-custom-voice-first-virtual-assistants-preview"></a>關於自訂語音優先虛擬助理預覽

使用 Azure 語音服務自訂虛擬助理，賦予開發人員建立自然、擬人的對話介面，供應用程式和體驗之用。 Bot Framework 的 Direct Line Speech 頻道提供相容 Bot 有組織且協調的進入點，具備低延遲性和高可靠性的互動語音功能。 這些 bot 可以使用 Microsoft 的 Language Understanding (LUIS) 來進行自然語言互動。 Direct Line 語音是由使用語音軟體發展工具組 (SDK) 的裝置存取。

   ![Direct line speech 協調流程服務流程的概念圖](media/voice-first-virtual-assistants/overview.png "語音通道流程")


自訂語音優先虛擬助理的 Direct Line 語音和其相關功能, 是[虛擬助理解決方案和企業範本](https://docs.microsoft.com/azure/bot-service/bot-builder-enterprise-template-overview)的理想補充。 雖然 Direct Line Speech 可以與任何相容的 bot 搭配運作, 但這些資源提供高品質對話體驗可重複使用的基準, 以及快速入門的常見支援技能和模型。


## <a name="core-features"></a>核心功能

| Category | 功能 |
|----------|----------|
|[自訂喚醒字](speech-devices-sdk-create-kws.md) | 您可以讓使用者使用自訂關鍵字 (例如「嗨 Contoso」) 開始與 bot 進行對話。 這項工作是使用語音 SDK 中的自訂喚醒字引擎來完成, 它可以使用[您可以在這裡產生](speech-devices-sdk-create-kws.md)的自訂喚醒字來設定。 Direct Line 語音通道包含服務端喚醒字驗證, 可改善喚醒字啟用與裝置本身的精確度。
|[語音轉換文字](speech-to-text.md) | Direct Line 語音通道會使用 Azure 語音服務中的語音轉換文字[,](speech-to-text.md)將音訊的即時轉譯為已辨識的文字。 此文字可供您的 bot 和用戶端應用程式在轉譯時使用。
|[文字轉換語音](text-to-speech.md) | 來自 bot 的文字回應會使用 Azure 語音服務中的[文字轉換語音](text-to-speech.md)進行合成。 然後, 您的用戶端應用程式會將此合成作為音訊串流。 Microsoft 提供建立您自己的自訂高品質神經 TTS 語音的功能, 為您的品牌提供語音, 以深入瞭解如何[與我們聯繫](mailto:mstts@microsoft.com)。
|[Direct Line 語音](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech) | Direct Line Speech 是 Bot Framework 內的通道, 可讓您的用戶端應用程式、相容的 Bot 和 Azure 語音服務的功能之間順暢且順暢地連線。 如需有關將 bot 設定為使用 Direct Line 語音通道的詳細資訊, 請參閱[Bot Framework 檔中的頁面](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech)。

## <a name="sample-code"></a>範例程式碼

建立語音優先虛擬助理的範例程式碼可在 GitHub 上取得。 這些範例涵蓋了用戶端應用程式, 可讓您以數種熱門的程式設計語言連接到您的 bot。

* [語音優先虛擬助理範例 (SDK)](https://aka.ms/csspeech/samples)
* [快速入門: 語音優先虛擬助理 (C#)](quickstart-virtual-assistant-csharp-uwp.md)
* [快速入門: 語音優先虛擬助理 (JAVA)](quickstart-virtual-assistant-java-jre.md)
* [Direct Line 語音用戶端C#(、WPF)](https://github.com/Azure-Samples/Cognitive-Services-Direct-Line-Speech-Client)

## <a name="customization"></a>自訂

使用 Azure 語音服務建立的語音優先虛擬助理可以使用適用于[語音轉換文字](speech-to-text.md)、[文字轉換語音](text-to-speech.md)及[自訂關鍵字選取](speech-devices-sdk-create-kws.md)範圍的完整自訂選項。

> [!NOTE]
> 自訂選項會因語言/地區設定而有所不同 (請參閱[支援的語言](supported-languages.md))。

## <a name="reference-docs"></a>參考文件

* [語音 SDK](speech-sdk-reference.md)
* [Azure Bot 服務](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0)

## <a name="next-steps"></a>後續步驟

* [免費取得語音服務的訂用帳戶金鑰](get-started.md)
* [取得語音 SDK](speech-sdk.md)
* [建立及部署基本 Bot](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-basic-deploy?view=azure-bot-service-4.0)
* [取得虛擬助理解決方案和企業範本](https://github.com/Microsoft/AI)
