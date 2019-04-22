---
title: 適用於 Azure Cosmos DB Gremlin API 評估您的查詢，以執行分析函式
description: 了解如何進行疑難排解，並改善您使用 「 執行 」 設定檔步驟的 Gremlin 查詢。
services: cosmos-db
author: luisbosquez
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-graph
ms.topic: conceptual
ms.date: 03/27/2019
ms.author: lbosq
ms.openlocfilehash: 2f3967c64e79b2bc7b01b35eff26f5ac0d4e3db4
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/18/2019
ms.locfileid: "59288602"
---
# <a name="how-to-use-the-execution-profile-step-to-evaluate-your-gremlin-queries"></a>如何評估您的 Gremlin 查詢中使用的執行設定檔的步驟

本文提供如何使用 Azure Cosmos DB Gremlin API 圖表資料庫的執行設定檔步驟的概觀。 此步驟可讓您進行疑難排解的相關資訊，查詢最佳化，以及它適用於 Cosmos DB Gremlin API 帳戶，才能執行任何 Gremlin 查詢。

若要使用此步驟中，只需附加`executionProfile()`函式呼叫結尾的 Gremlin 查詢。 **將執行 Gremlin 查詢**和作業的結果會傳回查詢執行設定檔的 JSON 回應物件。

例如︰

```java
    // Basic traversal
    g.V('mary').out()

    // Basic traversal with execution profile call
    g.V('mary').out().executionProfile()
```

之後呼叫`executionProfile()`步驟中，回應會成為 JSON 物件，其中包含執行的 Gremlin 步驟、 總時間，以及陳述式所導致的 Cosmos DB 執行階段運算子的陣列。

> [!NOTE]
> Apache Tinkerpop 規格中未定義此實作執行設定檔。 它是 Azure Cosmos DB Gremlin API 實作特有的。


## <a name="response-example"></a>回應範例

註解的範例將傳回的輸出如下：

> [!NOTE]
> 此範例將說明回應的一般結構的註解附註。 實際 executionProfile 回應不包含任何註解。

```json
[
  {
    // The Gremlin statement that was executed.
    "gremlin": "g.V('mary').out().executionProfile()",

    // Amount of time in milliseconds that the entire operation took.
    "totalTime": 28,

    // An array containing metrics for each of the steps that were executed. Each Gremlin step will translate to one or more of these steps.
    // This list is sorted in order of execution.
    "metrics": [
      {
        // This operation obtains a set of Vertex objects.
        // The metrics include: time, percentTime of total execution time, resultCount, fanoutFactor, count, size (in bytes) and time.
        "name": "GetVertices",
        "time": 24,
        "annotations": {
          "percentTime": 85.71
        },
        "counts": {
          "resultCount": 2
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 2,
            "size": 696,
            "time": 0.4
          }
        ]
      },
      {
        // This operation obtains a set of Edge objects. Depending on the query, these might be directly adjacent to a set of vertices, or separate, in the case of an E() query.
        // The metrics include: time, percentTime of total execution time, resultCount, fanoutFactor, count, size (in bytes) and time.
        "name": "GetEdges",
        "time": 4,
        "annotations": {
          "percentTime": 14.29
        },
        "counts": {
          "resultCount": 1
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 1,
            "size": 419,
            "time": 0.67
          }
        ]
      },
      {
        // This operation obtains the vertices that a set of edges point at.
        // The metrics include: time, percentTime of total execution time and resultCount.
        "name": "GetNeighborVertices",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 1
        }
      },
      {
        // This operation represents the serialization and preparation for a result from the preceding graph operations.
        // The metrics include: time, percentTime of total execution time and resultCount.
        "name": "ProjectOperator",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 1
        }
      }
    ]
  }
]
```

> [!NOTE]
> ExecutionProfile 步驟會執行 Gremlin 查詢。 這包括`addV`或`addE`步驟，因為這會導致建立，並會認可在查詢中指定的變更。 如此一來，Gremlin 查詢所產生的要求單位也會收取任何費用。

## <a name="execution-profile-response-objects"></a>執行設定檔回應物件

ExecutionProfile() 函式的回應將會產生具有下列結構的 JSON 物件的階層：
  - **Gremlin 作業物件**:代表整個執行的 Gremlin 作業。 包含下列屬性。
    - `gremlin`:明確 Gremlin 陳述式執行。
    - `totalTime`:時間 （毫秒），在產生的執行步驟。 
    - `metrics`:陣列，其中包含每個 Cosmos DB 執行階段運算子可完成查詢所執行。 這份清單是以執行順序排序。
    
  - **Cosmos DB 執行階段運算子**:代表每個元件的整個 Gremlin 作業。 這份清單是以執行順序排序。 每個物件包含下列屬性：
    - `name`:操作員名稱。 這是已評估，並執行步驟的類型。 下表中閱讀更多。
    - `time`:以毫秒為單位，指定的運算子所花費的時間量。
    - `annotations`:包含其他資訊，特定運算子所執行。
    - `annotations.percentTime`:執行特定運算子所花費的總時間百分比。
    - `counts`:這個運算子所傳回的儲存層的物件數目。 這個包含`counts.resultCount`內的純量值。
    - `storeOps`:表示可以跨越一或多個資料分割的儲存體作業。
    - `storeOps.fanoutFactor`:代表這個特定的儲存體作業存取的資料分割數目。
    - `storeOps.count`:表示此儲存體作業傳回的結果數目。
    - `storeOps.size`:表示的大小，以位元組為單位的指定儲存體作業的結果。

Cosmos DB Gremlin 執行階段運算子|描述
---|---
`GetVertices`| 此步驟中取得一組 predicated 的物件從持續性層級。 
`GetEdges`| 此步驟中取得一組頂點的相鄰的邊緣。 此步驟可能會導致一個或多個儲存體作業。
`GetNeighborVertices`| 此步驟中取得連線到邊緣的一組的頂點。 邊緣則包含資料分割索引鍵和識別碼的其來源和目標的頂點。
`Coalesce`| 此步驟會負責評估的兩項作業只要`coalesce()`執行 Gremlin 步驟。
`CartesianProductOperator`| 此步驟會計算兩個資料集之間笛卡兒乘積。 通常執行每當述詞`to()`或`from()`習慣。
`ConstantSourceOperator`| 此步驟中計算運算式，以產生常數值的結果。
`ProjectOperator`| 此步驟會準備並序列化回應，使用上述作業的結果。
`ProjectAggregation`| 此步驟會準備並序列化彙總作業的回應。

> [!NOTE]
> 這份清單將會繼續加入新的運算子時進行更新。

## <a name="examples-on-how-to-analyze-an-execution-profile-response"></a>有關如何分析執行設定檔回應範例

以下是常見的最佳化作業，可以使用執行設定檔回應中發現的範例：
  - 眼盲的展開傳送查詢。
  - 未篩選的查詢。

### <a name="blind-fan-out-query-patterns"></a>眼盲的展開傳送查詢模式

假設下列執行設定檔回應，從**資料分割的圖表**:

```json
[
  {
    "gremlin": "g.V('tt0093640').executionProfile()",
    "totalTime": 46,
    "metrics": [
      {
        "name": "GetVertices",
        "time": 46,
        "annotations": {
          "percentTime": 100
        },
        "counts": {
          "resultCount": 1
        },
        "storeOps": [
          {
            "fanoutFactor": 5,
            "count": 1,
            "size": 589,
            "time": 75.61
          }
        ]
      },
      {
        "name": "ProjectOperator",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 1
        }
      }
    ]
  }
]
```

您可以從中進行下列結論：
- 查詢是單一的識別碼查閱，因為 Gremlin 陳述式會遵循模式`g.V('id')`。
- 從`time`計量，此查詢的延遲很高，因為它是看起來[多個單一點讀取作業的 10 毫秒](https://docs.microsoft.com/azure/cosmos-db/introduction#guaranteed-low-latency-at-99th-percentile-worldwide)。
- 如果我們將探討`storeOps`物件，我們可以看到`fanoutFactor`是`5`，這表示[5 份資料分割](https://docs.microsoft.com/azure/cosmos-db/partition-data)所存取的這項作業。

這項分析結束時，我們可以決定第一個查詢會存取超過所需的更多分割區。 可以藉由指定分割索引鍵做為述詞查詢中處理此問題。 這會導致較少的延遲，並小於每個查詢的成本。 深入了解[圖表分割](graph-partitioning.md)。 更佳的查詢就會`g.V('tt0093640').has('partitionKey', 't1001')`。

### <a name="unfiltered-query-patterns"></a>未篩選的查詢模式

比較下列兩個執行設定檔回應。 為了簡單起見，這些範例會使用單一資料分割的圖表。

此第一個查詢會擷取所有具有標籤的頂點`tweet`，然後取得其相鄰的頂點：

```json
[
  {
    "gremlin": "g.V().hasLabel('tweet').out().executionProfile()",
    "totalTime": 42,
    "metrics": [
      {
        "name": "GetVertices",
        "time": 31,
        "annotations": {
          "percentTime": 73.81
        },
        "counts": {
          "resultCount": 30
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 13,
            "size": 6819,
            "time": 1.02
          }
        ]
      },
      {
        "name": "GetEdges",
        "time": 6,
        "annotations": {
          "percentTime": 14.29
        },
        "counts": {
          "resultCount": 18
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 20,
            "size": 7950,
            "time": 1.98
          }
        ]
      },
      {
        "name": "GetNeighborVertices",
        "time": 5,
        "annotations": {
          "percentTime": 11.9
        },
        "counts": {
          "resultCount": 20
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 4,
            "size": 1070,
            "time": 1.19
          }
        ]
      },
      {
        "name": "ProjectOperator",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 20
        }
      }
    ]
  }
]
```

請注意設定檔相同的查詢，但現在有額外的篩選， `has('lang', 'en')`，之後再探索相鄰的頂點：

```json
[
  {
    "gremlin": "g.V().hasLabel('tweet').has('lang', 'en').out().executionProfile()",
    "totalTime": 14,
    "metrics": [
      {
        "name": "GetVertices",
        "time": 14,
        "annotations": {
          "percentTime": 58.33
        },
        "counts": {
          "resultCount": 11
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 11,
            "size": 4807,
            "time": 1.27
          }
        ]
      },
      {
        "name": "GetEdges",
        "time": 5,
        "annotations": {
          "percentTime": 20.83
        },
        "counts": {
          "resultCount": 18
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 18,
            "size": 7159,
            "time": 1.7
          }
        ]
      },
      {
        "name": "GetNeighborVertices",
        "time": 5,
        "annotations": {
          "percentTime": 20.83
        },
        "counts": {
          "resultCount": 18
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 4,
            "size": 1070,
            "time": 1.01
          }
        ]
      },
      {
        "name": "ProjectOperator",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 18
        }
      }
    ]
  }
]
```

這兩個查詢達到相同的結果，不過，第一個需要更多的要求單位因為它需要逐一查看查詢的相鄰的項目之前的較大的初始資料集。 比較這兩種回應的下列參數時，我們可以看到此行為的指標：
- `metrics[0].time`值較高的第一個回應，這表示，此單一步驟所花費的時間來解決。
- `metrics[0].counts.resultsCount`值較高也在第一次的回應中，表示初始的 「 工作 」 資料集較大。

## <a name="next-steps"></a>後續步驟
* 深入了解[支援的 Gremlin 功能](gremlin-support.md)Azure Cosmos DB 中。 
* 深入了解[在 Azure Cosmos DB 的 Gremlin API](graph-introduction.md)。
