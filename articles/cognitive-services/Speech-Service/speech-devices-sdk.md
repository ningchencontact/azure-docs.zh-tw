---
title: 關於語音裝置 SDK - 語音服務
titleSuffix: Azure Cognitive Services
description: 開始使用語音裝置 SDK。 語音服務可與各種裝置和音訊來源搭配運作。 您現在可以利用相符的硬體和軟體，將語音應用程式提升至下一個層級。 語音裝置 SDK 是已預先微調的程式庫，可搭配有目的建置的麥克風陣列開發套件。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 341f35d04c7f3f591198acbbf05ac32afe84e95c
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/10/2019
ms.locfileid: "67718537"
---
# <a name="about-the-speech-devices-sdk"></a>關於語音裝置 SDK

[語音服務](overview.md)搭配各種不同的裝置和音訊的來源。 您現在可以利用相符的硬體和軟體，將語音應用程式提升至下一個層級。 語音裝置 SDK 是已預先微調的程式庫，可搭配有目的建置的麥克風陣列開發套件。

語音裝置 SDK 可協助您：

* 快速測試新的語音案例。
* 更輕鬆地將雲端式語音服務整合到您的裝置中。
* 為客戶建立卓越的使用者體驗。

語音裝置 SDK 會取用[語音 SDK](speech-sdk.md)， 它會使用 Speech SDK 傳送由我們的進階音訊處理演算法，從裝置的麥克風陣列，若要處理的音訊[語音服務](overview.md)。 其可使用多聲道音訊，透過噪音抑制、回音消除、波束成形和消除迴響，來提供更精確的遠場[語音辨識](speech-to-text.md)。

您也可以使用語音裝置 SDK 來建置環境有自己的裝置[自訂喚醒 word](speech-devices-sdk-create-kws.md)因此是唯一您的品牌的提示，會起始使用者互動。

語音裝置 SDK 有助於各種啟用語音的案例，例如[自訂語音第一個虛擬助理](https://aka.ms/bots/speech/va)、 磁碟機透過訂購系統[交談文字記錄](conversation-transcription-service.md)，和智慧說話者。 您可以利用文字來回應使用者、以預設或[自訂語音](how-to-customize-voice-font.md)回答他們、提供搜尋結果、[翻譯](speech-translation.md)成其他語言等等。 我們期待看到您所打造的不凡成果！

## <a name="get-the-speech-devices-sdk"></a>取得語音裝置 SDK

### <a name="android"></a>Android

適用於 Android 裝置會下載最新版[Android 語音裝置 SDK](https://aka.ms/sdsdk-download-android)。

### <a name="windows"></a>視窗

Windows 的跨平台的 Java 應用程式提供的範例應用程式。 下載最新版[JRE 語音裝置 SDK](https://aka.ms/sdsdk-download-JRE)。
應用程式已內建此語音 SDK 封裝，並在 64 位元 Windows 上的 Eclipse Java IDE (v4)。 它會在 64 位元 Java 8 Runtime Environment (JRE) 上執行。

### <a name="linux"></a>Linux

適用於 Linux 的跨平台的 Java 應用程式提供的範例應用程式。 下載最新版[JRE 語音裝置 SDK](https://aka.ms/sdsdk-download-JRE)。
應用程式已內建的語音 SDK 套件及 64 位元 Linux （Ubuntu 16.04，Ubuntu 18.04，Debian 9） 上的 Eclipse Java IDE (v4)。 它會在 64 位元 Java 8 Runtime Environment (JRE) 上執行。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [選擇您的語音裝置](get-speech-devices-sdk.md)
>
> [!div class="nextstepaction"]
> [免費取得語音服務的訂用帳戶金鑰](get-started.md)
