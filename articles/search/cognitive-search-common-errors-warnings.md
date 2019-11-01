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
ms.openlocfilehash: 6455ac9dbe0933f6d46d1137e0a19dcc388d8c80
ms.sourcegitcommit: 3486e2d4eb02d06475f26fbdc321e8f5090a7fac
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/31/2019
ms.locfileid: "73243048"
---
# <a name="common-errors-and-warnings-of-the-ai-enrichment-pipeline-in-azure-search"></a>Azure 搜尋服務中 AI 擴充管線的常見錯誤和警告

本文提供在 Azure 搜尋服務的 AI 擴充期間可能會遇到的常見錯誤和警告的資訊和解決方案。

## <a name="errors"></a>Errors
當錯誤計數超過[' maxFailedItems '](cognitive-search-concept-troubleshooting.md#tip-3-see-what-works-even-if-there-are-some-failures)時，就會停止編制索引。 

如果您想要讓索引子忽略這些錯誤（並略過「失敗的檔」），請考慮更新 `maxFailedItems` 和 `maxFailedItemsPerBatch`，如[這裡](https://docs.microsoft.com/rest/api/searchservice/create-indexer#general-parameters-for-all-indexers)所述。

> [!NOTE]
> 每個失敗的檔及其檔索引鍵（如果有的話）會顯示為索引子執行狀態中的錯誤。 如果您已將索引子設定為容許失敗，您可以利用[index api](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents)在稍後手動上傳檔。

下列各節可協助您解決錯誤，並允許繼續編制索引。

### <a name="could-not-read-document"></a>無法讀取檔
索引子無法從資料來源讀取檔。 發生這種情況的原因可能是：

| 原因 | 範例 | 行動 |
| --- | --- | --- |
| 跨不同檔的欄位類型不一致 | 值的類型與資料行類型不相符。 無法將 `'{47.6,-122.1}'` 儲存在 [作者] 資料行中。  預期的類型為 JArray。 | 請確定每個欄位的類型在不同的檔中都相同。 例如，如果第一個檔 `'startTime'` 欄位是日期時間，而第二個檔是字串，則會遇到此錯誤。 |
| 來自資料來源之基礎服務的錯誤 | （從 Cosmos DB） `{"Errors":["Request rate is large"]}` | 檢查您的儲存體實例，以確保其狀況良好。 您可能需要調整您的縮放/分割。 |
| 暫時性問題 | 從伺服器接收結果時發生傳輸層級錯誤。 （提供者： TCP 提供者，錯誤： 0-遠端主機已強制關閉現有的連接 | 偶爾會發生非預期的連線問題。 請稍後再試著透過索引子執行檔。 |

### <a name="could-not-extract-document-content"></a>無法解壓縮檔內容
具有 Blob 資料來源的索引子無法從檔解壓縮內容（例如，PDF 檔案）。 發生這種情況的原因可能是：

| 原因 | 範例 | 行動 |
| --- | --- | --- |
| blob 超過大小限制 | 檔是 `'150441598'` 個位元組，超過您目前服務層級的檔解壓縮 `'134217728'` 位元組大小上限。 | [blob 索引錯誤](search-howto-indexing-azure-blob-storage.md#dealing-with-errors) |
| blob 具有不支援的內容類型 | 檔具有不支援的內容類型 `'image/png'` | [blob 索引錯誤](search-howto-indexing-azure-blob-storage.md#dealing-with-errors) |
| blob 已加密 | 無法處理檔-它可能已加密或密碼保護。 | [blob 設定](search-howto-indexing-azure-blob-storage.md#controlling-which-parts-of-the-blob-are-indexed) |
| 暫時性問題 | 處理 blob 時發生錯誤：要求已中止：已取消要求。 | 偶爾會發生非預期的連線問題。 請稍後再試著透過索引子執行檔。 |

### <a name="could-not-parse-document"></a>無法剖析檔
索引子從資料來源讀取檔，但將檔內容轉換成指定的欄位對應架構時發生問題。 發生這種情況的原因可能是：

| 原因 | 範例 | 行動 |
| --- | --- | --- |
| 缺少檔索引鍵 | 檔索引鍵不能遺失或空白 | 確保所有檔都具有有效的檔索引鍵 |
| 檔索引鍵無效 | 檔索引鍵的長度不能超過1024個字元 | 修改檔索引鍵以符合驗證需求。 |
| 無法將欄位對應套用至欄位 | 無法將對應函數 `'functionName'` 套用至欄位 `'fieldName'`。 陣列不可以是 null。 參數名稱：位元組 | 再次檢查索引子上定義的[欄位](search-indexer-field-mappings.md)對應，並與失敗檔之指定欄位的資料進行比較。 可能需要修改欄位對應或檔資料。 |
| 無法讀取域值 | 無法讀取位於索引 `'fieldIndex'` 的資料行 `'fieldName'` 的值。 從伺服器接收結果時發生傳輸層級錯誤。 （提供者： TCP 提供者，錯誤： 0-遠端主機已強制關閉現有的連接）。 | 這些錯誤通常是因為資料來源的基礎服務發生非預期的連接問題。 請稍後再試著透過索引子執行檔。 |

### <a name="could-not-execute-skill"></a>無法執行技能
索引子無法在技能集中執行技能。

| 原因 | 範例 | 行動 |
| --- | --- | --- |
| 暫時性連線問題 | 發生暫時性錯誤。 請稍後再試。 | 偶爾會發生非預期的連線問題。 請稍後再試著透過索引子執行檔。 |
| 潛在的產品 bug | 發生意外錯誤。 | 這表示不明的失敗類別，可能表示有產品錯誤。 請提出[支援票證](https://ms.portal.azure.com/#create/Microsoft.Support)以取得協助。 |
| 技能在執行期間發生錯誤 | （來自合併技能）有一或多個位移值無效，無法剖析。 專案已插入至文字結尾 | 請使用錯誤訊息中的資訊來修正問題。 這種失敗會需要採取動作來解決。 |

### <a name="could-not-execute-skill-because-the-web-api-request-failed"></a>因為 Web API 要求失敗，所以無法執行技能
因為對 Web API 的呼叫失敗，所以技能執行失敗。 當使用自訂技能時，通常會發生這類失敗，在此情況下，您將需要對自訂程式碼進行偵錯工具以解決此問題。 如果不是來自內建技能的失敗，請參閱錯誤訊息，以取得修正問題的協助。

### <a name="could-not-execute-skill-because-web-api-skill-response-is-invalid"></a>因為 Web API 技能回應無效，所以無法執行技能
技能執行失敗，因為呼叫 Web API 傳回不正確回應。 當使用自訂技能時，通常會發生這類失敗，在此情況下，您將需要對自訂程式碼進行偵錯工具以解決此問題。 如果失敗是來自內建技能，請提出[支援票證](https://ms.portal.azure.com/#create/Microsoft.Support)以取得協助。

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

您可以為 `timeout` 參數設定的最大值為230秒。  如果您的自訂技能無法在230秒內一致地執行，您可以考慮減少自訂技能的 `batchSize`，讓它在單一執行中會有較少的檔要處理。  如果您已經將您的 `batchSize` 設定為1，就必須重寫技能，使其能夠在230秒內執行，或將其分割成多個自訂技能，讓任何單一自訂技能的執行時間最多可達230秒。 如需詳細資訊，請參閱[自訂技能檔](cognitive-search-custom-skill-web-api.md)。

### <a name="could-not-mergeorupload--delete-document-to-the-search-index"></a>無法 ' `MergeOrUpload` ' |搜尋索引的 ' `Delete` ' 檔

已讀取並處理檔，但索引子無法將它新增至搜尋索引。 發生這種情況的原因可能是：

| 原因 | 範例 | 行動 |
| --- | --- | --- |
| 檔中的詞彙大於[32 KB 的限制](search-limits-quotas-capacity.md#api-request-limits) | 欄位包含太大的詞彙 | 您可以確保欄位未設定為可篩選、facetable 或可排序，藉以避免這項限制。
| 檔大於[api 要求大小上限](search-limits-quotas-capacity.md#api-request-limits) | 檔太大，無法編制索引 | [如何為大型資料集編制索引](search-howto-large-index.md)
| 無法連接到目標索引（在重試後仍會繼續），因為服務正在進行其他負載，例如查詢或索引。 | 無法建立連接以更新索引。 搜尋服務正在負荷過重。 | [相應增加您的搜尋服務](search-capacity-planning.md)
| 搜尋服務正在修補以進行服務更新，或處於拓撲重新設定的過程中。 | 無法建立連接以更新索引。 搜尋服務目前已關閉/搜尋服務正在進行轉換。 | 以至少3個複本設定服務，每個[SLA 檔](https://azure.microsoft.com/support/legal/sla/search/v1_0/)的可用性99.9%
| 基礎計算/網路資源（罕見）中的失敗 | 無法建立連接以更新索引。 發生未知的失敗。 | 設定要依照[排程執行](search-howto-schedule-indexers.md)的索引子，以從失敗狀態中收取。

### <a name="could-not-index-document-because-the-indexer-data-to-index-was-invalid"></a>無法索引檔，因為索引的索引子資料無效

已讀取並處理檔，但由於索引欄位的設定不相符，以及索引子所解壓縮資料的本質，因此無法將它新增至搜尋索引。 發生這種情況的原因可能是：

| 原因 | 範例
| --- | ---
| 索引子所解壓縮之欄位的資料類型與對應 [目標索引] 欄位的資料模型不相容。 | 具有索引鍵 '_data_' 之檔中的資料欄位 '_data_' 具有無效值 ' 的類型 ' Edm。字串 ' '。 預期的類型為 ' Collection （Edm. String） '。 |
| 無法從字串值解壓縮任何 JSON 實體。 | 無法將欄位 '_data_' 的類型 ' Edm. 字串 ' ' 剖析為 JSON 物件。 錯誤： ' 剖析值後發現未預期的字元： ' '。 路徑 '_path_'，第1行，位置3162。 ' |
| 無法從字串值解壓縮 JSON 實體的集合。  | 無法將欄位 '_data_' 的類型 ' Edm. 字串 ' ' 剖析為 JSON 陣列。 錯誤： ' 剖析值後發現未預期的字元： ' '。 路徑 ' [0] '，第1行，位置 27. ' |
| 在來源文件中發現未知的類型。 | 無法為未知的類型 '_unknown_' 編制索引 |
| 來源文件中使用了地理位置點的不相容標記法。 | 不支援 WKT 點字串常值。 請改用 GeoJson point 常值 |

在所有這些情況下，請參閱[支援的資料類型（Azure 搜尋服務）](https://docs.microsoft.com/rest/api/searchservice/supported-data-types)和[資料類型對應，Azure 搜尋服務中的索引子](https://docs.microsoft.com/rest/api/searchservice/data-type-map-for-indexers-in-azure-search)，確定您已正確建立索引架構，並已設定適當的[索引子欄位](search-indexer-field-mappings.md)對應。 錯誤訊息將包含有助於追蹤不相符之來源的詳細資料。

### <a name="could-not-process-document-within-indexer-max-run-time"></a>無法處理索引子執行時間上限中的檔

當索引子無法在允許的執行時間內從資料來源完成處理單一檔時，就會發生這個錯誤。 使用技能集時，[執行時間上限](search-limits-quotas-capacity.md#indexer-limits)較短。 當此錯誤發生時，如果您將 maxFailedItems 設定為0以外的值，則索引子會在未來執行時略過檔，讓索引可以進行。 如果您無法承受略過任何檔，或如果您一直看到此錯誤，請考慮將檔分解成較小的檔，以便在單一索引子執行中進行部分進度。

##  <a name="warnings"></a>消息
警告不會停止編制索引，但會指出可能導致未預期結果的狀況。 無論您採取動作與否，都取決於資料和您的案例。

### <a name="could-not-execute-skill-because-a-skill-input-was-invalid"></a>因為技能輸入無效，所以無法執行技能
索引子無法在技能集中執行技能，因為技能的輸入遺漏、錯誤的類型或其他無效。

認知技能具有必要的輸入和選擇性輸入。 例如，關鍵字組的「[提取技能](cognitive-search-skill-keyphrases.md)」有兩個必要的輸入 `text`、`languageCode` 和沒有選擇性的輸入。 如果任何必要的輸入無效，則會略過技能並產生警告。 略過的技能不會產生任何輸出，因此，如果其他技能使用略過的技能輸出，他們可能會產生額外的警告。

如果您想要在遺漏輸入的情況下提供預設值，您可以使用[條件式技能](cognitive-search-skill-conditional.md)來產生預設值，然後使用[條件式技能](cognitive-search-skill-conditional.md)的輸出做為技能輸入。


```json
{
    "@odata.type": "#Microsoft.Skills.Util.ConditionalSkill",
    "context": "/document",
    "inputs": [
        { "name": "condition", "source": "= $(/document/language) == null" },
        { "name": "whenTrue", "source": "= 'en'" },
        { "name": "whenFalse", "source": "= $(/document/language)" }
    ],
    "outputs": [ { "name": "output", "targetName": "languageWithDefault" } ]
}
```

| 原因 | 範例 | 行動 |
| --- | --- | --- |
| 技能輸入的類型錯誤 | 所需的技能輸入 `X` 不是預期的類型 `String`。 所需的技能輸入 `X` 不是預期的格式。 | 某些技能預期特定類型的輸入，例如[情感技能](cognitive-search-skill-sentiment.md)預期 `text` 為字串。 如果輸入指定非字串值，則技能不會執行，而且不會產生任何輸出。 請確定您的資料集具有類型的輸入值一致，或使用[自訂 WEB API 技能](cognitive-search-custom-skill-web-api.md)來前置處理輸入。 如果您要在陣列上逐一查看技能，請檢查技能內容和輸入是否有 `*` 在正確的位置。 通常內容和輸入來源的結尾都應該是陣列的 `*`。 |
| 缺少技能輸入 | 缺少必要的技能輸入 `X`。 | 如果您的所有檔都收到此警告，則輸入路徑中很可能出現錯誤，您應該仔細檢查屬性名稱的大小寫、路徑中的額外或遺失 `*`，以及來自資料來源的檔會定義所需的輸入。 |
| 技能語言代碼輸入無效 | 技能輸入 `languageCode` 具有下列語言代碼 `X,Y,Z`，其中至少有一個無效。 | 請參閱[下方](cognitive-search-common-errors-warnings.md#skill-input-languagecode-has-the-following-language-codes-xyz-at-least-one-of-which-is-invalid)的詳細資料 |

### <a name="skill-input-languagecode-has-the-following-language-codes-xyz-at-least-one-of-which-is-invalid"></a>技能輸入 ' languageCode ' 具有下列語言代碼 ' X，Y，Z '，其中至少有一個無效。
不支援傳遞至選擇性 `languageCode` 輸入下游技能的一個或多個值。 如果您將[LanguageDetectionSkill](cognitive-search-skill-language-detection.md)的輸出傳遞給後續技能，而輸出所包含的語言比這些下游技能所支援的更多，就會發生這種情況。

如果您知道您的資料集全都使用一種語言，則應該移除[LanguageDetectionSkill](cognitive-search-skill-language-detection.md)和 `languageCode` 技能輸入，並改為使用該技能的 `defaultLanguageCode` 技能參數，並假設該技術支援該語言。

如果您知道您的資料集包含多個語言，因此您需要[LanguageDetectionSkill](cognitive-search-skill-language-detection.md)和 `languageCode` 輸入，請考慮新增[ConditionalSkill](cognitive-search-skill-conditional.md) ，以使用不受支援的語言來篩選掉文字，然後才傳入下游技能的文字。  以下是此 EntityRecognitionSkill 可能顯示的範例：

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

### <a name="skill-input-was-truncated"></a>已截斷技能輸入
認知技能對於可以一次分析的文字長度有限制。 如果這些技能的文字輸入超過該限制，我們會截斷文字以符合限制，然後在該截斷的文字上執行擴充。 這表示技能會執行，但不是您所有的資料。

在下面的範例 LanguageDetectionSkill 中，如果超出字元限制，[`'text'` 輸入] 欄位可能會觸發此警告。 您可以在[技能檔](cognitive-search-predefined-skills.md)中找到技能的輸入限制。

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

### <a name="web-api-skill-response-contains-warnings"></a>Web API 技能回應包含警告
索引子可以在技能集中執行技能，但是來自 Web API 要求的回應表示在執行期間出現警告。 請參閱警告以瞭解資料受到影響的方式，以及是否需要採取動作。

### <a name="the-current-indexer-configuration-does-not-support-incremental-progress"></a>目前的索引子設定不支援增量進度
只有 Cosmos DB 資料來源才會發生此警告。

建立索引期間，採累加進度能確保當索引子執行因暫時性失敗或執行時間限制而中斷，索引子仍能夠在下次執行時從中斷的部分繼續建立索引，而不需要重新建立整個集合的索引。 這在建立大型集合的索引時尤其重要。

繼續未完成之索引工作的功能，是前提在擁有以 `_ts` 資料行排序的檔時。 索引子會使用時間戳來決定下一個要挑選的檔。 如果 `_ts` 資料行遺失，或索引子無法判斷自訂查詢是否依其排序，則索引子會在開始時啟動，而您會看到這個警告。

您可以覆寫此行為，使用 [`assumeOrderByHighWatermarkColumn` 設定] 屬性來啟用累加進度，並隱藏這個警告。

[Cosmos DB 增量進度和自訂查詢的詳細資訊。](https://go.microsoft.com/fwlink/?linkid=2099593)