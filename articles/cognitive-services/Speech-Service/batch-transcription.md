---
title: 如何使用批次轉譯 - 語音服務
titlesuffix: Azure Cognitive Services
description: 如果您想要轉譯儲存體 (例如 Azure Blob) 中數量龐大的音訊，則適用批次轉譯。 透過使用該專屬 REST API，您可以使用共用存取簽章 (SAS) URI 來指向音訊檔案，並以非同步方式接收轉譯。
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 2/20/2019
ms.author: panosper
ms.custom: seodec18
ms.openlocfilehash: 3b403eb80bae01efe730b69b7e6a5ddaea81355a
ms.sourcegitcommit: 6cab3c44aaccbcc86ed5a2011761fa52aa5ee5fa
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/20/2019
ms.locfileid: "56447645"
---
# <a name="why-use-batch-transcription"></a>為何使用 Batch 轉譯？

如果您想要轉譯儲存體 (例如 Azure Blob) 中數量龐大的音訊，則適用批次轉譯。 透過使用該專屬 REST API，您可以使用共用存取簽章 (SAS) URI 來指向音訊檔案，並以非同步方式接收轉譯。

## <a name="prerequisites"></a>必要條件

### <a name="subscription-key"></a>訂用帳戶金鑰

如同語音服務的所有功能，您可以依照我們的[快速入門指南](get-started.md)從 [Azure 入口網站](https://portal.azure.com)建立訂用帳戶金鑰。 如果您打算從我們的基準模型取得轉譯，您只要建立金鑰即可。

>[!NOTE]
> 語音服務的標準訂用帳戶 (S0) 才能使用批次轉譯。 免費訂用帳戶金鑰 (F0) 將無法運作。 如需詳細資訊，請參閱[定價和限制](https://azure.microsoft.com/en-us/pricing/details/cognitive-services/speech-services/)。

### <a name="custom-models"></a>自訂模型

如果您打算自訂原音或語言模型，請依照[自訂原音模型](how-to-customize-acoustic-models.md)和[自訂語言模型](how-to-customize-language-model.md)中的步驟進行。 若要使用批次轉譯中建立的模型，您需要其模型識別碼。 此識別碼不是您在 [端點詳細資料] 檢視上所找到的端點識別碼，這是您選取該模型的詳細資料時可擷取的模型識別碼。

## <a name="the-batch-transcription-api"></a>Batch 轉譯 API

Batch 轉譯 API 提供非同步語音轉換文字轉譯及其他功能。 此 REST API 所公開的方法可以：

1. 建立批次的處理要求
1. 查詢狀態
1. 下載轉譯

> [!NOTE]
> Batch 轉譯 API 很適合話務中心，它們通常會累積數千小時的音訊。 這能輕鬆轉譯大量的音訊錄製。

### <a name="supported-formats"></a>支援的格式

Batch 轉譯 API 支援下列格式：

| 格式 | 轉碼器 | Bitrate | 採樣速率 |
|--------|-------|---------|-------------|
| WAV | PCM | 16 位元 | 8 或 16 kHz、單聲道、立體聲 |
| MP3 | PCM | 16 位元 | 8 或 16 kHz、單聲道、立體聲 |
| OGG | OPUS | 16 位元 | 8 或 16 kHz、單聲道、立體聲 |

針對立體聲音訊資料流，Batch 轉譯 API 會在轉譯期間分離左右聲道。 這會建立兩個 JSON 結果檔案，每個聲道各建立一個。 每個語句的時間戳記可讓開發人員建立排序的最終文字記錄。 此範例要求包含不雅內容篩選、標點符號和字組層級時間戳記的屬性。 

### <a name="configuration"></a>組態

組態參數以 JSON 格式提供：

```json
{
  "recordingsUrl": "<URL to the Azure blob to transcribe>",
  "models": ["<optional acoustic model ID>, <optional language model ID>"],
  "locale": "<local to us, for example en-US>",
  "name": "<user define name of the transcription batch>",
  "description": "<optional description of the transcription>",
  "properties": {
    "ProfanityFilterMode": "Masked",
    "PunctuationMode": "DictatedAndAutomatic",
    "AddWordLevelTimestamps" : "True"
  }
}
```

> [!NOTE]
> Batch 轉譯 API 使用 REST 服務來要求轉譯、其狀態及相關結果。 您可以使用任何語言的 API。 下一節描述 API 的使用方式。

### <a name="configuration-properties"></a>組態屬性

| 參數 | 說明 | 必要/選用 |
|-----------|-------------|---------------------|
| `ProfanityFilterMode` | 指定如何處理辨識結果中的不雅內容。 接受的值為 `none` (會停用不雅內容過濾)、`masked` (為以星號取代不雅內容)、`removed` (會移除結果中的所有不雅內容) 或 `tags` (會新增「不雅內容」標記)。 預設設定為 `masked`。 | 選用 |
| `PunctuationMode` | 指定如何處理辨識結果中的標點符號。 接受的值為`none` (會停用標點符號)、`dictated` (暗示明確的標點符號)、`automatic` (會讓解碼器處理標點符號) 或 `dictatedandautomatic` (暗示口述的標點符號或自動)。 | 選用 |
 | `AddWordLevelTimestamps` | 指定是否將字組層級時間戳記新增至輸出。 接受的值為`true` 會啟用字組層級時間戳記，而 `false` (預設值) 會停用。 | 選用 |

## <a name="sample-code"></a>範例程式碼

`samples/batch` 子目錄內部的 [GitHub 範例存放庫](https://aka.ms/csspeech/samples)提供完整的範例。

您自訂的範例程式碼要有訂用帳戶資訊、服務區域、指向音訊檔的 SAS URI 以轉譯，以及模型識別碼，以防您想要使用自訂原音或語言模型。 

[!code-csharp[Configuration variables for batch transcription](~/samples-cognitive-services-speech-sdk/samples/batch/csharp/program.cs#batchdefinition)]

範例程式碼將設定用戶端並提交轉譯要求。 接著會輪詢狀態資訊並列印轉譯進度的詳細資料。

[!code-csharp[Code to check batch transcription status](~/samples-cognitive-services-speech-sdk/samples/batch/csharp/program.cs#batchstatus)]

如需上述呼叫的完整詳細資訊，請參閱我們的 [Swagger 文件](https://westus.cris.ai/swagger/ui/index)。 如需此處所顯示的完整範例，請前往 `samples/batch` 子目錄中的 [GitHub](https://aka.ms/csspeech/samples)。

請留意張貼音訊和接收轉譯狀態的非同步設定。 您建立的用戶端是 .NET HTTP 用戶端。 `PostTranscriptions` 方法可傳送音訊檔案詳細資料，而 `GetTranscriptions` 方法可接收結果。 `PostTranscriptions` 會傳回控制代碼，然後 `GetTranscriptions` 使用它來取得轉譯狀態。

目前的範例程式碼沒有指定自訂模型。 該服務會使用基準模型來轉譯一或多個檔案。 若要指定模型，您可以傳遞相同的方法，如同原音和語言模型的模型識別碼。

> [!NOTE]
> 針對基準轉譯，您不需要宣告基準模型的識別碼。 如果您只有指定語言模型識別碼 (且沒有任何的原音模型識別碼)，會自動選取相符的原音模型。 如果您只有指定原音模型識別碼，則會自動選取相符的語言模型。

### <a name="supported-storage"></a>支援的儲存體

目前僅支援 Azure Blob 儲存體。

## <a name="download-the-sample"></a>下載範例

您可以在 [GitHub 範例存放庫](https://aka.ms/csspeech/samples)中的 `samples/batch` 目錄找到範例。

> [!NOTE]
> 批次轉譯作業會盡可能排程，作業何時會變更為執行狀態則無法預估。 只要處於執行中狀態，實際轉譯的即時處理速度會比音訊快。

## <a name="next-steps"></a>後續步驟

* [試用認知服務](https://azure.microsoft.com/try/cognitive-services/)
