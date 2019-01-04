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
ms.date: 12/13/2018
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: b06864e08f6edf52e4c96c33c88bba9f8ef4e859
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/13/2018
ms.locfileid: "53343202"
---
# <a name="about-the-text-to-speech-api"></a>關於文字轉換語音 API

**文字轉換語音** (TTS) API 會將輸入文字轉換為自然發音語音 (也稱為「語音合成」)。

若要產生語音，您的應用程式會將 HTTP POST 要求傳送給文字轉換語音 API。 文字會合成為人聲語音，並傳回為音訊檔案。 支援各種語音和語言。

將採用語音合成的情節包括：

* *改善存取範圍：***文字轉換語音**技術可讓內容擁有者和發行者針對人員與內容互動的各種方式進行回應。 視力受損或閱讀困難的人士喜歡能夠透過聽覺使用內容。 語音輸出也可讓您在行動裝置上更輕鬆地享受文字內容 (例如報紙或部落格)，同時通訊或執行。

* *在多工情節中回應：***文字轉換語音**可讓人員在開車或方便讀取環境外部時快速且輕鬆地吸收重要資訊。 在此區域，導覽是常見應用程式。

* 以多個模式增強學習：人們學習的最佳方式不盡相同。 線上學習專家已示範，同時提供語音和文字有助於更輕鬆地了解和保留資訊。

* 提供直覺式聊天機器人或助理：交談能力可能是智慧型聊天機器人或虛擬助理的不可或缺的一部分。 越來越多的公司正在開發聊天機器人，為客戶提供更吸引人的客戶服務體驗。 語音允許機器人透過聽覺接收回應 (例如，透過電話)，因而新增另一個範圍。

## <a name="voice-support"></a>語音支援

Microsoft **文字轉換語音**服務在 45 個以上的語言和地區設定中提供 75 種以上的語音。 若要使用這些標準「語音字型」，您只需要在呼叫服務的 REST API 時，使用一些其他參數來指定語音名稱。 如需所支援語言、地區設定和語音的詳細資訊，請參閱[支援的語言](language-support.md#text-to-speech)。

### <a name="neural-voices"></a>神經語音

神經文字轉換語音可用來讓與聊天機器人及虛擬小幫手的互動變得更加自然有趣；將數位文字 (例如電子書) 轉換成有聲書；以及增強車上導航系統。 具有類似人類的自然韻律和清楚的文字清晰度，神經 TTS 大幅降低您與 AI 系統互動時的聆聽疲勞。 如需神經語音的詳細資訊，請參閱[支援的語言](language-support.md#text-to-speech)。

### <a name="custom-voices"></a>自訂語音

文字轉換語音的語音自訂功能可讓您為品牌建立可辨識且獨一無二的語音，也稱為「音型」。 若要建立聲音音調，請進行錄音室錄音，並上傳相關聯的腳本作為訓練資料。 此服務會接著建立專為您的錄音調整的獨特語音模型。 您可以使用此音型來合成語音。 如需詳細資訊，請參閱[自訂音型](how-to-customize-voice-font.md)。

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

* [取得免費語音服務訂用帳戶](https://azure.microsoft.com/try/cognitive-services/)
* [快速入門：將文字轉換成語音，Python](quickstart-python-text-to-speech.md)
* [快速入門：將文字轉換成語音，.NET Core](quickstart-dotnet-text-to-speech.md)
* [REST API 參考資料](rest-apis.md)
