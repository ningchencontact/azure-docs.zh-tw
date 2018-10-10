---
title: 使用用戶端程式庫來開始使用 Bing 語音辨識 API | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: 使用 Microsoft 認知服務中的 Bing 語音用戶端程式庫，來開發將語音轉換成文字的應用程式。
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.component: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: zhouwang
ROBOTS: NOINDEX
ms.openlocfilehash: e9d1bf1a6a2383a58a890ce9add816f9e9060273
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/24/2018
ms.locfileid: "46948130"
---
# <a name="get-started-with-bing-speech-service-client-libraries"></a>開始使用 Bing 語音服務用戶端程式庫

除了透過 REST API 提出直接的 HTTP 要求外，Bing 語音服務還為開發人員提供了不同語言的語音用戶端程式庫。 語音用戶端程式庫：

- 支援更進階的語音辨識功能，例如即時的中繼結果、長時間音訊串流 (最多 10 分鐘) 和連續辨識。
- 以您偏好的語言提供簡單且適用於慣用語的 API。
- 隱藏低層級的通訊詳細資料。

下列是目前可用的 Bing 語音用戶端程式庫：

- [C# 桌面程式庫](GetStartedCSharpDesktop.md)
- [C# 服務程式庫](GetStartedCSharpServiceLibrary.md)
- [JavaScript 程式庫](GetStartedJSWebsockets.md)
- [適用於 Android 的 JAVA 程式庫](GetStartedJavaAndroid.md)
- [適用於 iOS 的 Objective-C 程式庫](Get-Started-ObjectiveC-iOS.md)

> [!NOTE] 
在 2018 年 5 月，我們也發行了新的[語音服務](../../speech-service/index.yml) (公開預覽狀態)。 我們鼓勵您[免費試用](../../speech-service/get-started.md)。 

## <a name="additional-resources"></a>其他資源

- [範例](../samples.md)頁面會提供使用語音用戶端程式庫的完整範例。
- 如果您需要的用戶端程式庫尚未受到支援，您可以建立自己的 SDK。 在平台上實作[語音 WebSocket 通訊協定](../API-Reference-REST/websocketprotocol.md)並使用您所選擇的語言。

## <a name="license"></a>授權

所有「認知服務 SDK」和範例都是透過 MIT 授權來獲得授權的。 如需詳細資訊，請參閱[授權](https://github.com/Microsoft/Cognitive-Speech-STT-JavaScript/blob/master/LICENSE.md) \(英文\)。

