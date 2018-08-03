---
title: 什麼是語音服務 (預覽)？ | Microsoft Docs
description: 語音服務 (屬於 Microsoft 認知服務的一部分) 聯合了先前個別提供的數個 Azure 語音服務：Bing 語音 (包含語音辨識和文字轉換語音)、自訂語音和語音翻譯。
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
manager: noellelacharite
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 05/07/2018
ms.author: v-jerkin
ms.openlocfilehash: adfc854fc24b9e285c405f3038a21ec84cd2f4c2
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/12/2018
ms.locfileid: "38989330"
---
# <a name="what-is-the-speech-service-preview"></a>什麼是語音服務 (預覽)？

語音服務 (屬於 Microsoft 認知服務的一部分) 聯合了先前個別提供的數個 Azure 語音服務：Bing 語音 (包含語音辨識和文字轉換語音)、自訂語音和語音翻譯。 如同其先驅服務一樣，語音服務是由其他 Microsoft 產品 (包括 Cortana 和 Microsoft Office) 中使用的技術所提供。

> [!NOTE]
> 語音服務目前為公用預覽狀態。 定期返回這裡，以取得文件更新、額外的程式碼範例等等。

利用一個訂用帳戶，統一的語音服務可讓開發人員輕鬆地為其應用程式提供強大的語音功能。 您的應用程式現在可以提供語音命令、轉譯、聽寫、語音合成和翻譯功能。

|函式|說明|
|-|-|
|語音轉換文字|將連續的人類語音轉換成可作為您的應用程式輸入的文字。 可以與 [Language Understanding 服務](https://docs.microsoft.com/azure/cognitive-services/luis/) (LUIS) 整合，從語句衍生使用者意圖。|
|文字轉語音|將文字轉換成自然聲音合成語音的音訊檔案。|
|語音&nbsp;翻譯|利用文字或語音輸出，將語音翻譯成其他語言。|

## <a name="using-the-speech-service"></a>使用語音服務

語音服務可以用兩種方式提供。 [SDK](speech-sdk.md) 會擷取網路通訊協定的詳細資料。 [REST API](rest-apis.md) 可搭配任何程式設計語言運作，但不會提供 SDK 所提供的所有函式。

|<br>方法|語音<br>轉換文字|文字轉換<br>語音|語音<br>翻譯|<br>說明|
|-|-|-|-|-|
|[SDK](speech-sdk.md)|是|否|是|可簡化開發作業且適用於特定程式設計語言的程式庫。|
|[REST](rest-apis.md)|是|yes|否|以 HTTP 為基礎的簡單 API，可讓您輕鬆地將語音新增至您的應用程式。|

## <a name="speech-to-text"></a>語音轉換文字

[語音轉換文字](speech-to-text.md) (STT) (或語音辨識) API 可將音訊串流轉譯為文字，以便應用程式接受作為輸入。 例如，您的應用程式可以將文字輸入文件，或作為命令執行它。

語音轉換文字已分別針對互動式、對話和聽寫案例進行最佳化。 以下是語音轉換文字 API 常見的使用案例。 

* 辨識簡短的語句 (如命令)，而無暫時結果
* 轉譯先前記錄的長篇談話，如語音郵件訊息
* 即時轉譯串流語音，將部分結果用於聽寫
* 根據口語的自然語言要求，判斷使用者想要做什麼

語音轉換文字 API 支援互動式語音轉譯，以及即時的連續辨識和過渡期結果。 它也支援語音結束偵測、選用自動大小寫和標點符號、粗話遮罩和文字正規化。

您可以自訂「語音轉換文字」原音和語言模型，以便容納特殊詞彙、吵雜環境及不同的說話方式。

## <a name="text-to-speech"></a>文字轉語音

透過[文字轉換語音](text-to-speech.md) (TTS) 或語音合成，API 可將純文字轉換成自然發音語音，並以音訊檔案傳遞到您的應用程式。 針對多種支援的語言提供多種聲音 (因性別或腔調而異)。

此 API 支援[語音合成標記語言 (SSML)](speech-synthesis-markup.md) 標記，因此您可以為麻煩的單字指定確切的語音發音。 SSML 也可以在文字中表示語音特性 (包含強調、速率、音量、性別及音高)。

以下是文字轉換語音 API 的常見使用案例。

* 為視覺障礙使用者以語音輸出代替畫面輸出
* 車內應用程式的語音提示，例如導航
* 對話使用者介面搭配語音轉換文字 API

如果希望應用程式能夠辨識不支援的方言或獨特的語音，則文字轉換語音 API 可支援[自訂語音模型](how-to-customize-voice-font.md)。

## <a name="speech-translation"></a>語音翻譯

[語音翻譯](speech-translation.md) API 可用來翻譯接近即時的串流音訊，或處理錄製的語音。 在串流翻譯中，服務會傳回可向使用者顯示的過渡期結果，以指出翻譯進度。 結果可能會以文字或語音傳回。

語音翻譯的使用案例包括下列各項。

* 實作出差者的「對話」翻譯行動應用程式或裝置 
* 提供自動翻譯以便製作音訊和視訊錄製品的字幕

## <a name="speech-devices-sdk"></a>語音裝置 SDK

在推出統一語音服務後，Microsoft 和合作夥伴會提供整合式硬體/軟體平台，此平台已針對開發語音裝置進行最佳化：[語音裝置 SDK](speech-devices-sdk.md)。 此 SDK 適用於開發所有類型應用程式的智慧型語音裝置。

語音裝置 SDK 可讓您以自訂的喚醒字打造自己的周邊裝置，用以觸發您的品牌獨有的音訊擷取。 它也提供多聲道來源的上層音訊處理，以進行更精確的語音辨識，包括噪音抑制、遠場語音和波束成形。

SDK 是以使用連接埠 443 的 Web 通訊端為基礎。

## <a name="next-steps"></a>後續步驟

取得語音服務的免費試用訂用帳戶金鑰。

> [!div class="nextstepaction"]
> [免費試用語音服務](get-started.md)
