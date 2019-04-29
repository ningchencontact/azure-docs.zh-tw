---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
origin.date: 09/24/2018
ms.date: 04/01/2019
ms.author: v-biyu
ms.openlocfilehash: 31482cc7850c574b952c454021af729da324ba15
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60769935"
---
<!-- N.B. no header, no intents here, language-agnostic -->

認知服務[語音 SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) 提供最簡單的方式，在您的應用程式中使用完整功能的 [語音轉換文字]。

1. 建立語音設定，並提供語音服務訂用帳戶金鑰或授權權杖和[區域](~/articles/cognitive-services/speech-service/regions.md)作為參數。 請視需要變更設定。 例如可提供自訂的端點，以指定非標準服務端點，或是選擇語音的輸入語言或輸出格式。

1. 以語音設定建立語音辨識器。 如果您想要用預設麥克風以外的來源 (例如，音訊資料流或音訊檔案) 進行辨識，請提供音訊設定。

1. 繫結事件以便執行非同步作業 (如有需要)。 當辨識器具有過渡期和最終結果時，它會接著呼叫事件處理常式。 否則，您的應用程式只會收到最終轉譯結果。

1. 開始辨識。 針對一次性辨識 (例如命令或查詢辨識)，請使用 `RecognizeOnceAsync()` (或對等語言) 方法。 此方法會傳回第一個辨識出來的語句。 針對長時間執行的辨識 (例如轉譯)，請使用 `StartContinuousRecognitionAsync()` (或對等語言) 方法。 請繫結事件，以產生非同步辨識結果。

請參閱使用語音 SDK 的語音辨識案例所適用的下列程式碼片段。

[!INCLUDE [Get a subscription key](cognitive-services-speech-service-get-subscription-key.md)]
