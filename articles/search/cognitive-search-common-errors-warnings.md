---
title: 常見錯誤和警告-Azure 搜尋服務
description: 本文提供在 Azure 搜尋服務的 AI 擴充期間可能會遇到的常見錯誤和警告的資訊和解決方案。
services: search
manager: heidist
author: amotley
ms.service: search
ms.workload: search
ms.topic: conceptual
ms.date: 09/18/2019
ms.author: abmotley
ms.openlocfilehash: 18befbfb924129518ac32a7fdddaa9ee573840b0
ms.sourcegitcommit: f2d9d5133ec616857fb5adfb223df01ff0c96d0a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/03/2019
ms.locfileid: "71936478"
---
# <a name="common-errors-and-warnings-of-the-ai-enrichment-pipeline-in-azure-search"></a>Azure 搜尋服務中 AI 擴充管線的常見錯誤和警告

本文提供在 Azure 搜尋服務的 AI 擴充期間可能會遇到的常見錯誤和警告的資訊和解決方案。

## <a name="errors"></a>錯誤
當錯誤計數超過[' maxfaileditems '](cognitive-search-concept-troubleshooting.md#tip-3-see-what-works-even-if-there-are-some-failures)時，就會停止編制索引。 下列各節可協助您解決錯誤，並允許繼續編制索引。

### <a name="could-not-read-document"></a>無法讀取檔
索引子無法從資料來源讀取檔。 發生這種情況的原因可能是：

| `Reason` | 範例 | Action |
| --- | --- | --- |
| 跨不同檔的欄位類型不一致 | 值的類型與資料行類型不相符。 無法在 [作者] 資料行中儲存 `'{47.6,-122.1}'`。  預期的類型為 JArray。 | 請確定每個欄位的類型在不同的檔中都相同。 例如，如果第一個檔 `'startTime'` 欄位是日期時間，而第二個檔是字串，則會遇到此錯誤。 |
| 來自資料來源之基礎服務的錯誤 | （從 Cosmos DB） `{"Errors":["Request rate is large"]}` | 檢查您的儲存體實例，以確保其狀況良好。 您可能需要調整您的縮放/分割。 |
| 暫時性問題 | 從伺服器接收結果時發生傳輸層級錯誤。 (提供者：TCP 提供者，錯誤：0-遠端主機已強制關閉現有的連接 | 偶爾會發生非預期的連線問題。 請稍後再試著透過索引子執行檔。 |

### <a name="could-not-extract-document-content"></a>無法解壓縮檔內容
具有 Blob 資料來源的索引子無法從檔解壓縮內容（例如，PDF 檔案）。 發生這種情況的原因可能是：

| `Reason` | 範例 | Action |
| --- | --- | --- |
| blob 超過大小限制 | 檔為 `'150441598'` 個位元組，超過目前服務層級的檔解壓縮的大小上限 `'134217728'` 個位元組。 | [blob 索引錯誤](search-howto-indexing-azure-blob-storage.md#dealing-with-errors) |
| blob 具有不支援的內容類型 | 檔具有不支援的內容類型 `'image/png'` | [blob 索引錯誤](search-howto-indexing-azure-blob-storage.md#dealing-with-errors) |
| blob 已加密 | 無法處理檔-它可能已加密或密碼保護。 | [blob 設定](search-howto-indexing-azure-blob-storage.md#controlling-which-parts-of-the-blob-are-indexed) |
| 暫時性問題 | 處理 blob 時發生錯誤：要求已中止：已取消要求。 | 偶爾會發生非預期的連線問題。 請稍後再試著透過索引子執行檔。 |

### <a name="could-not-parse-document"></a>無法剖析檔
索引子從資料來源讀取檔，但將檔內容轉換成指定的欄位對應架構時發生問題。 發生這種情況的原因可能是：

| `Reason` | 範例 | Action |
| --- | --- | --- |
| 缺少檔索引鍵 | 檔索引鍵不能遺失或空白 | 確保所有檔都具有有效的檔索引鍵 |
| 檔索引鍵無效 | 檔索引鍵的長度不能超過1024個字元 | 修改檔索引鍵以符合驗證需求。 |
| 無法將欄位對應套用至欄位 | 無法將對應函數 `'functionName'` 套用至 field `'fieldName'`。 陣列不可以是 null。 參數名稱：位元組 | 再次檢查索引子上定義的[欄位](search-indexer-field-mappings.md)對應，並與失敗檔之指定欄位的資料進行比較。 可能需要修改欄位對應或檔資料。 |
| 無法讀取域值 | 無法讀取位於索引 `'fieldIndex'` 的資料行 `'fieldName'` 的值。 從伺服器接收結果時發生傳輸層級錯誤。 (提供者：TCP 提供者，錯誤：0-遠端主機已強制關閉現有的連接）。 | 這些錯誤通常是因為資料來源的基礎服務發生非預期的連接問題。 請稍後再試著透過索引子執行檔。 |

### <a name="skill-input-languagecode-has-the-following-language-codes-xyz-at-least-one-of-which-is-invalid"></a>技能輸入 ' languageCode ' 具有下列語言代碼 ' X，Y，Z '，其中至少有一個無效。
不支援傳遞至選擇性 `languageCode` 輸入下游技能的一個或多個值。 如果您將[LanguageDetectionSkill](cognitive-search-skill-language-detection.md)的輸出傳遞給後續技能，而輸出所包含的語言比這些下游技能所支援的更多，就會發生這種情況。

如果您知道您的資料集全都使用一種語言，則應該移除[LanguageDetectionSkill](cognitive-search-skill-language-detection.md)和 @no__t 1 技能輸入，並改為使用該技能的 `defaultLanguageCode` 技能參數，並假設該技術支援該語言。

如果您知道您的資料集包含多個語言，因此您需要[LanguageDetectionSkill](cognitive-search-skill-language-detection.md)和 @no__t 1 輸入，請考慮新增[ConditionalSkill](cognitive-search-skill-conditional.md) ，以使用不受支援的語言篩選掉文字，然後才傳入下游技能的文字。  以下是此 EntityRecognitionSkill 可能顯示的範例：

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ConditionalSkill",
    "context": "/document",
    "inputs": [
        { "name": "condition", "source": "= $(/document/language) == 'de' || $(/document/language) == 'en' || $(/document/language) == 'es' || $(/document/language) == 'fr' || $(/document/language) == 'it'" },
        { "name": "whenTrue", "source": "/document/content" },
        { "name": "whenFalse", "source": "= null" }
    ],
    "outputs": [ { "name": "output", "targetName": "supportedByEntityRecognitionSkill" } ]
}
```

以下是每個可能產生此錯誤訊息之技能的目前支援語言參考：
* [文字分析支援的語言](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages)（適用[于 KeyPhraseExtractionSkill](cognitive-search-skill-keyphrases.md)、 [EntityRecognitionSkill](cognitive-search-skill-entity-recognition.md)和[SentimentSkill](cognitive-search-skill-sentiment.md)）
* [翻譯工具支援的語言](https://docs.microsoft.com/azure/cognitive-services/translator/language-support)（適用于[文字 TranslationSkill](cognitive-search-skill-text-translation.md)）
* [文字 SplitSkill](cognitive-search-skill-textsplit.md)支援的語言： `da, de, en, es, fi, fr, it, ko, pt`

### <a name="skill-did-not-execute-within-the-time-limit"></a>技能未在時間限制內執行
在兩種情況下，您可能會遇到這個錯誤訊息，每個都應該以不同的方式處理。 請依照下列指示，視您為此錯誤傳回的技能而定。

#### <a name="built-in-cognitive-service-skills"></a>內建認知服務技能
認知服務 API 端點支援許多內建的認知技能，例如語言偵測、實體辨識或 OCR。 有時候這些端點有暫時性問題，而要求將會超時。針對暫時性問題，除了等待並再試一次以外，沒有任何補救措施。 作為緩和措施，請考慮將您的索引子設定為依[排程執行](search-howto-schedule-indexers.md)。 排定的編制索引會從停止的地方繼續進行。 假設暫時性問題已解決，則索引和認知技能的處理應該能夠在下一次排程執行時繼續進行。

#### <a name="custom-skills"></a>自訂技能
如果您所建立的自訂技能遇到逾時錯誤，您可以嘗試幾件事。 首先，請檢查您的自訂技能，並確保它不會停滯在無限迴圈中，而且會以一致的方式傳回結果。 一旦您確認是這種情況，請判斷技能的執行時間。 如果您未在自訂技能定義上明確設定 `timeout` 值，則預設 `timeout` 為30秒。 如果30秒不夠長，無法讓您的技能執行，您可以在自訂技能定義上指定較高的 `timeout` 值。 以下是自訂技能定義的範例，其中的 timeout 設定為90秒：

```json
  {
        "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
        "uri": "<your custom skill uri>",
        "batchSize": 1,
        "timeout": "PT90S",
        "context": "/document",
        "inputs": [
          {
            "name": "input",
            "source": "/document/content"
          }
        ],
        "outputs": [
          {
            "name": "output",
            "targetName": "output"
          }
        ]
      }
```

您可以為 `timeout` 參數設定的最大值為230秒。  如果您的自訂技能無法在230秒內一致地執行，您可以考慮減少自訂技能的 @no__t 0，使其在單一執行中要處理的檔數較少。  如果您已將 `batchSize` 設定為1，就必須重寫技能，以便在230秒內執行，或將其分割成多個自訂技能，讓任何單一自訂技能的執行時間最多可達230秒。 如需詳細資訊，請參閱[自訂技能檔](cognitive-search-custom-skill-web-api.md)。

##  <a name="warnings"></a>警告
警告不會停止編制索引，但會指出可能導致未預期結果的狀況。 無論您採取動作與否，都取決於資料和您的案例。

### <a name="skill-input-was-truncated"></a>已截斷技能輸入
認知技能對於可以一次分析的文字長度有限制。 如果這些技能的文字輸入超過該限制，我們會截斷文字以符合限制，然後在該截斷的文字上執行擴充。 這表示技能會執行，但不是您所有的資料。

在下面的範例 LanguageDetectionSkill 中，如果超出字元限制，`'text'` 輸入欄位可能會觸發此警告。 您可以在[技能檔](cognitive-search-predefined-skills.md)中找到技能的輸入限制。

```json
 {
    "@odata.type": "#Microsoft.Skills.Text.LanguageDetectionSkill",
    "inputs": [
      {
        "name": "text",
        "source": "/document/text"
      }
    ],
    "outputs": [...]
  }
```

如果您想要確保所有文字都經過分析，請考慮使用[分割技能](cognitive-search-skill-textsplit.md)。