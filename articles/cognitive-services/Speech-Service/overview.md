---
title: 什麼是語音服務 (預覽)？
description: 語音服務 (屬於 Microsoft 認知服務的一部分) 聯合了先前個別提供的數個 Azure 語音服務：Bing 語音 (包含語音辨識和文字轉換語音)、自訂語音和語音翻譯。
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 05/07/2018
ms.author: v-jerkin
ms.openlocfilehash: 60ff2f71766a14af17ebb1cb9d20825976471296
ms.sourcegitcommit: 1aedb52f221fb2a6e7ad0b0930b4c74db354a569
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/17/2018
ms.locfileid: "42093848"
---
# <a name="what-is-the-speech-service-preview"></a>什麼是語音服務 (預覽)？

利用一個訂用帳戶，語音服務可讓開發人員輕鬆地為其應用程式新增強大的語音功能。 您的應用程式現在可以提供語音命令、轉譯、聽寫、語音合成和語音翻譯功能。

語音服務是由其他 Microsoft 產品 (包括 Cortana 和 Microsoft Office) 中使用的技術所提供。

> [!NOTE]
> 語音服務目前為公用預覽狀態。 定期返回這裡，以取得文件更新、額外的程式碼範例等等。

## <a name="speech-service-features"></a>語音服務功能

|函式|說明|
|-|-|
|[語音轉文字](speech-to-text.md)| 將音訊串流轉譯為文字，以便應用程式接受作為輸入。 也可與 [Language Understanding 服務](https://docs.microsoft.com/azure/cognitive-services/luis/) (LUIS) 整合，從語句衍生使用者意圖。|
|[文字轉換語音](text-to-speech.md)| 將純文字轉換成自然發音語音，並以音訊檔案傳遞到您的應用程式。 針對多種支援的語言提供多種聲音 (因性別或腔調而異)。 |
|[語音翻譯](speech-translation.md)| 可用來翻譯接近即時的串流音訊，或處理錄製的語音。 |
|自訂語音轉換文字|您可以藉由建立您自己的[原音](how-to-customize-acoustic-models.md)和[語言](how-to-customize-language-model.md)模型，並指定自訂[發音](how-to-customize-pronunciation.md)規則，來自訂語音轉換文字。 |
|[自訂文字轉換語音](how-to-customize-voice-font.md)|您可以建立您自己的語音以供文字轉換語音使用。|
|[語音裝置 SDK](speech-devices-sdk.md)| 在推出統一語音服務後，Microsoft 和合作夥伴會提供整合式硬體/軟體平台，此平台已針對開發語音裝置進行最佳化 |

## <a name="access-to-the-speech-service"></a>存取語音服務

語音服務可以用兩種方式提供。 [SDK](speech-sdk.md) 會擷取網路通訊協定的詳細資料，讓您更輕鬆地在支援的平台上進行開發。 [REST API](rest-apis.md) 可搭配任何程式設計語言運作，但不會提供 SDK 所提供的所有函式。

|<br>方法|語音<br>轉換文字|文字轉換<br>語音|語音<br>翻譯|<br>說明|
|-|-|-|-|-|
|[SDK](speech-sdk.md)|是|否|是|適用於特定程式設計語言的程式庫，使用以 Websocket 為基礎的通訊協定來簡化開發作業。|
|[REST](rest-apis.md)|是|yes|否|以 HTTP 為基礎的簡單 API，可讓您輕鬆地將語音新增至您的應用程式。|

## <a name="speech-scenarios"></a>語音案例

下面有一些語音技術常見用法的簡單討論。 [語音 SDK](speech-sdk.md) 是大多數案例的核心。

> [!div class="checklist"]
> * 建立語音觸發式應用程式
> * 謄寫話務中心錄音
> * 實作語音聊天機器人

### <a name="voice-triggered-apps"></a>語音觸發式應用程式

語音輸入可讓應用程式富有彈性、能夠免持，且快速使用。 在具有語音功能的應用程式中，使用者可直接要求所需的資訊，而不需要透過按一下或點選來瀏覽。

如果應用程式的適用對象是一般大眾，您可以使用語音服務所提供的基準語音辨識模型。 它能夠辨識典型環境下各種不同的說話者。

如果應用程式會用於特定領域 (例如醫藥或 IT)，您可以建立[語言模型](how-to-customize-language-model.md)，讓語音服務學習應用程式所使用的特殊術語。

如果應用程式會用於吵雜環境 (例如工廠)，您可以建立自訂[原音模型](how-to-customize-acoustic-models.md)，讓語音服務有更好的語音/雜訊辨別能力。

要開始使用很簡單，只要下載[語音 SDK](speech-sdk.md) 並遵循相關[快速入門](quickstart-csharp-dotnet-windows.md)文章即可。

### <a name="transcribe-call-center-recordings"></a>謄寫話務中心錄音

我們通常只會在通話發生問題時，才會回頭聽取話務中心的錄音內容。 有了語音服務，將錄音謄寫為文字便很容易。 謄寫為文字後，便可輕鬆地對文字建立索引以進行[全文檢索搜尋](https://docs.microsoft.com/azure/search/search-what-is-azure-search)，也可套用[文字分析](https://docs.microsoft.com/azure/cognitive-services/Text-Analytics/)來偵測情感、語言和關鍵片語。

如果話務中心錄音通常包含專有術語 (例如，產品名稱或 IT 行話)，則可以建立[語言模型](how-to-customize-language-model.md)讓語音服務學習這些詞彙。 自訂[原音模型](how-to-customize-acoustic-models.md)可協助語音服務了解較差的電話連線。

如需此案例的詳細資訊，請深入了解語音服務的[批次謄寫](batch-transcription.md)。

### <a name="voice-bots"></a>語音聊天機器人

[聊天機器人](https://dev.botframework.com/)日漸成為讓使用者獲得其所需資訊，以及讓客戶接觸到所喜愛業務的普遍方式。 在網站或應用程式中新增對話式使用者介面，可提升其功能的尋找和存取速度。 使用語音服務時，此對話會以合成語音實際回應口語查詢，而呈現出煥然一新的流暢度。

若要在具有語音功能的聊天機器人中新增獨特的風格 (並強化品牌形象)，您可以為其添加自己的語音。 建立自訂語音的程序需要兩個步驟。 首先，您要[錄製](record-custom-voice-samples.md)您想要使用的語音。 然後，[提交這些錄音](how-to-customize-voice-font.md) (以及文字謄寫) 給語音服務的[語音自訂入口網站](https://cris.ai/Home/CustomVoice)，由它完成其餘作業。 建立自訂語音後，便可輕鬆地在應用程式中使用。

## <a name="next-steps"></a>後續步驟

取得語音服務的訂用帳戶金鑰。

> [!div class="nextstepaction"]
> [免費試用語音服務](get-started.md)
