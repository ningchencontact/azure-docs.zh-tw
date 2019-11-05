---
title: 語音助理-語音服務
titleSuffix: Azure Cognitive Services
description: 概述使用語音軟體發展工具組（SDK）的語音助理的功能、功能和限制。
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: travisw
ms.openlocfilehash: c97f6414876441290cade68b8f9a054970586402
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2019
ms.locfileid: "73501016"
---
# <a name="about-voice-assistants"></a>關於語音助理

使用 Azure 語音服務的語音助理可讓開發人員為其應用程式和體驗建立自然、人類類似的對話介面。

語音助理服務可讓您在裝置與小幫手執行之間快速可靠地進行互動，使用（1） Bot Framework 的 Direct Line 語音通道，或（2）整合式自訂命令（預覽）服務來完成工作。

應用程式會使用語音軟體發展工具組（SDK）連接到語音助理服務。

   ![語音助理協調流程服務流程的概念圖](media/voice-assistants/overview.png "語音助理流程")

## <a name="core-features"></a>核心功能

| 類別 | 特性 |
|----------|----------|
|[Custom 關鍵字](speech-devices-sdk-create-kws.md) | 使用者可以使用自訂關鍵字（例如 "嗨 Contoso"）來開始與助理交談。 應用程式會使用語音 SDK 中的自訂關鍵字引擎來執行這項工作，您可以使用[這裡產生](speech-devices-sdk-create-kws.md)的自訂關鍵字來設定。 語音助理可以使用服務端關鍵字驗證來改善關鍵字啟用的精確度（與裝置本身的比較）。
|[語音轉換文字](speech-to-text.md) | 語音助理會使用 Azure 語音服務的[語音轉換文字，](speech-to-text.md)將即時音訊轉換成已辨識的文字。 這項文字已轉譯，可供您的小幫手和用戶端應用程式使用。
|[文字轉換語音](text-to-speech.md) | 來自您小幫手的文字回應會使用 Azure 語音服務中的[文字轉換語音](text-to-speech.md)進行合成。 然後，您的用戶端應用程式可以使用此合成作為音訊串流。 Microsoft 提供建立您自己的自訂高品質神經 TTS 語音的功能，讓您的品牌能夠發聲。 若要深入瞭解，請[洽詢我們](mailto:mstts@microsoft.com)。

## <a name="comparing-assistant-solutions"></a>比較 assistant 解決方案

語音助理服務會將您的裝置上應用程式連接到您的獨特助理執行。 開發人員可以使用（1） Bot Framework 的[Direct Line 語音](direct-line-speech.md)通道，或（2）[自訂命令（預覽）](custom-commands.md)解決方案，來撰寫語音助理。

   ![助理解決方案的比較](media/voice-assistants/assistant-solution-comparison.png "助理解決方案的比較")

| 方案 | 特性 |
|----------|----------|
|[自訂命令（預覽）](custom-commands.md) | 自訂命令（預覽）為語音助理提供簡化的撰寫和裝載解決方案。 它是針對工作完成和命令及控制案例的需求而量身打造。
|[Direct Line 語音](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech) | Direct Line 語音可讓（1）您的用戶端應用程式、（2）相容的 bot，以及（3） Azure 語音服務的功能順暢且順暢地連線。 如需有關將 bot 設定為使用 Direct Line 語音通道的詳細資訊，請參閱[Bot Framework 檔中的頁面](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech)。

一旦您使用上述其中一種解決方案建立了語音助理，請使用語音 SDK 中的 `DialogServiceConnector`，將您的裝置應用程式連線到該電腦。 如需詳細資訊，請參閱每個解決方案的快速入門和範例。

## <a name="getting-started-with-voice-assistants"></a>開始使用語音助理

我們提供的快速入門是為了讓您在10分鐘內執行程式碼而設計的。 此表格包含依語言組織的語音助理快速入門清單。

| 快速入門 | 平台 | API 參考資料 |
|------------|----------|---------------|
| C#，UWP | Windows | [Browse](https://aka.ms/csspeech/csharpref) |
| Java | Windows、macOS、Linux | [Browse](https://aka.ms/csspeech/javaref) |
| Java | Android | [Browse](https://aka.ms/csspeech/javaref) |

## <a name="sample-code"></a>範例程式碼

您可以在 GitHub 上取得用來建立語音助理的範例程式碼。 這些範例涵蓋了用來以數種熱門的程式設計語言連接到您的小幫手的用戶端應用程式。

* [語音助理範例（SDK）](https://aka.ms/csspeech/samples)
* [教學課程：使用語音 SDK 語音啟用您的助理C#](tutorial-voice-enable-your-bot-speech-sdk.md)

## <a name="tutorial"></a>教學課程

本教學課程說明如何[使用語音 SDK 和 Direct Line 語音通道](tutorial-voice-enable-your-bot-speech-sdk.md)，為您的小幫手提供語音。

## <a name="customization"></a>自訂

使用 Azure 語音服務建立的語音助理可以使用適用于[語音轉換文字](speech-to-text.md)、[文字轉換語音](text-to-speech.md)及[自訂關鍵字選取](speech-devices-sdk-create-kws.md)範圍的完整自訂選項。

> [!NOTE]
> 自訂選項會因語言/地區設定而有所不同（請參閱[支援的語言](supported-languages.md)）。

## <a name="reference-docs"></a>參考文件

* [語音 SDK](speech-sdk-reference.md)
* [Azure Bot 服務](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0)

## <a name="next-steps"></a>後續步驟

* [免費取得語音服務的訂用帳戶金鑰](get-started.md)
* [取得語音 SDK](speech-sdk.md)
* [深入瞭解自訂命令（預覽）](custom-commands.md)
* [深入瞭解 Direct Line Speech](direct-line-speech.md)