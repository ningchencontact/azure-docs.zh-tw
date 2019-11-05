---
title: 語音助理的相關常見問題
titleSuffix: Azure Cognitive Services
description: 使用自訂命令（預覽）或 Direct Line 語音訊道，取得語音助理最熱門問題的解答。
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: travisw
ms.openlocfilehash: 7ad3f932e9a10723d6cc1bae2fc4854c932d4c64
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2019
ms.locfileid: "73500896"
---
# <a name="voice-assistants-frequently-asked-questions"></a>語音助理：常見問題

如果您在本檔中找不到問題的解答，請參閱[其他支援選項](support.md)。

## <a name="general"></a>一般

**問：什麼是語音助理？**

**答：** 就像 Cortana 一樣，語音助理是接聽使用者說話語句的解決方案、分析那些語句的內容，並執行一或多個動作來回應語句的意圖，然後提供經常出現的使用者回應包含說話的元件。 這是與系統互動的「語音輸入」、「語音輸出」體驗。 語音助理作者使用語音 SDK 中的 `DialogServiceConnector` 來建立裝置上的應用程式，以便與使用[自訂命令（預覽）](custom-commands.md)或 Bot Framework [Direct Line Speech](direct-line-speech.md)通道所建立的助理進行通訊。 這些助理可以使用自訂關鍵字、自訂語音和自訂語音，來提供針對您的品牌或產品量身打造的經驗。

**問：我應該使用自訂命令（預覽）或 Direct Line 語音嗎？有何不同？**

**答：** [自訂命令（預覽）](custom-commands.md)是較低複雜度的工具組，可輕鬆建立並裝載適合工作完成案例的小幫手。 [Direct Line Speech](direct-line-speech.md)提供更豐富、更精密的功能，可啟用健全的對話案例。 如需詳細資訊，請參閱[assistant 解決方案的比較](voice-assistants.md#comparing-assistant-solutions)。

**問：如何開始使用？**

**答：** 開始建立自訂命令（預覽）應用程式或基本 Bot Framework bot 的最佳方式。

* [建立自訂命令（預覽）應用程式](quickstart-custom-speech-commands-create-new.md)
* [建立基本的 Bot Framework bot](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-basic-deploy?view=azure-bot-service-4.0)
* [將 bot 連線至 Direct Line 語音訊道](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech)

## <a name="debugging"></a>Debugging

**問：我在連接時收到401錯誤，沒有任何作用。我知道我的語音訂用帳戶金鑰有效。這是怎麼回事？**

**答：** 在 Azure 入口網站上管理您的訂用帳戶時，請確定您使用的是**語音**資源（CognitiveServicesSpeechServices，"speech"），而*不*是**認知服務**資源（CognitiveServicesAllInOne，"All 認知服務"）。 此外，請檢查[語音服務區域的語音助理支援](regions.md#voice-assistants)。

![direct line speech 的正確訂用帳戶](media/voice-assistants/faq-supported-subscription.png "相容語音訂用帳戶的範例")

**問：我從我的 `DialogServiceConnector`取得辨識文字，但我看到來自我的 bot 的「1011」錯誤和任何東西。因此?**

**答：** 此錯誤表示您的助理與語音助理服務之間發生通訊問題。

* 若為自訂命令（預覽），請確定您的自訂命令（預覽）應用程式已發佈
* 針對 Direct Line 語音，請確定您已將[bot 連線至 Direct Line 語音通道](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech)、將[串流通訊協定支援新增](https://aka.ms/botframework/addstreamingprotocolsupport)至您的 bot （具有相關的 Web 通訊端支援），然後檢查您的 bot 是否回應傳入的要求從通道。

**問：此程式碼仍然無法正常執行，並（或）在使用 `DialogServiceConnector`時收到不同的錯誤。我該怎麼做？**

**答：** 以檔案為基礎的記錄提供更多詳細資料，並有助於加速支援要求。 若要啟用此功能，請參閱[如何使用檔案記錄](how-to-use-logging.md)。

## <a name="next-steps"></a>後續步驟

* [疑難排解](troubleshooting.md)
* [版本資訊](releasenotes.md)
