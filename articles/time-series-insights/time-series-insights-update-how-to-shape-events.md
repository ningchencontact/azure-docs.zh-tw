---
title: 使用 Azure 時間序列深入解析為事件塑形 | Microsoft Docs
description: 瞭解如何使用 Azure 時間序列深入解析 Preview 來塑造事件。
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 10/22/2019
ms.custom: seodec18
ms.openlocfilehash: f8a50e062d2dac1f30f8b745f351570262daac53
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/28/2019
ms.locfileid: "72990884"
---
# <a name="shape-events-with-azure-time-series-insights-preview"></a>使用 Azure 時間序列深入解析為事件塑形

此文章可協助您為 JSON 檔案塑形以最佳化 Azure 時間序列深入解析預覽版查詢的效率。

## <a name="best-practices"></a>最佳作法

考慮如何將事件傳送至時間序列深入解析 Preview。 也就是說，您應該一律：

* 盡可能有效率地透過網路傳送資料。
* 以可協助您針對您的案例更適當地彙總資料的方式存放資料。

若要獲得最佳查詢效能，請執行下列動作：

* 請勿傳送不必要的屬性。 時間序列深入解析預覽版會根據您的使用量向您收費。 最好存放並存取您將查詢的資料。
* 為統計資料使用執行個體欄位。 此實務可協助您避免透過網路傳送統計資料。 實例欄位是時間序列模型的元件，其運作方式就像是時間序列深入解析服務中正式推出的參考資料。 若要深入瞭解實例欄位，請參閱[時間序列模型](./time-series-insights-update-tsm.md)。
* 在兩個或更多事件之間共用維度屬性。 此實務可協助您更有效率地透過網路傳送資料。
* 請勿使用深層陣列巢狀結構。 時間序列深入解析 Preview 最多支援兩個包含物件的嵌套陣列層級。 時間序列深入解析會將訊息中的陣列壓平合併為具有屬性值組的多個事件。
* 如果所有或大部分的事件只存在少數的量值，最好將這些量值當作相同物件內的個別屬性來傳送。 將它們分開傳送可減少事件數目，而且可能會改善查詢效能，因為需要處理的事件較少。

## <a name="example"></a>範例

下列範例是以兩部或多部裝置傳送度量或訊號的案例為基礎。 測量或信號可以是*流動率*、*引擎石油壓力*、*溫度*和*濕度*。

在此範例中，有一個 Azure IoT 中樞訊息，其中外部陣列包含通用維度值的共用區段。 外部陣列使用時間序列執行個體資料來提高訊息的效率。 

時間序列實例包含裝置中繼資料。 此中繼資料不會隨著每個事件而變更，但它會提供有用的資料分析屬性。 若要節省透過網路傳送的位元組，並讓訊息更有效率，請考慮批次處理通用維度值，並使用時間序列實例中繼資料。

### <a name="example-json-payload"></a>範例 JSON 承載

```JSON
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

### <a name="time-series-instance"></a>時間序列執行個體 
> [!NOTE]
> 時間序列識別碼是 *deviceId*。

```JSON
{
    "timeSeriesId": [
      "FXXX"
    ],
    "typeId": "17150182-daf3-449d-adaf-69c5a7517546",
    "hierarchyIds": [
      "b888bb7f-06f0-4bfd-95c3-fac6032fa4da"
    ],
    "description": null,
    "instanceFields": {
      "L1": "REVOLT SIMULATOR",
      "L2": "Battery System",
    }
  },
  {
    "timeSeriesId": [
      "FYYY"
    ],
    "typeId": "17150182-daf3-449d-adaf-69c5a7517546",
    "hierarchyIds": [
      "b888bb7f-06f0-4bfd-95c3-fac6032fa4da"
    ],
    "description": null,
    "instanceFields": {
      "L1": "COMMON SIMULATOR",
      "L2": "Battery System",
    }
  },
```

時間序列深入解析預覽版會在查詢期間聯結資料表 (在壓平之後)。 該資料表包括額外的資料行，例如**類型**。 下列範例會示範如何[塑造](./time-series-insights-send-events.md#supported-json-shapes)您的遙測資料。

| deviceId  | 類型 | L1 | L2 | timestamp | series.Flow Rate ft3/s | series.Engine Oil Pressure psi |
| ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| `FXXX` | Default_Type | SIMULATOR | Battery System | 2018-01-17T01:17:00Z |   1.0172575712203979 |    34.7 |
| `FXXX` | Default_Type | SIMULATOR |   Battery System |    2018-01-17T01:17:00Z | 2.445906400680542 |  49.2 |
| `FYYY` | LINE_DATA    COMMON | SIMULATOR |    Battery System |    2018-01-17T01:18:00Z | 0.58015072345733643 |    22.2 |

在上面的範例中，請注意下列各點：

* 統計屬性是存放在時間序列深入解析預覽版中，以最佳化透過網路傳素的資料。
* 時間序列深入解析預覽資料會透過實例中所定義的時間序列識別碼，在查詢時聯結。
* 會使用兩層的嵌套。 此數位是時間序列深入解析 Preview 支援的最多。 請務必避免巢狀結構很深的陣列。
* 因為有一些量值，它們會在相同的物件中以不同的屬性傳送。 在此範例中，**series.Flow Rate psi**、**series.Engine Oil Pressure psi** 與 **series.Flow Rate ft3/s** 是唯一資料行。

>[!IMPORTANT]
> 實例欄位不會與遙測一起儲存。 它們會與中繼資料一起儲存在時間序列模型中。
> 上面的資料表代表查詢檢視。

## <a name="next-steps"></a>後續步驟

- 若要實作這些指導方針，請參閱 [Azure 時間序列深入解析預覽版查詢語法](./time-series-insights-query-data-csharp.md)。 您將深入瞭解適用于資料存取時間序列深入解析預覽 REST API 的查詢語法。
- 若要了解支援的 JSON 塑形，請參閱[支援的 JSON 塑形](./time-series-insights-send-events.md#supported-json-shapes)。
