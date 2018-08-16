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
ms.openlocfilehash: 0cd6d984ac9329112aa388e8d8ee808d4c3e6227
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/02/2018
ms.locfileid: "39445629"
---
# <a name="what-is-the-speech-service-preview"></a>什麼是語音服務 (預覽)？

語音服務是由其他 Microsoft 產品 (包括 Cortana 和 Microsoft Office) 中使用的技術所提供。  這項服務與認知服務一樣適用於任何客戶。 

> [!NOTE]
> 語音服務目前為公用預覽狀態。 定期返回這裡，以取得文件更新、額外的程式碼範例等等。

利用一個訂用帳戶，我們的語音服務可讓開發人員輕鬆地為其應用程式提供強大的語音功能。 您的應用程式現在可以提供語音命令、轉譯、聽寫、語音合成和翻譯功能。

## <a name="speech-service-features"></a>語音服務功能

|函式|說明|
|-|-|
|[語音轉文字](speech-to-text.md)| 將音訊串流轉譯為文字，以便應用程式接受作為輸入。 也可與 [Language Understanding 服務](https://docs.microsoft.com/azure/cognitive-services/luis/) (LUIS) 整合，從語句衍生使用者意圖。|
|[文字轉換語音](text-to-speech.md)| 將純文字轉換成自然發音語音，並以音訊檔案傳遞到您的應用程式。 針對多種支援的語言提供多種聲音 (因性別或腔調而異)。 |
|[語音翻譯](speech-translation.md)| 可用來翻譯接近即時的串流音訊，或處理錄製的語音。 |
|[語音裝置 SDK](speech-devices-sdk.md)| 在推出統一語音服務後，Microsoft 和合作夥伴會提供整合式硬體/軟體平台，此平台已針對開發語音裝置進行最佳化 |

## <a name="using-the-speech-service"></a>使用語音服務

語音服務可以用兩種方式提供。 [SDK](speech-sdk.md) 會擷取網路通訊協定的詳細資料。 [REST API](rest-apis.md) 可搭配任何程式設計語言運作，但不會提供 SDK 所提供的所有函式。

|<br>方法|語音<br>轉換文字|文字轉換<br>語音|語音<br>翻譯|<br>說明|
|-|-|-|-|-|
|[SDK](speech-sdk.md)|是|否|是|可簡化開發作業且適用於特定程式設計語言的程式庫。|
|[REST](rest-apis.md)|是|yes|否|以 HTTP 為基礎的簡單 API，可讓您輕鬆地將語音新增至您的應用程式。|

## <a name="next-steps"></a>後續步驟

取得語音服務的免費試用訂用帳戶金鑰。

> [!div class="nextstepaction"]
> [免費試用語音服務](get-started.md)
