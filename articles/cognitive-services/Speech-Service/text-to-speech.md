---
title: 關於文字轉換語音 | Microsoft Docs
description: 文字轉換語音功能概觀。
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
manager: noellelacharite
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 05/07/2018
ms.author: v-jerkin
ms.openlocfilehash: 84baf03c83bb63883b80982056cdf6e1e25b3fb7
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/23/2018
ms.locfileid: "35370675"
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

Microsoft **文字轉換語音**服務在 45 個以上的語言和地區設定中提供 75 種以上的語音。 若要使用這些標準「語音字型」，您只需要在呼叫服務的 REST API 時，使用一些其他參數來指定語音名稱。 如需所支援語音的詳細資料，請參閱[支援的語言](supported-languages.md)。 

若希望應用程式辨識獨特的語音，您可以從自己的語音範例建立[自訂語音字型](how-to-customize-voice-font.md)。

## <a name="next-steps"></a>後續步驟

* [取得語音試用版訂用帳戶](https://azure.microsoft.com/try/cognitive-services/)
* [了解如何以 C# 辨識語音](quickstart-csharp-windows.md)
