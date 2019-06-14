---
title: 了解 Azure 串流分析作業的相容性層級
description: 了解如何為 Azure 串流分析作業設定相容性層級，並了解最新相容性層級中的重大變更
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 5/2/2019
ms.openlocfilehash: c1b2875e6899d2301a4c4b564882214dc7bc4981
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "65205465"
---
# <a name="compatibility-level-for-azure-stream-analytics-jobs"></a>Azure 串流分析作業的相容性層級

本文說明在 Azure Stream Analytics 中的相容性層級選項。 Stream Analytics 是受控的服務，定期更新功能和效能改進。 大部分服務的執行階段更新的自動都會提供給使用者。 

不過，在服務中的一些新功能可能會造成重大的變更，例如現有的工作，此行為變更，或者中執行的作業取用資料的方式變更。 您可以保留現有的 Stream Analytics 作業執行時藉由保留設定降低相容性層級並沒有重大變更。 當您準備好進行的最新的執行階段行為，可以在您選擇藉由引發相容性層級。 

## <a name="choose-a-compatibility-level"></a>選擇 相容性層級

相容性層級可控制串流分析作業的執行階段行為。 

Azure Stream Analytics 目前支援三個的相容性層級：

* 1.0-先前的行為
* 1.1-預設行為
* 1.2 （預覽）-最新的增強功能評估最新的行為

期間正式運作的 Azure Stream Analytics 幾年前就已經導入的原始 1.0 相容性層級。

當您建立新的 Stream Analytics 作業時，最好使用最新的相容性層級加以建立。 開始您作業的設計依賴最新的行為，以避免稍後新增的變更和複雜度。

## <a name="set-the-compatibility-level"></a>設定相容性層級

在 Azure 入口網站或使用，您可以設定的 Stream Analytics 作業的相容性層級[建立作業 REST API 呼叫](/rest/api/streamanalytics/stream-analytics-job)。

若要更新相容性層級，在 Azure 入口網站中的作業：

1. 使用[Azure 入口網站](https://portal.azure.com)找出您的 Stream Analytics 作業。
2. **停止**之前更新的相容性層級作業。 如果您的作業處於執行中狀態，則無法更新相容性層級。
3. 底下**設定**標題之下，選取**相容性層級**。
4. 選擇您想要的相容性層級值。
5. 選取 **儲存**在頁面底部。

![Azure 入口網站中的串流分析相容性層級](media/stream-analytics-compatibility-level/stream-analytics-compatibility.png)

更新相容性層級時，T-SQL 編譯器會以對應至所選相容性層級的語法來驗證作業。

## <a name="compatibility-level-12-preview"></a>相容性層級 1.2 （預覽）

相容性層級 1.2 中導入下列重大變更：

### <a name="geospatial-functions"></a>GeoSpatial 函式

**先前的層級：** Azure Stream Analytics 會使用地理位置計算。

**1.2 的層級：** Azure Stream Analytics 可讓您計算幾何投射的地理座標。 不沒有地理空間函式的簽章中的任何變更。 不過，其語意會稍有不同，所以比之前更精確的計算。

Azure Stream Analytics 支援開放式地理空間的參考資料編製索引。 包含地理空間元素的參考資料可以進行更快速的聯結計算編製索引。

已更新的地理空間函式會將完整的已知文字 (well-known text，WKT) 地理空間格式表示。 您可以指定其他先前不支援的開放式地理空間元件以 GeoJson。

如需詳細資訊，請參閱 <<c0> [ 更新為在 Azure Stream Analytics-雲端和 IoT Edge 中的地理空間功能](https://azure.microsoft.com/blog/updates-to-geospatial-functions-in-azure-stream-analytics-cloud-and-iot-edge/)。

### <a name="parallel-query-execution-for-input-sources-with-multiple-partitions"></a>具有多個資料分割的輸入來源執行平行查詢

**先前的層級：** Azure Stream Analytics 查詢需要使用 PARTITION BY 子句來在輸入的來源資料分割平行查詢處理。

**1.2 的層級：** 如果在輸入的來源資料分割可平行處理查詢邏輯，Azure Stream Analytics 會建立個別的查詢執行個體，並且會平行執行計算。

### <a name="native-bulk-api-integration-with-cosmosdb-output"></a>使用 CosmosDB 輸出的原生大量 API 整合

**先前的層級：** 更新插入行為*插入或合併*。

**1.2 的層級：** 使用 CosmosDB 輸出的原生大量 API 整合最大化輸送量，並可以有效率地處理節流的要求。 如需詳細資訊，請參閱 < [Azure Stream Analytics 輸出至 Azure Cosmos DB 頁面](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-documentdb-output#improved-throughput-with-compatibility-level-12)。

更新插入行為*插入或取代*。

### <a name="datetimeoffset-when-writing-to-sql-output"></a>DateTimeOffset 時寫入至 SQL 輸出

**先前的層級：** [DateTimeOffset](https://docs.microsoft.com/sql/t-sql/data-types/datetimeoffset-transact-sql?view=sql-server-2017)類型已調整為 UTC。

**1.2 的層級：** DateTimeOffset 會不會再進行調整。

### <a name="strict-validation-of-prefix-of-functions"></a>嚴格驗證的前置詞的函式

**先前的層級：** 發生的函式的前置詞沒有嚴格的驗證。

**1.2 的層級：** Azure Stream Analytics 有嚴格驗證的函式的前置詞。 將前置詞新增至內建函式會導致錯誤。 比方說，`myprefix.ABS(…)`不受支援。

將前置詞新增至內建彙總也會導致錯誤。 比方說，`myprefix.SUM(…)`不受支援。

使用前置詞"system"的任何使用者定義函式會導致錯誤。

### <a name="disallow-array-and-object-as-key-properties-in-cosmos-db-output-adapter"></a>不允許為 Cosmos DB 輸出配接器中的索引鍵屬性的陣列和物件

**先前的層級：** 陣列和物件的型別所支援作為索引鍵屬性。

**1.2 的層級：** 做為索引鍵屬性不會再支援陣列和物件的類型。

## <a name="compatibility-level-11"></a>相容性層級 1.1

相容性層級 1.1 中引入了下列重大變更：

### <a name="service-bus-xml-format"></a>服務匯流排 XML 格式

**1.0 的層級：** Azure 串流分析使用 DataContractSerializer，因此訊息內容包含 XML 標籤。 例如:

`@\u0006string\b3http://schemas.microsoft.com/2003/10/Serialization/\u0001{ "SensorId":"1", "Temperature":64\}\u0001`

**1.1 的層級：** 訊息內容直接包含串流，而不含其他標籤。 例如：`{ "SensorId":"1", "Temperature":64}`

### <a name="persisting-case-sensitivity-for-field-names"></a>欄位名稱保持大小寫區分

**1.0 的層級：** 由 Azure 串流分析引擎處理時，欄位名稱會變更為小寫。

**1.1 的層級：** Azure Stream Analytics 引擎處理它們時，將會保存的欄位名稱的區分大小寫。

> [!NOTE]
> 使用 Edge 環境所裝載的串流分析作業尚無法使用保持大小寫區分的功能。 因此，如果您的作業裝載在 Edge 上，所有欄位名稱都會轉換為小寫。

### <a name="floatnandeserializationdisabled"></a>FloatNaNDeserializationDisabled

**1.0 的層級：** CREATE TABLE 命令並未在「浮點數 (FLOAT)」資料行型別中篩選具 NaN (不是數字 (Not-a-Number。 例如，Infinity, -Infinity) 的事件，因其不符合這些數字的記載範圍。

**1.1 的層級：** CREATE TABLE 可讓您指定強式結構描述。 串流分析引擎會驗證資料是否符合此結構描述。 使用此模型，命令可以篩選具有 NaN 值的事件。

### <a name="disable-automatic-upcast-for-datetime-strings-in-json"></a>停用自動向上轉型，在 JSON 中的日期時間字串

**1.0 的層級：** JSON 剖析器會自動將具有日期/時間/區域資訊的字串值向上轉型 (upcast) 為日期時間型別，再將它轉換成 UTC。 此行為會導致遺失時區資訊。

**1.1 的層級：** 不再自動將具有日期/時間/區域資訊的字串值向上轉型 (upcast) 為日期時間型別。 如此一來，即可保留時區資訊。

## <a name="next-steps"></a>後續步驟

* [對 Azure 串流分析輸入進行疑難排解](stream-analytics-troubleshoot-input.md)
* [Stream Analytics 資源健康狀態](stream-analytics-resource-health.md)
