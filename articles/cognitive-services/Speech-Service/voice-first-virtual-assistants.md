---
title: 自訂語音第一個虛擬助理 （預覽）-語音服務
titleSuffix: Azure Cognitive Services
description: 功能、 功能和限制的自訂語音第一個虛擬助理使用 Bot Framework 和認知服務語音軟體開發套件 (SDK) 的直接列語音通道的概觀。
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: travisw
ms.custom: ''
ms.openlocfilehash: f40e53c67f099fea739e45f6bdc6963ee1e235cb
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/02/2019
ms.locfileid: "65026756"
---
# <a name="about-custom-voice-first-virtual-assistants-preview"></a>關於自訂的語音第一個虛擬助理預覽

使用 Azure 語音服務的自訂虛擬助理，讓開發人員建立自然、 擬人之交談的介面，其應用程式和體驗。 Bot Framework 直接列語音通道會提供相容機器人的框架，可讓具備低延遲和高可靠性等特性的互動語音中語音的協調、 協調的進入點，以增強這些功能。 Microsoft 的 Language Understanding (LUIS) 可以使用這些 bot 自然語言互動。 直接列語音使用語音服務軟體開發套件 (SDK) 的裝置來存取。

   ![直線語音協調流程服務流程的概念圖](media/voice-first-virtual-assistants/overview.png "語音通道流程")

直接列語音和其相關聯的功能，為自訂的語音第一個虛擬助理是理想補充[虛擬助理 （jlca） 解決方案](https://docs.microsoft.com/azure/bot-service/bot-builder-virtual-assistant-introduction)並[Enterprise 範本](https://docs.microsoft.com/azure/bot-service/bot-builder-enterprise-template-overview)。 雖然直接列語音可以使用任何相容的 bot 時，這些資源會提供可重複使用的基準的高品質的交談式體驗，以及常見的支援技術和使用者快速入門的模型。

## <a name="core-features"></a>核心功能

| Category | 特性 |
|----------|----------|
|[自訂的網路喚醒 word](speech-devices-sdk-create-kws.md) | 您可以讓使用者以使用自訂的關鍵字 「 嘿，Contoso。 」 類似的機器人開始交談 完成這項工作與自訂的網路喚醒 word 引擎在語音 SDK 中，可以設定以文字做為自訂的網路喚醒[您可以在這裡產生](speech-devices-sdk-create-kws.md)。 直接列語音通道包括更為精確的線上醒機 word 啟用，與單獨裝置的服務端喚醒文字驗證。
|[語音轉換文字](speech-to-text.md) | 直接列語音通道包含音訊的已辨識的文字所使用的即時轉譯[語音轉換文字](speech-to-text.md)Azure 語音服務。 因為它謄寫，此文字是可供您的機器人和用戶端應用程式。
|[文字轉換語音](text-to-speech.md) | 文字的回應，從您的 bot 將會使用綜合[文字轉換語音](text-to-speech.md)Azure 語音服務。 此合成然後都可以當做音訊資料流，用戶端應用程式。 Microsoft 可讓您建置您自己[自訂、 高品質的類神經 TTS 語音](https://aka.ms/customneuraltts)，可讓您的品牌聲音。
|[直線語音](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech.md) | Bot Framework 內的通道，直接列語音可讓順利且流暢地與之間的連線用戶端應用程式、 相容的 bot，Azure 語音服務的功能。 如需有關如何設定您的 bot，以使用直接列語音通道的詳細資訊，請參閱 < [Bot Framework 文件中的其頁面](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech.md)。

## <a name="sample-code"></a>範例程式碼

建立語音第一個虛擬助理的範例程式碼位於 GitHub 上。 這些範例涵蓋連線到您的 bot 為數種熱門的程式設計語言中的用戶端應用程式。

* [語音第一個虛擬助理範例 (SDK)](https://aka.ms/csspeech/samples)
* [快速入門： 語音優先虛擬助理 (C#)](quickstart-virtual-assistant-csharp-uwp.md)
* [快速入門： 語音第一個虛擬助理 (Java)](quickstart-virtual-assistant-java-jre.md)

## <a name="customization"></a>自訂

語音第一個虛擬助理建置使用 Azure 語音服務可以使用完整的自訂選項，可供[語音轉換文字](speech-to-text.md)，[文字轉換語音](text-to-speech.md)，和[custom 關鍵字選取項目](speech-devices-sdk-create-kws.md)。

> [!NOTE]
> 語言/地區而異的自訂選項 (請參閱[支援的語言](supported-languages.md))。

## <a name="reference-docs"></a>參考文件

* [語音 SDK](speech-sdk-reference.md)
* [Azure Bot 服務](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0)

## <a name="next-steps"></a>後續步驟

* [免費取得語音服務的訂用帳戶金鑰](get-started.md)
* [取得語音 SDK](speech-sdk.md)
* [建立及部署基本 Bot](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-basic-deploy?view=azure-bot-service-4.0)
* [取得虛擬助理 （jlca） 的方案和 Enterprise 範本](https://github.com/Microsoft/AI)
