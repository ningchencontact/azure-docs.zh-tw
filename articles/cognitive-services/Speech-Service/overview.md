---
title: 什麼是語音服務 (預覽)？
description: 語音服務 (屬於 Microsoft 認知服務的一部分) 聯合了先前個別提供的數個 Azure 語音服務：Bing 語音 (包含語音辨識和文字轉換語音)、自訂語音和語音翻譯。
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.component: speech-service
ms.topic: overview
ms.date: 05/07/2018
ms.author: v-jerkin
ms.openlocfilehash: 922320bb0b880e933b27025257e6a533fe257680
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2018
ms.locfileid: "43091467"
---
# <a name="what-is-the-speech-service"></a>什麼是語音服務？

語音服務結合了先前透過 [Bing 語音 API](https://docs.microsoft.com/azure/cognitive-services/speech/home)、[Translator Speech](https://docs.microsoft.com/azure/cognitive-services/translator-speech/)、[自訂語音 (Custom Speech)](https://docs.microsoft.com/azure/cognitive-services/custom-speech-service/cognitive-services-custom-speech-home) 和[自訂語音 (Custom Voice)](http://customvoice.ai/) 服務提供的 Azure 語音功能。 現在，只要一個訂用帳戶即可存取前述所有功能。

如同其他 Azure 服務一樣，語音服務也是由 Cortana 和 Microsoft Office 等產品中使用的成熟語音技術所提供。 Azure 雲端產生的結果和可靠性，都值得您的信賴。

> [!NOTE]
> 語音服務目前為公用預覽狀態。 定期回到這裡取得文件更新、新的程式碼範例等等。

## <a name="main-speech-service-functions"></a>主要語音服務功能

語音服務的主要功能是語音轉換文字 (也稱為語音辨識或轉譯)、文字轉換語音 (語音合成)，以及語音翻譯。

|函式|特性|
|-|-|
|[語音轉換文字](speech-to-text.md)| <ul><li>連續的即時語音謄寫成文字。<li>可以分批謄寫錄音中的語音。 <li>提供互動式、對話和聽寫等使用案例的辨識模式。<li>支援的中繼結果、語音結束偵測、自動文字格式，和不雅內容遮罩。 <li>可呼叫 [Language Understanding](https://docs.microsoft.com/azure/cognitive-services/luis/) (LUIS)，從謄寫的語音衍生使用者意圖。\*|
|[文字轉換語音](text-to-speech.md)| <ul><li>將文字轉換成自然發音語音。 <li>為多種支援的語言提供多個性別和/或方言。 <li>支援純文字輸入或語音合成標記語言 (SSML)。 |
|[語音翻譯](speech-translation.md)| <ul><li>近乎即時地翻譯串流音訊<li> 也可以處理錄製的語音<li>提供文字或合成語音形式的結果。 |

\* *意圖辨識需要 LUIS 訂用帳戶。*


## <a name="customizing-speech-features"></a>自訂語音功能

語音服務可讓您使用自己的資料來定型模型，作為語音服務的語音轉換文字和文字轉換語音功能的基礎。 

|功能|模型|目的|
|-|-|-|
|語音轉換文字|[原音模型](how-to-customize-acoustic-models.md)|有助於謄寫特定的說話者和環境，例如汽車或工廠|
||[語言模型](how-to-customize-language-model.md)|有助於謄寫特定領域的詞彙和文法，例如醫療或 IT 專業術語|
||[發音模型](how-to-customize-pronunciation.md)|有助於謄寫縮寫和縮略字，例如 "IOU" 代表 "i oh you" |
|文字轉換語音|[聲音音調](how-to-customize-voice-font.md)|以人類語音的範例將模型定型，給予應用程式它自己的聲音。|

您的自訂模型建立之後，將可用在應用程式的語音轉換文字或文字轉換語音功能所將運用到的任何情境。


## <a name="using-the-speech-service"></a>使用語音服務

為了讓支援語音的應用程式更易於開發，Microsoft 提供了可與新的語音服務搭配使用的[語音 SDK](speech-sdk.md)。 語音 SDK 提供適用於 C#、C++ 和 Java 且具一致性的原生語音轉換文字和語音翻譯 API。 如果您要使用其中一種語言進行開發，語音 SDK 將可為您處理網路詳細資料，而讓開發更為容易。

語音服務也具有適用於任何程式設計語言的 [REST API](rest-apis.md)，可用來提出 HTTP 要求。 但 REST 介面並未提供 SDK 的即時串流功能。

|<br>方法|語音<br>轉換文字|文字轉換<br>語音|語音<br>翻譯|<br>說明|
|-|-|-|-|-|
|[語音 SDK](speech-sdk.md)|是|否|是|適用於 C#、C++ 和 Java 的原生 API，可簡化開發工作。|
|[REST](rest-apis.md)|是|yes|否|以 HTTP 為基礎的簡單 API，可讓您輕鬆地將語音新增至您的應用程式。|

### <a name="websockets"></a>WebSocket

語音服務也有用來串流語音轉換文字和語音翻譯的 Websocket 通訊協定。 語音 SDK 會使用這些通訊協定與語音服務進行通訊。 您應使用語音 SDK，而不是嘗試自行實作與語音服務的 WebSocket 通訊。

不過，如果您已有透過 Websocket 使用 Bing 語音或 Translator Speech 的程式碼，您可以直接將其更新為使用語音服務。 Websocket 通訊協定是相容的，只有端點不同。

### <a name="speech-devices-sdk"></a>語音裝置 SDK

[語音裝置 SDK](speech-devices-sdk.md) 是支援語音的裝置開發人員所適用的整合式硬體和軟體平台。 我們的硬體合作夥伴會提供參考設計和開發單元。 Microsoft 提供可充分運用硬體功能的裝置最佳化 SDK。


## <a name="speech-scenarios"></a>語音案例

語音服務的使用案例包括：

> [!div class="checklist"]
> * 建立語音觸發式應用程式
> * 謄寫話務中心錄音
> * 實作語音聊天機器人

### <a name="voice-user-interface"></a>語音使用者介面

語音輸入可讓應用程式富有彈性、能夠免持，且快速使用。 在支援語音的應用程式中，使用者可直接要求所需的資訊，而不需要加以瀏覽。

如果應用程式的適用對象是一般大眾，您可以使用預設的語音辨識模型。 這些模型可在一般環境下妥善辨識各種不同的說話者。

如果應用程式會用於特定領域 (例如醫藥或 IT)，您可以建立[語言模型](how-to-customize-language-model.md)，讓語音服務學習應用程式所使用的特殊術語。

如果應用程式會用於吵雜環境 (例如工廠)，您可以建立自訂[原音模型](how-to-customize-acoustic-models.md)，讓語音服務有更好的語音/雜訊辨別能力。

要開始使用很簡單，只要下載[語音 SDK](speech-sdk.md) 並遵循相關[快速入門](quickstart-csharp-dotnet-windows.md)文章即可。

### <a name="call-center-transcription"></a>話務中心轉譯

我們通常只會在通話發生問題時，才會回頭聽取話務中心的錄音內容。 有了語音服務，將錄音謄寫為文字便很容易。 謄寫為文字後，便可輕鬆地對文字建立索引以進行[全文檢索搜尋](https://docs.microsoft.com/azure/search/search-what-is-azure-search)，也可套用[文字分析](https://docs.microsoft.com/azure/cognitive-services/Text-Analytics/)來偵測情感、語言和關鍵片語。

如果話務中心錄音涉及專用術語 (例如，產品名稱或 IT 專業術語)，您可以建立[語言模型](how-to-customize-language-model.md)讓語音服務學習這些詞彙。 自訂[原音模型](how-to-customize-acoustic-models.md)可協助語音服務了解較差的電話連線。

如需此案例的詳細資訊，請深入了解語音服務的[批次謄寫](batch-transcription.md)。

### <a name="voice-bots"></a>語音聊天機器人

[聊天機器人](https://dev.botframework.com/)日漸成為讓使用者獲得其所需資訊，以及讓客戶接觸到所喜愛業務的普遍方式。 在網站或應用程式中新增對話式使用者介面，可提升其功能的尋找和存取速度。 使用語音服務時，此對話會以同樣的方式回應口語查詢，而呈現出煥然一新的流暢度。

若要在具有語音功能的聊天機器人中新增獨特的風格 (並強化品牌形象)，您可以為其添加自己的語音。 建立自訂語音的程序需要兩個步驟。 首先，您要[錄製](record-custom-voice-samples.md)您想要使用的語音。 然後，[提交這些錄音](how-to-customize-voice-font.md) (以及文字謄寫) 給語音服務的[語音自訂入口網站](https://cris.ai/Home/CustomVoice)，由它完成其餘作業。 建立自訂語音後，便可輕鬆地在應用程式中使用。

## <a name="next-steps"></a>後續步驟

取得語音服務的訂用帳戶金鑰。

> [!div class="nextstepaction"]
> [免費試用語音服務](get-started.md)
