---
title: Azure Cosmos DB Gremlin API 中的資料分割
description: 了解如何使用 Azure Cosmos DB 中的資料分割圖表。 本文也說明資料分割圖表的需求和最佳做法。
author: luisbosquez
ms.author: lbosq
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: conceptual
ms.date: 12/06/2018
ms.custom: seodec18
ms.openlocfilehash: f1e486a302b440d819e15ef86f8d76ea5e50d201
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "60888399"
---
# <a name="using-a-partitioned-graph-in-azure-cosmos-db"></a>使用 Azure Cosmos DB 中的資料分割圖表

Azure Cosmos DB 中的 Gremlin API 的主要功能之一，是能夠透過水平縮放處理大規模的圖形。 水平縮放透過 [Azure Cosmos DB 中的資料分割功能](partition-data.md)來達成。 容器可以在儲存體和輸送量方面獨立調整。 您可以在 Azure Cosmos DB 中，建立能自動縮放來儲存圖表資料的容器。 資料根據指定的**分割區金鑰**來自動取得平衡。

本文件將詳細說明如何分割圖形資料庫，及其對頂點 (或節點) 和邊緣的影響。

## <a name="requirements-for-partitioned-graph"></a>資料分割圖表的需求

以下將詳細說明建立資料分割圖形容器時的須知：

- 如果預計容器大小要儲存超過 10 GB ，或如果想要每秒 (Ru) 配置超過 10,000 個要求單位，則**必須進行資料分割**。

- **頂點和邊緣會儲存為 JSON 文件**。

- **端點需要資料分割索引鍵**。 此索引鍵會決定頂點將透過雜湊演算法儲存在哪個資料分割中。 此分割區金鑰名稱，是不含空格或特殊字元的單一字詞字串。 建立新容器時，定義分割區金鑰，而且其格式為：`/partitioning-key-name`。

- **邊緣將會連同其來源頂點一起儲存**。 換句話說，對每個頂點而言，其分割區金鑰將會定義其本身及其外傳邊緣的儲存位置。 之所以這麼做，是為了避免在圖形查詢中使用 `out()` 基數時出現跨資料分割的查詢。

- **圖形查詢必須指定資料分割索引鍵**。 若要在 Azure Cosmos DB 中充分利用水平資料分割，則應在選取單一端點時盡可能指定資料分割索引鍵。 以下是在資料分割的圖表中選取一或多個頂點的查詢：

    - 不支援 Gremlin API 中的容器以 `/id` 和 `/label` 作為資料分割索引鍵。


    - 依識別碼選取頂點，然後**使用 `.has()` 步驟指定資料分割索引鍵屬性**： 
    
        ```
        g.V('vertex_id').has('partitionKey', 'partitionKey_value')
        ```
    
    - 藉由**指定包含資料分割索引鍵值和識別碼的 Tuple** 來選取頂點： 
    
        ```
        g.V(['partitionKey_value', 'vertex_id'])
        ```
        
    - 指定**資料分割索引鍵值和識別碼的 Tuple 陣列**：
    
        ```
        g.V(['partitionKey_value0', 'verted_id0'], ['partitionKey_value1', 'vertex_id1'], ...)
        ```
        
    - 選取一組頂點，並**指定資料分割索引鍵值清單**： 
    
        ```
        g.V('vertex_id0', 'vertex_id1', 'vertex_id2', …).has('partitionKey', within('partitionKey_value0', 'partitionKey_value01', 'partitionKey_value02', …)
        ```

## <a name="best-practices-when-using-a-partitioned-graph"></a>使用資料分割圖表時的最佳做法

使用無限制容器進行資料分割圖表時，您可以使用下列指導方針來確保效能和延展性：

- **在查詢頂點時務必要指定資料分割索引鍵值**。 從已知資料分割取得頂點，是可達到效能的方法。

- **查詢邊緣時應盡可能使用外傳方向**。 如前所述，邊緣會連同其外傳方向的來源頂點一起儲存。 依此模式為原則設計資料和查詢時，將可盡量降低進行跨資料分割查詢的可能性。

- **選擇可將資料平均分散到每個資料分割的資料分割索引鍵**。 此決策高度仰賴解決方案的資料模型。 請閱讀 [Azure Cosmos DB 中的資料分割和調整](partition-data.md)，以深入了解如何建立適當的資料分割索引鍵。

- **將查詢最佳化以在資料分割的界限內取得資料**。 最佳的資料分割策略將與查詢模式相對應。 叢單一資料分割取得資料的查詢，將可提供最佳效能。

## <a name="next-steps"></a>後續步驟

接下來，您可以繼續閱讀下列文章：

* 了解 [Azure Cosmos DB 中的資料分割和調整](partition-data.md)。
* 了解 [Gremlin API 中的 Gremlin 支援](gremlin-support.md)。
* 了解 [Gremlin API 簡介](graph-introduction.md)。
