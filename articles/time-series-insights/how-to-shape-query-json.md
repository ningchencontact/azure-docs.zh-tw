---
title: 在 Azure 時間序列深入解析查詢中成形 JSON 的最佳做法 | Microsoft Docs
description: 了解如何改善您的 Azure 時間序列深入解析查詢效率。
services: time-series-insights
author: ashannon7
manager: cshankar
ms.service: time-series-insights
ms.topic: article
ms.date: 05/09/2019
ms.author: anshan
ms.custom: seodec18
ms.openlocfilehash: 535fe9a7920db89e434b4cc1b66aa38bf72a7829
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/16/2019
ms.locfileid: "65790073"
---
# <a name="how-to-shape-json-to-maximize-query-performance"></a>如何成形 JSON 來將查詢效能最大化 

本文提供指引來形成 JSON，將 Azure Time Series Insights 查詢的效率最大化。

## <a name="video"></a>影片

### <a name="learn-best-practices-for-shaping-json-to-meet-your-storage-needsbr"></a>了解最佳做法成形以符合您的儲存體需求的 JSON。</br>

> [!VIDEO https://www.youtube.com/embed/b2BD5hwbg5I]

## <a name="best-practices"></a>最佳做法

請務必考慮如何將事件傳送至時間序列深入解析。 也就是說，您應該一律：

1. 盡可能有效率地透過網路傳送資料。
1. 請確定您的資料會儲存在可讓您執行適合您案例的彙總的方式。
1. 請確定您不會達到的 Time Series Insights 的最大值的屬性限制：
   - S1 環境為 600 個屬性 (資料行)。
   - S2 環境為 800 個屬性 (資料行)。

下列指引有助於確保盡可能獲得最佳查詢效能：

1. 請勿使用動態屬性，例如以標記識別項作為屬性名稱，因為這樣做會導致達到最大屬性限制。
1. 請勿傳送不必要的屬性。 如果不需要某個查詢屬性，最好不要傳送它，以避免達到儲存空間限制。
1. 使用[參考資料](time-series-insights-add-reference-data-set.md)，以避免透過網路傳送靜態資料。
1. 在多個事件之間共用維度屬性，以更有效率地透過網路傳送資料。
1. 請勿使用深層陣列巢狀結構。 Time Series Insights 支援最多兩個層級的巢狀包含物件的陣列。 時間序列深入解析會在訊息中，陣列壓的屬性值組的多個事件。
1. 如果所有或大部分的事件只存在少數的量值，最好將這些量值當作相同物件內的個別屬性來傳送。 個別傳送可減少事件數目，而且由於需要處理的事件數目較少，因此查詢效能可能會較佳。 有數個量值時，將它們當作單一屬性中的值來傳送，可降低達到最大屬性限制的可能性。

## <a name="example-overview"></a>範例概觀

下列兩個範例示範傳送事件，並強調上述建議。 在每個範例之後，您可以查看如何套用建議。

這些範例是根據多部裝置傳送度量或訊號的案例而來。 這些度量或訊號可以是流動率、引擎機油壓力、溫度和溼度。 在第一個範例中，有幾個度量會跨所有裝置。 在第二個範例中，有許多裝置，每部裝置會傳送許多唯一的度量。

## <a name="scenario-one-only-a-few-measurements-exist"></a>案例一： 只有少數的度量值存在

> [!TIP]
> **建議**： 將每個度量/訊號傳送的個別屬性/資料行。

在下列範例中，有一個 IoT 中樞訊息，其中外部陣列包含通用維度值的共用區段。 外部陣列使用參考資料來提高訊息的效率。 參考資料包含裝置中繼資料，不會隨每個事件變更，但會提供有用的屬性以進行資料分析。 批次處理通用維度值，以及採用參考資料，都可以節省透過網路傳送的位元組數目，讓訊息更有效率。

範例 JSON 承載：

```json
[
    {
        "deviceId": "FXXX",
        "timestamp": "2018-01-17T01:17:00Z",
        "series": [
            {
                "Flow Rate ft3/s": 1.0172575712203979,
                "Engine Oil Pressure psi ": 34.7
            },
            {
                "Flow Rate ft3/s": 2.445906400680542,
                "Engine Oil Pressure psi ": 49.2
            }
        ]
    },
    {
        "deviceId": "FYYY",
        "timestamp": "2018-01-17T01:18:00Z",
        "series": [
            {
                "Flow Rate ft3/s": 0.58015072345733643,
                "Engine Oil Pressure psi ": 22.2
            }
        ]
    }
]
```

* 參考資料資料表 (索引鍵屬性是**deviceId**):

   | deviceId | messageId | deviceLocation |
   | --- | --- | --- |
   | FXXX | LINE\_DATA | EU |
   | FYYY | LINE\_DATA | US |

* 時間序列見解事件資料表 (壓平合併後)：

   | deviceId | messageId | deviceLocation | timestamp | series.Flow Rate ft3/s | series.Engine Oil Pressure psi |
   | --- | --- | --- | --- | --- | --- |
   | FXXX | LINE\_DATA | EU | 2018-01-17T01:17:00Z | 1.0172575712203979 | 34.7 |
   | FXXX | LINE\_DATA | EU | 2018-01-17T01:17:00Z | 2.445906400680542 | 49.2 |
   | FYYY | LINE\_DATA | US | 2018-01-17T01:18:00Z | 0.58015072345733643 | 22.2 |

上方：

- **deviceId** 資料行是車隊中各種裝置的資料行標頭。 嘗試將 deviceId 值設為其本身的屬性名稱，搭配其他五個資料行，會將總裝置數限制為 595 (S1 環境) 或 795 (S2 環境)。

- 避免不必要的屬性，例如廠牌和型號資訊等。由於未來不會查詢這些屬性，因此予以排除可改善網路和儲存空間效率。

- 使用參考資料來減少透過網路傳輸的位元組數目。 兩個屬性**messageId**並**deviceLocation**，使用索引鍵的屬性，聯結**deviceId**。 這項資料是在輸入時，加入的遙測資料，而且之後會儲存在時間序列深入解析查詢。

- 使用兩個圖層的巢狀結構，這是支援的時間序列深入解析的巢狀結構的最大數量。 請務必避免巢狀結構很深的陣列。

- 量值會當作相同物件內的個別屬性來傳送，因為量值數目很少。 在本例中，**series.Flow Rate psi** 和 **series.Engine Oil Pressure ft3/s** 是唯一的資料行。

## <a name="scenario-two-several-measures-exist"></a>案例二： 存在幾個量值

> [!TIP]
> **建議：** 將度量當作 "type"、"unit"、"value" Tuple 來傳送。

範例 JSON 承載：

```json
[
    {
        "deviceId": "FXXX",
        "timestamp": "2018-01-17T01:17:00Z",
        "series": [
            {
                "tagId": "pumpRate",
                "value": 1.0172575712203979
            },
            {
                "tagId": "oilPressure",
                "value": 49.2
            },
            {
                "tagId": "pumpRate",
                "value": 2.445906400680542
            },
            {
                "tagId": "oilPressure",
                "value": 34.7
            }
        ]
    },
    {
        "deviceId": "FYYY",
        "timestamp": "2018-01-17T01:18:00Z",
        "series": [
            {
                "tagId": "pumpRate",
                "value": 0.58015072345733643
            },
            {
                "tagId": "oilPressure",
                "value": 22.2
            }
        ]
    }
]
```

* 參考資料 (索引鍵屬性是**deviceId**並**series.tagId**):

   | deviceId | series.tagId | messageId | deviceLocation | 類型 | 單位 |
   | --- | --- | --- | --- | --- | --- |
   | FXXX | pumpRate | LINE\_DATA | EU | 流動率 | ft3/s |
   | FXXX | oilPressure | LINE\_DATA | EU | 引擎機油壓力 | psi |
   | FYYY | pumpRate | LINE\_DATA | US | 流動率 | ft3/s |
   | FYYY | oilPressure | LINE\_DATA | US | 引擎機油壓力 | psi |

* 時間序列見解事件資料表 (壓平合併後)：

   | deviceId | series.tagId | messageId | deviceLocation | 類型 | 單位 | timestamp | series.value |
   | --- | --- | --- | --- | --- | --- | --- | --- |
   | FXXX | pumpRate | LINE\_DATA | EU | 流動率 | ft3/s | 2018-01-17T01:17:00Z | 1.0172575712203979 | 
   | FXXX | oilPressure | LINE\_DATA | EU | 引擎機油壓力 | psi | 2018-01-17T01:17:00Z | 34.7 |
   | FXXX | pumpRate | LINE\_DATA | EU | 流動率 | ft3/s | 2018-01-17T01:17:00Z | 2.445906400680542 | 
   | FXXX | oilPressure | LINE\_DATA | EU | 引擎機油壓力 | Psi | 2018-01-17T01:17:00Z | 49.2 |
   | FYYY | pumpRate | LINE\_DATA | US | 流動率 | ft3/s | 2018-01-17T01:18:00Z | 0.58015072345733643 |
   | FYYY | oilPressure | LINE\_DATA | US | 引擎機油壓力 | psi | 2018-01-17T01:18:00Z | 22.2 |

上方：

- 資料行**deviceId**並**series.tagId**做為資料行標頭，針對各種裝置和團隊中的標記。 使用每個標頭作為其本身的屬性，搭配其他六個資料行，會將查詢限制為總裝置數 594 (S1 環境) 或 794 (S2 環境)。

- 第一個範例中所述的原因，被避免不必要的屬性。

- 參考資料用來減少透過網路傳送，藉由引進的位元組數目**deviceId**，唯一的配對**messageId**並**deviceLocation**。 針對唯一的 **type** 和 **unit** 組合，則會使用複合索引鍵 **series.tagId**。 複合索引鍵允許使用 **deviceId** 和 **series.tagId** 組來參考四個值：**messageId、deviceLocation、type** 和 **unit**。 這項資料是在輸入時，加入的遙測資料，而且之後會儲存在時間序列深入解析查詢。

- 使用兩個圖層的巢狀結構，第一個範例中所述的原因。

### <a name="for-both-scenarios"></a>針對這兩個案例

如果您的屬性具有大量可能值，最好當作單一資料行內的相異值來傳送，而不是為每個值建立新的資料行。 在前兩個範例中：

  - 在第一個範例中，具有數個值的屬性並不多，因此適合將每個屬性當作個別的屬性。
  - 不過，在第二個範例中，您可以看到量值並未指定為個別屬性，而是指定為通用數列屬性下的值/量值陣列。 新的索引鍵 **tagId** 會送出，以在扁平化資料表中建立新的資料行 **series.tagId**。 使用參考資料可建立新的屬性 **type** 和 **unit**，以避免達到屬性限制。

## <a name="next-steps"></a>後續步驟

- 讀取[Azure Time Series Insights 的查詢語法](/rest/api/time-series-insights/ga-query-syntax)若要深入了解查詢語法的 Time Series Insights 資料存取 REST API。

- 了解[圖形事件如何](./time-series-insights-send-events.md)。
