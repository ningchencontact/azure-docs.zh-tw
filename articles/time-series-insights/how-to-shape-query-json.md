---
title: 在 Azure 時間序列深入解析查詢中成形 JSON 的最佳做法 | Microsoft Docs
description: 了解如何改善您的 Azure 時間序列深入解析查詢效率。
services: time-series-insights
author: ashannon7
manager: cshankar
ms.service: time-series-insights
ms.topic: article
ms.date: 05/09/2019
ms.author: dpalled
ms.custom: seodec18
ms.openlocfilehash: 089285637bb740fea47f1fd07de0906dfe46662b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66244466"
---
# <a name="shape-json-to-maximize-query-performance"></a>若要最大化查詢效能的 JSON 圖形 

本文章提供有關如何將 Azure Time Series Insights 查詢的效率最大化的 JSON 圖形的指引。

## <a name="video"></a>視訊

### <a name="learn-best-practices-for-shaping-json-to-meet-your-storage-needsbr"></a>了解最佳做法成形以符合您的儲存體需求的 JSON。</br>

> [!VIDEO https://www.youtube.com/embed/b2BD5hwbg5I]

## <a name="best-practices"></a>最佳作法
請思考您將事件傳送至 Time Series Insights。 也就是，您永遠：

1. 盡可能有效率地透過網路傳送資料。
1. 請確定您的資料儲存方式，以便您可以執行適合您案例的彙總。
1. 請確定您不會送達的 Time Series Insights 的最大值的屬性限制：
   - S1 環境為 600 個屬性 (資料行)。
   - S2 環境為 800 個屬性 (資料行)。

下列指導方針可協助確保最佳的可能查詢效能：

1. 請勿使用動態屬性，例如標記識別項，做為屬性名稱。 此使用，因此有助於達到最大值的屬性限制。
1. 請勿傳送不必要的屬性。 如果查詢屬性不是必要項目，最好不要將它傳送。 如此一來，避免儲存體限制。
1. 使用[參考資料](time-series-insights-add-reference-data-set.md)以避免透過網路傳送的靜態資料。
1. 共用更有效率地透過網路傳送資料的多個事件之間的維度屬性。
1. 請勿使用深層陣列巢狀結構。 Time Series Insights 支援最多兩個層級的巢狀包含物件的陣列。 時間序列深入解析會將訊息中的陣列壓的屬性值組的多個事件。
1. 如果所有或大部分的事件只存在少數的量值，最好將這些量值當作相同物件內的個別屬性來傳送。 將它們傳送到個別可減少事件數目，並可能會改善查詢效能，因為需要處理的事件數目較少。 幾個量值時，將它們傳送為中的單一屬性的值降至最低可能會達到最大值的屬性限制。

## <a name="example-overview"></a>範例概觀

下列兩個範例示範如何傳送事件，以反白顯示上述的建議。 下列每個範例中，您可以看到已套用建議的方式。

這些範例是根據多部裝置傳送度量或訊號的案例而來。 度量或號誌，可以是流動率、 引擎石油壓力、 溫度和溼度。 在第一個範例中，有幾個度量會跨所有裝置。 第二個範例有許多裝置，而且每個裝置會傳送許多獨特的度量單位。

## <a name="scenario-one-only-a-few-measurements-exist"></a>案例一：只有少數的度量值存在

> [!TIP]
> 我們建議您傳送給每個度量或信號做為個別的屬性或資料行。

在下列範例中，沒有單一的 Azure IoT 中樞訊息，其中外部陣列都包含共用通用的維度值區段。 外部陣列使用參考資料來提高訊息的效率。 參考資料包含裝置中繼資料不會變更每個事件，但它會提供有用的屬性進行資料分析。 批次處理常見的維度值，以及採用的參考資料可以節省透過網路傳送的位元組，這可讓訊息更有效率。

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

* 具有索引鍵屬性的參考資料資料表**deviceId**:

   | deviceId | messageId | deviceLocation |
   | --- | --- | --- |
   | FXXX | LINE\_DATA | EU |
   | FYYY | LINE\_DATA | US |

* 時間序列深入解析事件資料表，壓平合併後：

   | deviceId | messageId | deviceLocation | timestamp | series.Flow Rate ft3/s | series.Engine Oil Pressure psi |
   | --- | --- | --- | --- | --- | --- |
   | FXXX | LINE\_DATA | EU | 2018-01-17T01:17:00Z | 1.0172575712203979 | 34.7 |
   | FXXX | LINE\_DATA | EU | 2018-01-17T01:17:00Z | 2.445906400680542 | 49.2 |
   | FYYY | LINE\_DATA | US | 2018-01-17T01:18:00Z | 0.58015072345733643 | 22.2 |

在這兩個資料表上的資訊：

- **deviceId** 資料行是車隊中各種裝置的資料行標頭。 使 deviceId 值本身的屬性名稱限制 （適用於 S1 環境） 的 595 或與其他五個資料行 （適用於 S2 環境） 795 裝置總數。
- 如範例的廠牌與型號的資訊，被避免不必要的屬性。 因為屬性不會在未來進行查詢，予以排除啟用更好的網路和儲存空間效率。
- 使用參考資料來減少透過網路傳輸的位元組數目。 兩個屬性**messageId**並**deviceLocation**使用的索引鍵屬性加入**deviceId**。 這項資料在輸入階段聯結的遙測資料，並接著會儲存在時間序列深入解析查詢。
- 使用兩個圖層的巢狀結構，這是支援的時間序列深入解析的巢狀結構的最大數量。 請務必避免巢狀結構很深的陣列。
- 量值會傳送為相同的物件內的個別屬性，因為有幾個量值。 在本例中，**series.Flow Rate psi** 和 **series.Engine Oil Pressure ft3/s** 是唯一的資料行。

## <a name="scenario-two-several-measures-exist"></a>案例二：存在幾個量值

> [!TIP]
> 我們建議您傳送的度量單位為 「 型別，」 「 單位，"和"value"的 tuple。

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

* 有索引鍵屬性的參考資料資料表**deviceId**並**series.tagId**:

   | deviceId | series.tagId | messageId | deviceLocation | type | unit |
   | --- | --- | --- | --- | --- | --- |
   | FXXX | pumpRate | LINE\_DATA | EU | 流動率 | ft3/s |
   | FXXX | oilPressure | LINE\_DATA | EU | 引擎機油壓力 | psi |
   | FYYY | pumpRate | LINE\_DATA | US | 流動率 | ft3/s |
   | FYYY | oilPressure | LINE\_DATA | US | 引擎機油壓力 | psi |

* 時間序列深入解析事件資料表，壓平合併後：

   | deviceId | series.tagId | messageId | deviceLocation | type | unit | timestamp | series.value |
   | --- | --- | --- | --- | --- | --- | --- | --- |
   | FXXX | pumpRate | LINE\_DATA | EU | 流動率 | ft3/s | 2018-01-17T01:17:00Z | 1.0172575712203979 | 
   | FXXX | oilPressure | LINE\_DATA | EU | 引擎機油壓力 | psi | 2018-01-17T01:17:00Z | 34.7 |
   | FXXX | pumpRate | LINE\_DATA | EU | 流動率 | ft3/s | 2018-01-17T01:17:00Z | 2.445906400680542 | 
   | FXXX | oilPressure | LINE\_DATA | EU | 引擎機油壓力 | psi | 2018-01-17T01:17:00Z | 49.2 |
   | FYYY | pumpRate | LINE\_DATA | US | 流動率 | ft3/s | 2018-01-17T01:18:00Z | 0.58015072345733643 |
   | FYYY | oilPressure | LINE\_DATA | US | 引擎機油壓力 | psi | 2018-01-17T01:18:00Z | 22.2 |

在這兩個資料表上的資訊：

- 資料行**deviceId**並**series.tagId**做為資料行標頭，針對各種裝置和團隊中的標記。 使用每個自己的屬性會限制查詢與 594 （適用於 S1 環境） 或 （適用於 S2 環境） 的 794 其他六個資料行使用的裝置總數。
- 第一個範例中所述的原因，被避免不必要的屬性。
- 參考資料用來減少透過網路傳送，藉由引進的位元組數目**deviceId**，它用於唯一配對**messageId**和**deviceLocation**. 複合索引鍵**series.tagId**用於唯一配對**型別**並**單元**。 複合索引鍵可讓**deviceId**並**series.tagId**要用來參考四個值組： **messageId，deviceLocation，輸入，** 並**單元**. 這項資料會在輸入階段聯結的遙測資料。 它接著會查詢儲存在時間序列深入解析。
- 使用兩個圖層的巢狀結構，第一個範例中所述的原因。

### <a name="for-both-scenarios"></a>針對這兩個案例

大量的可能值的屬性，最好是以不同的值，而不是建立新的資料行，每個值的單一資料行內傳送。 在前兩個範例中：

  - 在第一個範例中，有幾個屬性有數個值，因此很適合讓每個個別屬性。
  - 在第二個範例中，量值不被指定為個別的屬性。 相反地，它們是常見的數列屬性底下的量值或值陣列。 新的金鑰**tagId**傳送時，它會建立新的資料行**series.tagId**扁平化資料表中。 新的屬性**型別**並**單元**藉由使用參考資料，如此一來，不會達到屬性限制。

## <a name="next-steps"></a>後續步驟

- 讀取[Azure Time Series Insights 的查詢語法](/rest/api/time-series-insights/ga-query-syntax)若要深入了解查詢語法的 Time Series Insights 資料存取 REST API。
- 了解[圖形事件如何](./time-series-insights-send-events.md)。
