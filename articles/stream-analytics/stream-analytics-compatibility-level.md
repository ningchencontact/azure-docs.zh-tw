---
title: 了解 Azure 串流分析作業的相容性層級
description: 了解如何為 Azure 串流分析作業設定相容性層級，並了解最新相容性層級中的重大變更
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/08/2019
ms.openlocfilehash: 6fb93152263d253de983b17d25f02f4c68a172fd
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/09/2019
ms.locfileid: "59361397"
---
# <a name="compatibility-level-for-azure-stream-analytics-jobs"></a>Azure 串流分析作業的相容性層級
 
相容性層級是指 Azure 串流分析服務的版本專屬行為。 Azure 串流分析是受控服務，會定期更新功能及改善效能。 通常更新會自動提供給終端使用者。 不過，某些新功能可能會引進重大變更，例如現有作業的行為變更、從這些作業取用資料的流程變更等等。相容性層級用來代表串流分析中引入的重大變更。 重大變更一律會隨新的相容性層級引入。 

相容性層級可確保現有作業執行時不發生任何失敗。 當您建立新的 Stream Analytics 作業時，最好使用最新的相容性層級加以建立。 
 
## <a name="set-a-compatibility-level"></a>設定相容性層級 

相容性層級可控制串流分析作業的執行階段行為。 您可以使用入口網站或使用[建立作業 REST API 呼叫](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-job)來設定串流分析作業的相容性層級。 Azure 串流分析目前支援兩個相容性層級 - "1.0" 和 "1.1"。 根據預設，相容性層級會設定為 "1.0"，其是在 Azure 串流分析正式運作期間所引入。 若要更新預設值，瀏覽至您現有的串流分析作業 > 選取 [設定] 區段中的 [相容性層級] 選項，然後變更值。 

請確定在更新相容性層級之前停止作業。 如果您的作業處於執行中狀態，則無法更新相容性層級。 

![Azure 入口網站中的串流分析相容性層級](media/stream-analytics-compatibility-level/stream-analytics-compatibility.png)

 
更新相容性層級時，T-SQL 編譯器會以對應至所選相容性層級的語法來驗證作業。 

## <a name="major-changes-in-the-latest-compatibility-level-12"></a>最新的相容性層級 (1.2) 的重大變更

相容性層級 1.2 中導入下列重大變更：

### <a name="geospatial-functions"></a>GeoSpatial 函式 

**舊版：** Azure Stream Analytics 會使用地理位置計算。

**目前的版本：** Azure Stream Analytics 可讓您計算幾何投射的地理座標。 不沒有地理空間函式的簽章中的任何變更。 不過，其語意會稍有不同，所以比之前更精確的計算。

Azure Stream Analytics 支援開放式地理空間的參考資料編製索引。 包含地理空間元素的參考資料可以進行更快速的聯結計算編製索引。

已更新的地理空間函式會將完整的已知文字 (well-known text，WKT) 地理空間格式表示。 您可以指定其他先前不支援的開放式地理空間元件以 GeoJson。

如需詳細資訊，請參閱 <<c0> [ 更新為在 Azure Stream Analytics-雲端和 IoT Edge 中的地理空間功能](https://azure.microsoft.com/blog/updates-to-geospatial-functions-in-azure-stream-analytics-cloud-and-iot-edge/)。

### <a name="parallel-query-execution-for-input-sources-with-multiple-partitions"></a>具有多個資料分割的輸入來源執行平行查詢 

**舊版：** Azure Stream Analytics 查詢需要使用 PARTITION BY 子句來在輸入的來源資料分割平行查詢處理。

**目前的版本：** 如果在輸入的來源資料分割可平行處理查詢邏輯，Azure Stream Analytics 會建立個別的查詢執行個體，並且會平行執行計算。

### <a name="native-bulk-api-integration-with-cosmosdb-output"></a>使用 CosmosDB 輸出的原生大量 API 整合

**舊版：** 更新插入行為*插入或合併*。

**目前的版本：** 使用 CosmosDB 輸出的原生大量 API 整合最大化輸送量，並可以有效率地處理節流的要求。

更新插入行為*插入或取代*。

### <a name="datetimeoffset-when-writing-to-sql-output"></a>DateTimeOffset 時寫入至 SQL 輸出

**舊版：**[DateTimeOffset](https://docs.microsoft.com/sql/t-sql/data-types/datetimeoffset-transact-sql?view=sql-server-2017)類型已調整為 UTC。

**目前的版本：** DateTimeOffset 會不會再進行調整。

### <a name="strict-validation-of-prefix-of-functions"></a>嚴格驗證的前置詞的函式

**舊版：** 發生的函式的前置詞沒有嚴格的驗證。

**目前的版本：** Azure Stream Analytics 有嚴格驗證的函式的前置詞。 將前置詞新增至內建函式會導致錯誤。 比方說，`myprefix.ABS(…)`不受支援。

將前置詞新增至內建彙總也會導致錯誤。 比方說，`myprefix.SUM(…)`不受支援。

使用前置詞"system"的任何使用者定義函式會導致錯誤。

### <a name="disallow-array-and-object-as-key-properties-in-cosmos-db-output-adapter"></a>不允許為 Cosmos DB 輸出配接器中的索引鍵屬性的陣列和物件

**舊版：** 陣列和物件的型別所支援作為索引鍵屬性。

**目前的版本：** 做為索引鍵屬性不會再支援陣列和物件的類型。


## <a name="next-steps"></a>後續步驟
* [疑難排解 Azure Stream Analytics 的輸入](stream-analytics-troubleshoot-input.md)
* [Stream Analytics 資源健康狀態](stream-analytics-resource-health.md)
