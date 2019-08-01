---
title: 微調文字到語音轉換輸出-語音服務
titleSuffix: Azure Cognitive Services
description: 在語音 SDK 中啟用記錄功能。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 4cf2338d76ce31f44eaf3fb235e5f8796602d819
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/26/2019
ms.locfileid: "68562740"
---
# <a name="fine-tune-text-to-speech-output"></a>微調文字轉換語音輸出

Azure 語音服務可讓您使用[語音合成標記語言 (SSML)](speech-synthesis-markup.md), 調整文字到語音轉換輸出的速度、發音、音量、音調和輪廓。 SSML 是以 XML 為基礎的標記語言, 會使用標記來通知服務需要微調的功能。 然後, 在每個要求的本文中, 會將 SSML 訊息傳送至文字轉換語音服務。 為了簡化自訂程式, 語音服務現在提供了[語音微調](https://aka.ms/voicetuning)工具, 可讓您以視覺化方式即時檢查和微調文字轉換語音的輸出。

語音微調工具支援 Microsoft 的[標準](language-support.md#standard-voices)、類[神經](language-support.md#text-to-speech)和[自訂語音](how-to-customize-voice-font.md)。

## <a name="get-started-with-the-voice-tuning-tool"></a>開始使用語音微調工具

您必須先完成下列步驟, 才能開始使用語音微調工具來微調文字轉換語音的輸出:

1. 建立[免費的 Microsoft 帳戶](https://account.microsoft.com/account)(如果您還沒有的話)。
2. 建立[免費的 Azure 帳戶](https://azure.microsoft.com/free/)(如果還沒有的話)。 按一下 [**開始免費**], 然後使用您的 Microsoft 帳戶建立新的 Azure 帳戶。

3. 在 Azure 入口網站中建立語音服務訂用帳戶。 提供[如何建立語音資源](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started#create-a-speech-resource-in-azure)的逐步指示。
   >[!NOTE]
   >當您在 Azure 入口網站中建立語音資源時, Azure 位置資訊必須與 TTS 語音區域相符。 類神經 TTS 語音支援 Azure 位置的子集合。 如需完整的支援清單, 請參閱[區域](regions.md#text-to-speech)。

   >[!NOTE]
   >您必須先在 Azure 入口網站中建立 F0 或 S0 索引鍵, 才能使用此服務。 語音微調**不**支援[30 天的免費試用金鑰](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started?branch=release-build-cogserv-speech-services#free-trial)。

4. 登入[語音調整](https://aka.ms/voicetuning)入口網站, 並聯機您的語音服務訂用帳戶。 選擇單一語音服務訂用帳戶, 然後建立專案。
5. 選取 [**新增微調**]。 接著，遵循下列步驟：

   * 找出並選取 [**所有訂閱**]。  
   * 選取 [連線到現有的訂用帳戶]  。  
     ![連接現有的訂](./media/custom-voice/custom-voice-connect-subscription.png)用帳戶。
   * 輸入您的 Azure 語音服務訂用帳戶金鑰, 然後選取 [**新增**]。 您的訂用帳戶金鑰可從 [訂用帳戶][頁面](https://go.microsoft.com/fwlink/?linkid=2090458)的語音自訂入口網站中取得。 您也可以從[Azure 入口網站](https://portal.azure.com/)中的 [資源管理] 窗格取得金鑰。
   * 如果您打算使用多個語音服務訂用帳戶, 請針對每個訂用帳戶重複這些步驟。

## <a name="customize-the-text-to-speech-output"></a>自訂文字到語音轉換輸出

現在您已建立帳戶並連結您的訂閱, 您可以開始微調文字轉換語音的輸出。

1. 選擇語音。
2. 輸入您要編輯的文字。
3. 開始進行編輯之前, 請先播放音訊以瞭解輸出。
4. 選取您想要精簡的文字/句子, 並開始使用不同的 SSML 型函數進行實驗。

>[!TIP]
> 如需調整 SSML 和調整語音輸出的詳細資訊, 請參閱[語音合成標記語言 (SSML)](speech-synthesis-markup.md)。

## <a name="limitations"></a>限制

類神經語音微調與標準和自訂語音的微調略有不同。

* 類神經語音不支援聲調。
* 音調和音量功能僅適用于完整的句子。 這些功能在 word 層級無法使用。
* 針對速率, 某些類神經語音可以根據單字進行微調, 而有些則需要您選取整個句子。

> [!TIP]
> 語音微調工具提供功能和微調的相關內容資訊。

## <a name="next-steps"></a>後續步驟
* [在 Azure 中建立語音資源](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started#create-a-speech-resource-in-azure)
* [開始語音微調](https://speech.microsoft.com/app.html#/VoiceTuning)
* [語音合成標記語言 (SSML)](speech-synthesis-markup.md) \(英文\)
