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
ms.subservice: cognitive-search
ms.openlocfilehash: 4e31f818e96ae9f13e3ce8892e575318831848f6
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/27/2019
ms.locfileid: "71329379"
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