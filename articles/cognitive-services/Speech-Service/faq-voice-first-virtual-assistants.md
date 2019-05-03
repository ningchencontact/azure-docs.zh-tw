---
title: 直接列語音相關的常見問題集
titleSuffix: Azure Cognitive Services
description: 取得語音第一個虛擬助理使用直接列語音通道最受歡迎的疑問的解答。
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: travisw
ms.openlocfilehash: 16e4bcbb1514cfd5bbddc22b663d636292095231
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/02/2019
ms.locfileid: "65026724"
---
# <a name="voice-first-virtual-assistants-preview-frequently-asked-questions"></a>語音第一個虛擬助理預覽：常見問題集

如果您在這份文件中找不到問題的解答，請參閱[其他支援選項](support.md)。

## <a name="general"></a>一般

**问：什麼是直接列語音？**

**答：**`SpeechBotConnector`語音 SDK 中提供雙向 (bidirectional) 來連線到直接列語音通道 Bot Framework 上的機器人的非同步通訊。 這個通道提供協調的存取語音轉換文字和文字轉換語音，從 Azure 語音服務，可讓 bot 以成為完整中的語音、 語音出交談式體驗。 網路喚醒的字 」 和 「 線上醒機 Word 驗證的支援，此方案讓您能夠建置可高度自訂的語音第一個虛擬小幫手，為您的品牌或產品。

**问：如何開始使用？**

**答：** 最佳的方式開始建立語音第一個虛擬助理會開始[建立基本的 Bot Framework bot](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-basic-deploy?view=azure-bot-service-4.0)。 接下來，連接到您的機器人[直接列語音通道](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech.md)。

## <a name="debugging"></a>Debugging

**问：連接時，我會收到 401 錯誤，並執行任何動作的運作方式。我知道我的語音訂用帳戶金鑰無效。發生什麼情況？**

**答：** 在預覽中，直接列語音會有非常特定的限制的訂用帳戶上。 請確定您使用**語音**（Microsoft.CognitiveServicesSpeechServices，"Speech"） 的資源並*不***認知服務**資源 (Microsoft.CognitiveServicesAllInOne，「 所有認知服務 」)。 此外，請注意，只有**westus2**目前支援的地區。

![更正訂用帳戶的直接線路語音](media/voice-first-virtual-assistants/faq-supported-subscription.png "相容的語音訂用帳戶的範例")

**问：我回去辨識文字從直接列語音，但我看到 '1011' 錯誤、 什麼都沒有從我的機器人。原因為何？**

**答：** 此錯誤表示 bot 和直接列語音間的通訊問題。 請確定您已[連線直接列語音通道](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech.md)，[新增串流處理通訊協定支援](https://aka.ms/botframework/addstreamingprotocolsupport)您的 bot （具有相關 Web 通訊端的支援），和您的 bot 會回應傳入的然後核取從通道的要求。

**问：這仍然無法運作及/或使用 SpeechBotConnector 時收到不同的錯誤並不清楚我怎麼。什麼*應該*我嗎？**

**答：** 檔案為基礎的記錄會提供更多詳細資料，並可協助加速的支援要求。 若要啟用此功能，請參閱[如何使用檔案記錄](how-to-use-logging.md)。

## <a name="next-steps"></a>後續步驟

* [疑難排解](troubleshooting.md)
* [版本資訊](releasenotes.md)
