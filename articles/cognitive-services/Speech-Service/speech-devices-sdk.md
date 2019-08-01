---
title: 關於語音裝置 SDK-語音服務
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
ms.openlocfilehash: f2819dd2194193ffa8171034fdbe01ac4cb26174
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/26/2019
ms.locfileid: "68558926"
---
# <a name="about-the-speech-devices-sdk"></a>關於語音裝置 SDK

[語音服務](overview.md)可與各種裝置和音訊來源搭配使用。 您現在可以利用相符的硬體和軟體，將語音應用程式提升至下一個層級。 語音裝置 SDK 是已預先微調的程式庫，可搭配有目的建置的麥克風陣列開發套件。

語音裝置 SDK 可協助您：

* 快速測試新的語音案例。
* 更輕鬆地將雲端式語音服務整合到您的裝置。
* 為客戶建立卓越的使用者體驗。

語音裝置 SDK 會取用[語音 SDK](speech-sdk.md)， 它會使用語音 SDK 將我們的先進音訊處理演算法所處理的音訊, 從裝置的麥克風陣列傳送至[語音服務](overview.md)。 其可使用多聲道音訊，透過噪音抑制、回音消除、波束成形和消除迴響，來提供更精確的遠場[語音辨識](speech-to-text.md)。

您也可以使用語音裝置 SDK 來建立具有[自訂喚醒字](speech-devices-sdk-create-kws.md)的環境裝置, 讓起始使用者互動的提示與您的品牌獨一無二。

語音裝置 SDK 可協助各種啟用語音的案例, 例如[自訂語音優先虛擬助理](https://aka.ms/bots/speech/va)、磁片磁碟機排序系統、[交談](conversation-transcription-service.md)轉譯和智慧型喇叭。 您可以利用文字來回應使用者、以預設或[自訂語音](how-to-customize-voice-font.md)回答他們、提供搜尋結果、[翻譯](speech-translation.md)成其他語言等等。 我們期待看到您所打造的不凡成果！

## <a name="get-the-speech-devices-sdk"></a>取得語音裝置 SDK

### <a name="android"></a>Android

針對 Android 裝置, 請下載最新版本的[Android 語音裝置 SDK](https://aka.ms/sdsdk-download-android)。

### <a name="windows"></a>Windows

針對 Windows, 範例應用程式會以跨平臺 JAVA 應用程式的形式提供。 下載最新版的[JRE 語音裝置 SDK](https://aka.ms/sdsdk-download-JRE)。
此應用程式是使用語音 SDK 套件，以及 64 位元 Windows 上的 Eclipse Java IDE (v4.8) 來建置。 它會在 64 位元 Java 8 Runtime Environment (JRE) 上執行。

### <a name="linux"></a>Linux

針對 Linux, 範例應用程式會以跨平臺 JAVA 應用程式的形式提供。 下載最新版的[JRE 語音裝置 SDK](https://aka.ms/sdsdk-download-JRE)。
此應用程式是使用語音 SDK 套件，以及 64 位元 Linux (Ubuntu 16.04、Ubuntu 18.04、Debian 9) 上的 Eclipse Java IDE (v4.8) 來建置。 它會在 64 位元 Java 8 Runtime Environment (JRE) 上執行。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [選擇您的語音裝置](get-speech-devices-sdk.md)
>
> [!div class="nextstepaction"]
> [免費取得語音服務的訂用帳戶金鑰](get-started.md)
