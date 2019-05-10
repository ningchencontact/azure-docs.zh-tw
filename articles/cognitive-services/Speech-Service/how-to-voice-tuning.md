---
title: 微調文字轉換語音輸出-語音服務
titleSuffix: Azure Cognitive Services
description: 啟用語音 SDK 中的記錄。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: erhopf
ms.openlocfilehash: 2352594b168f5d37ddb6a46064f9ac5801fb035f
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/09/2019
ms.locfileid: "65508396"
---
# <a name="fine-tune-text-to-speech-output"></a>微調文字轉換語音輸出

Azure 的語音服務可讓您調整速度、 發音、 磁碟區、 音調、 和分佈的輸出使用的文字轉換語音[語音合成標記語言 (SSML)](speech-synthesis-markup.md)。 SSML 是一種以 XML 為基礎的標記語言，會使用標記來通知哪些功能需要調整服務。 SSML 訊息然後傳送到文字轉換語音服務的每個要求的主體中。 若要簡化自訂程序，語音服務現在提供[語音微調](https://aka.ms/voicetuning)即時的工具，可讓您以視覺化方式檢查和微調文字轉換語音輸出。

微調語音工具支援 Microsoft[標準](language-support.md#standard-voices)，[類神經](language-support.md#text-to-speech)，並[自訂語音](how-to-customize-voice-font.md)。

## <a name="get-started-with-the-voice-tuning-tool"></a>開始使用語音微調工具

您可以開始微調語音微調工具的文字轉換語音輸出之前，您必須完成下列步驟：

1. 建立[免費的 Microsoft 帳戶](https://account.microsoft.com/account)如果您還沒有其中一個。
2. 建立[免費的 Azure 帳戶](https://azure.microsoft.com/free/)如果您還沒有其中一個。 按一下 **免費開始**，並建立新的 Azure 帳戶，使用您的 Microsoft 帳戶。

3. 在 Azure 入口網站中的語音服務的訂用帳戶。 逐步指示[如何建立語音資源](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started#create-a-speech-resource-in-azure)可用。
   >[!NOTE]
   >當您在 Azure 入口網站中建立語音資源時，Azure 位置資訊必須符合與 TTS 語音區域。 類神經 TTS 語音支援的子集合的 Azure 位置。 如需支援的完整清單，請參閱 <<c0> [ 區域](regions.md#text-to-speech)。

   >[!NOTE]
   >您需要有 F0 或 S0 金鑰建立在 Azure 入口網站中，您才能使用服務。 語音微調**不**支援[30 天免費試用版金鑰](https://review.docs.microsoft.com/en-us/azure/cognitive-services/speech-service/get-started?branch=release-build-cogserv-speech-services#free-trial)。

4. 登入[語音微調](https://aka.ms/voicetuning)入口網站中，並連接您的語音服務訂用帳戶。 選擇單一語音服務的訂用帳戶，然後建立專案。
5. 選取 **新的微調**。 接著，遵循下列步驟：

   * 找出並選取**所有訂用帳戶**。  
   * 選取 [連線到現有的訂用帳戶]。  
     ![連接現有的訂用帳戶](./media/custom-voice/custom-voice-connect-subscription.png)。
   * 輸入您 Azure 語音服務的訂用帳戶金鑰，然後選取**新增**。 您的訂用帳戶金鑰可用於語音自訂入口網站中，從[訂用帳戶頁面](https://go.microsoft.com/fwlink/?linkid=2090458)。 您也可以從 [資源管理] 窗格中來取得索引鍵[Azure 入口網站](https://portal.azure.com/)。 
   * 如果您有多個語音服務訂用帳戶，您打算使用時，請為每個訂用帳戶重複這些步驟。

## <a name="customize-the-text-to-speech-output"></a>自訂文字轉換語音輸出

既然您已建立帳戶並連結您的訂用帳戶，您可以開始微調文字轉換語音輸出。

1. 選擇一種聲音。
2. 輸入您想要編輯的文字。
3. 在開始進行編輯之前，播放的音訊輸出的感覺。
4. 選取您想要調整，在 word/句子並開始試驗不同的 SSML 函式。

>[!TIP]
> 如需調整 SSML 和微調語音輸出的詳細資訊，請參閱[語音合成標記語言 (SSML)](speech-synthesis-markup.md)。

## <a name="limitations"></a>限制

類神經語音微調會稍有不同的標準和自訂的語音微調。

* 聲調不支援的類神經的語音。
* 字距和磁碟區的功能只適用於完整的句子。 無法在 word 層級使用這些功能。
* 率，以一些類神經的語音，都可根據單字，而有些則需要您選取整個句子進行微調。

> [!TIP]
> 微調語音工具提供功能和調整的相關內容資訊。

## <a name="next-steps"></a>後續步驟
* [在 Azure 中建立語音資源](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started#create-a-speech-resource-in-azure)
* [開始微調語音](https://speech.microsoft.com/app.html#/VoiceTuning)
* [語音合成標記語言 (SSML)](speech-synthesis-markup.md) \(英文\)
