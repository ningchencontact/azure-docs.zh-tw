---
title: 使用用戶端程式庫來開始使用 Microsoft 語音辨識 API | Microsoft Docs
description: 使用 Microsoft 認知服務中的 Microsoft 語音服務用戶端程式庫，來開發將語音轉換成文字的應用程式。
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.component: bing-speech
ms.topic: article
ms.date: 09/15/2017
ms.author: zhouwang
ms.openlocfilehash: 5abe5bc48c2bd73d0facf33e41a8076df2972153
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/23/2018
ms.locfileid: "35368374"
---
# <a name="get-started-with-speech-service-client-libraries"></a>開始使用語音服務用戶端程式庫

除了透過 REST API 提出直接的 HTTP 要求外，語音服務還為開發人員提供了不同語言的語音用戶端程式庫。 語音用戶端程式庫：

- 支援更進階的語音辨識功能，例如即時的中繼結果、長時間音訊串流 (最多 10 分鐘) 和連續辨識。
- 以您偏好的語言提供簡單且適用於慣用語的 API。
- 隱藏低層級的通訊詳細資料。

下列是目前可用的語音用戶端程式庫：

- [C# 桌面程式庫](GetStartedCSharpDesktop.md)
- [C# 服務程式庫](GetStartedCSharpServiceLibrary.md)
- [JavaScript 程式庫](GetStartedJSWebsockets.md)
- [適用於 Android 的 JAVA 程式庫](GetStartedJavaAndroid.md)
- [適用於 iOS 的 Objective-C 程式庫](Get-Started-ObjectiveC-iOS.md)

## <a name="additional-resources"></a>其他資源

- [範例](../samples.md)頁面會提供使用語音用戶端程式庫的完整範例。
- 如果您需要的用戶端程式庫尚未受到支援，您可以建立自己的 SDK。 在平台上實作[語音 WebSocket 通訊協定](../API-Reference-REST/websocketprotocol.md)並使用您所選擇的語言。

## <a name="license"></a>授權

所有「認知服務 SDK」和範例都是透過 MIT 授權來獲得授權的。 如需詳細資訊，請參閱[授權](https://github.com/Microsoft/Cognitive-Speech-STT-JavaScript/blob/master/LICENSE.md) \(英文\)。
