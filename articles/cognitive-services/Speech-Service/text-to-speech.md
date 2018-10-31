---
title: 關於文字轉換語音 - 語音服務
titleSuffix: Azure Cognitive Services
description: 「文字轉換語音 API」在超過 45 種的語言和地區設定中提供超過 75 種的語音。 若要使用標準音型，您只需在呼叫「語音服務」時，指定語音名稱搭配一些其他參數即可。
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: conceptual
ms.date: 10/16/2018
ms.author: erhopf
ms.openlocfilehash: 7f01fe5c71cdd6f4c70527fcf2553374aae9a5d8
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/19/2018
ms.locfileid: "49469912"
---
# <a name="about-the-text-to-speech-api"></a>關於文字轉換語音 API

語音服務的**文字轉換語音** (TTS) API 會將輸入文字轉換為自然發音語音 (也稱為「語音合成」)。

若要產生語音，您的應用程式會將 HTTP POST 要求傳送給語音服務。 文字會合成為人聲語音，並傳回為音訊檔案。 支援各種語音和語言。

將採用語音合成的情節包括：

* *改善存取範圍：***文字轉換語音**技術可讓內容擁有者和發行者回應人員與其內容互動的不同方式。 視力受損或閱讀困難的人士喜歡能夠透過聽覺使用內容。 語音輸出也可讓您在行動裝置上更輕鬆地享受文字內容 (例如報紙或部落格)，同時通訊或執行。

* *在多工情節中回應：***文字轉換語音**可讓人員在開車或方便讀取環境外部時快速且輕鬆地吸收重要資訊。 在此區域，導覽是常見應用程式。

* *強化多個模式的學習：* 不同的人會以不同的方式學習得更好。 線上學習專家已示範，同時提供語音和文字有助於更輕鬆地了解和保留資訊。

* *提供直覺式機器人或助理：* 交談能力可能是智慧型聊天機器人或虛擬助理的不可或缺部分。 越來越多的公司正在開發聊天機器人，為客戶提供更吸引人的客戶服務體驗。 語音允許機器人透過聽覺接收回應 (例如，透過電話)，因而新增另一個範圍。

## <a name="voice-support"></a>語音支援

Microsoft **文字轉換語音**服務在 45 個以上的語言和地區設定中提供 75 種以上的語音。 若要使用這些標準「語音字型」，您只需要在呼叫服務的 REST API 時，使用一些其他參數來指定語音名稱。 如需所支援語音的詳細資料，請參閱[支援的語言](language-support.md#text-to-speech)。

若希望應用程式辨識獨特的語音，您可以從自己的語音範例建立[自訂語音字型](how-to-customize-voice-font.md)。

## <a name="api-capabilities"></a>API 功能

**文字轉換語音** API 的許多功能 (特別是關於自訂的部分) 都可透過 REST 取得。 下表摘要說明存取 API 的每個方法的功能。 如需完整的功能清單和 API 詳細資料，請參閱 [Swagger 參考](https://westus.cris.ai/swagger/ui/index) \(英文\)。

| 使用案例 | REST | SDK |
|-----|-----|-----|----|
| 上傳適用於語音調適的資料集 | 是 | 否 |
| 建立及管理音型模型 | 是 | 否 |
| 建立及管理音型部署 | 是 | 否 |
| 建立及管理音型測試| 是 | 否 |
| 管理訂用帳戶 | 是 | 否 |

> [!NOTE]
> API 會實作節流以將 API 要求限制為每 5 秒 25 個。 訊息標頭會通知限制。

## <a name="next-steps"></a>後續步驟

* [試用認知服務](https://azure.microsoft.com/try/cognitive-services/)
* [了解如何透過 REST API 來合成語音](how-to-text-to-speech.md)
