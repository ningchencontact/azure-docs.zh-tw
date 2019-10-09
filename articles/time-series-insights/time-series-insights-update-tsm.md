---
title: Azure 時間序列深入解析預覽中的時間序列模型 | Microsoft Docs
description: 了解 Azure 時間序列深入解析時間序列模型。
author: ashannon7
ms.author: dpalled
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 10/07/2019
ms.custom: seodec18
ms.openlocfilehash: 8f4ed6de5ff47efa441c371b80670c500f57c9bb
ms.sourcegitcommit: f9e81b39693206b824e40d7657d0466246aadd6e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/08/2019
ms.locfileid: "72034056"
---
# <a name="time-series-model"></a>時間序列模型

本文描述屬於 Azure 時間序列深入解析一部分的時間序列模型。 它討論模型本身、其功能，以及如何開始建置和更新您自己的模型。

傳統上，從 IoT 裝置收集的資料缺發關聯的資訊，因此很難快速地尋找及分析感應器。 時間序列模型主要動機是簡化尋找及分析 IoT 資料。 為了達到此目標，它藉由提供鑑藏、維護和擴充時間序列資料的功能，來協助準備取用者可用的資料集。

時間序列模型在查詢和瀏覽中扮演重要角色，因為它們將裝置和非裝置實體關聯化。 保留在時間序列模型中的資料，利用其中儲存的公式來提供時間序列查詢計算。

[@no__t 1Time 系列模型總覽](media/v2-update-tsm/tsm.png)](media/v2-update-tsm/tsm.png#lightbox)

## <a name="key-capabilities"></a>主要功能

為了達到簡單輕鬆就能管理時間序列關聯化的目的，時間序列模型在時間序列深入解析中提供下列功能。 它可協助您：

* 撰寫及管理計算或公式、轉換資料運用純量函數、彙總作業等等。
* 定義父子式關聯性以提供導覽和參考功能，也提供時間序列遙測的內容。
* 將與執行個體相關聯的屬性定義為「執行個體欄位」的一部分，並使用它們來建立階層。

## <a name="entity-components"></a>實體元件

時間序列模型有三個核心元件：

* <a href="#time-series-model-types">時間序列模型類型</a>
* <a href="#time-series-model-hierarchies">時間序列模型階層</a>
* <a href="#time-series-model-instances">時間序列模型實例</a>

這些元件會結合以指定時間序列模型，並組織您的 Azure 時間序列深入解析資料。

## <a name="time-series-model-types"></a>時間序列模型類型

時間序列模型「類型」協助您定義執行計算的變數或公式。 類型與特定時間序列深入解析執行個體相關聯。 一個類型可以有一或多個變數。 例如，某個時間序列深入解析執行個體可能是「溫度感應器」，包含的變數有「平均溫度」、「最低溫度」和「最高溫度」。 我們會在資料開始流入時間序列深入解析時建立預設類型。 預設類型可以從模型設定擷取及更新。 預設類型有計算事件數目的變數。

### <a name="time-series-model-type-json-example"></a>時間序列模型類型 JSON 範例

範例：

```JSON
{
    "name": "SampleType",
    "description": "This is sample type",
    "variables": {
        "Avg Temperature": {
            "kind": "numeric",
            "filter": null,
            "value": { "tsx": "$event.temperature.Double" },
            "aggregation": {"tsx": "avg($value)"}
        },
        "Count Temperature": {
            "kind": "aggregate",
            "filter": null,
            "value": null,
            "aggregation": {"tsx": "count()"}
        }
    }
}
```

如需時間序列模型類型的詳細資訊，請參閱[參考文件](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#types-api) \(英文\)。

### <a name="variables"></a>變數

時間序列深入解析類型有變數，它們是計算來自事件之值的具名計算。 時間序列深入解析變數定義包含公式和計算規則。 變數定義包括「種類」、「值」、「篩選條件」、「縮減」和「界限」。 變數儲存在時間序列模型中的類型定義中，並且可透過查詢 API 由內嵌方式提供，以覆寫儲存的定義。

下列矩陣是當作變數定義的說明：

[@no__t 1Type 變數定義表](media/v2-update-tsm/table.png)](media/v2-update-tsm/table.png#lightbox)

| 定義 | 描述 |
| --- | ---|
| 變數種類 |  支援*數值*和*匯總*種類 |
| 變數篩選條件 | 變數的篩選條件指定選擇性的篩選子句，以根據條件來限制視為計算的資料列數目。 |
| 變數值 | 變數值會用於且應該用於計算中。 要針對有問題的資料點參考的相關欄位。 |
| 變數彙總 | 變數的彙總函式提供計算的一部分。 時間序列深入解析支援一般彙總 (也就是「最小值」、「最大值」、「平均」、「加總」和「計數」)。 |

## <a name="time-series-model-hierarchies"></a>時間序列模型階層

階層藉由指定屬性名稱和其關係來組織執行個體。 您可能會有單一階層或多個階層。 它們不需要是您目前資料的一部分，但每個執行個體都應該對應至階層。 時間序列模型執行個體可以對應到單一階層或多個階層。

階層由「階層識別碼」、「名稱」和「來源」定義。 階層有路徑，它是使用者想建立的由上而下、父子式順序的階層。 父子式屬性會對應「執行個體欄位」。

### <a name="time-series-model-hierarchy-json-example"></a>時間序列模型階層 JSON 範例

範例：

```JSON
{
    "id": "4c6f1231-f632-4d6f-9b63-e366d04175e3",
    "name": "Location",
    "source": {
        "instanceFieldNames": [
                "state",
                "city"
            ]
    }
}
```

如需時間序列模型階層的詳細資訊，請參閱[參考文件](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#hierarchies-api) \(英文\)。

### <a name="hierarchy-definition-behavior"></a>階層定義行為

請考慮下列的範例，其中階層 H1 有 *building*、*floor* 和 *room* 作為其定義的一部分：

```plaintext
 H1 = [“building”, “floor”, “room”]
```

依「執行個體欄位」而定，階層屬性和值會顯示如下表中所示：

| 時間序列識別碼 | 執行個體欄位 |
| --- | --- |
| ID1 | "building" = "1000", "floor" = "10", "room" = "55"  |
| ID2 | "building" = "1000", "room" = "55" |
| ID3 | "floor" = "10" |
| ID4 | "building" = "1000", "floor" = "10"  |
| ID5 | 未設定 “building”、“floor” 或 “room” |

在上述範例中， **ID1**和**ID4**會在 Azure 時間序列深入解析 explorer 中顯示為階層 H1 的一部分，而其餘部分則分類為*無上層實例*，因為它們不符合指定的資料層次。

## <a name="time-series-model-instances"></a>時間序列模型執行個體

執行個體就是時間序列本身。 在大部分情況下，它們是 *deviceId* 或 *assetId*，也就是資產在環境中的唯一識別碼。 執行個體有與其相關聯的描述性資訊，稱為執行個體屬性。 執行個體屬性至少會包含階層資訊。 它們也可以包含有用的描述性資料，像是製造商、操作員或上次維護日期。

執行個體由 typeId、timeSeriesId、name、description、hierarchyIds 和 instanceFields 定義。 每個執行個體只對應至一個「類型」和一或多個階層。 執行個體會從階層繼承所有屬性，且可以針對近一步的執行個體屬性定義，新增額外的 *instanceFields*。

*instanceFields* 是執行個體的屬性，以及定義執行個體的任何靜態資料。 它們定義階層或非階層的值，同時也支援建立索引以執行搜尋作業。

*Name*屬性是選擇性的，而且區分大小寫。 如果不使用 name，則會預設為時間序列識別碼。 如果有提供 name，則時間序列識別碼仍然可在 Well (總管內圖表下方的方格) 中取得。

### <a name="time-series-model-instance-json-example"></a>時間序列模型執行個體 JSON 範例

範例：

```JSON
{
    "typeId": "1be09af9-f089-4d6b-9f0b-48018b5f7393",
    "timeSeriesId": ["sampleTimeSeriesId"],
    "name": "sampleName",
    "description": "Sample Instance",
    "hierarchyIds": [
        "1643004c-0a84-48a5-80e5-7688c5ae9295"
    ],
    "instanceFields": {
        "state": "California",
        "city": "Los Angeles"
    }
}
```

如需時間序列模型執行個體的詳細資訊，請參閱[參考文件](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#instances-api) \(英文\)。

### <a name="time-series-model-settings-example"></a>時間序列模型設定範例

範例：

```JSON
{
    "modelSettings": {
        "name": "DefaultModel",
        "timeSeriesIdProperties": [
            {
                "name": "id",
                "type": "String"
            }
        ],
        "defaultTypeId": "1be09af9-f089-4d6b-9f0b-48018b5f7393"
    }
}
```

如需時間序列模型設定的詳細資訊，請參閱[參考文件](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#model-settings-api) \(英文\)。

## <a name="next-steps"></a>後續步驟

- 請參閱 [Azure 時間序列深入解析預覽版儲存體和輸入](./time-series-insights-update-storage-ingress.md)。

- 查看新的[時間序列模型](https://docs.microsoft.com/rest/api/time-series-insights/preview-model) \(英文\)。