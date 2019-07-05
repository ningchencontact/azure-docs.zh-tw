---
title: Azure Cosmos DB Gremlin API 中的資料分割
description: 了解如何使用 Azure Cosmos DB 中的資料分割圖表。 本文也說明資料分割圖表的需求和最佳做法。
author: luisbosquez
ms.author: lbosq
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: conceptual
ms.date: 06/24/2019
ms.custom: seodec18
ms.openlocfilehash: 4c8761d82c8a735ac9c4bff2e5ac0107b2a57fe0
ms.sourcegitcommit: 084630bb22ae4cf037794923a1ef602d84831c57
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/03/2019
ms.locfileid: "67537537"
---
# <a name="using-a-partitioned-graph-in-azure-cosmos-db"></a>使用 Azure Cosmos DB 中的資料分割圖表

Azure Cosmos DB 中的 Gremlin API 的主要功能之一，是能夠透過水平縮放處理大規模的圖形。 容器可以在儲存體和輸送量方面獨立調整。 您可以在 Azure Cosmos DB 中，建立能自動縮放來儲存圖表資料的容器。 資料根據指定的**分割區金鑰**來自動取得平衡。

如果預計容器大小要儲存超過 10 GB ，或如果想要每秒 (Ru) 配置超過 10,000 個要求單位，則**必須進行資料分割**。 從相同的一般原則[分割機制的 Azure Cosmos DB](partition-data.md)套用幾個圖形特定的最佳化，如下所述。

![圖形資料分割。](./media/graph-partitioning/graph-partitioning.png)

## <a name="graph-partitioning-mechanism"></a>資料分割機制的圖形

下列指導方針說明 Azure Cosmos DB 中的資料分割策略的運作方式：

- **頂點和邊緣會儲存為 JSON 文件**。

- **端點需要資料分割索引鍵**。 此索引鍵會決定頂點將透過雜湊演算法儲存在哪個資料分割中。 建立新的容器時定義分割區索引鍵屬性名稱，而且它具有格式： `/partitioning-key-name`。

- **邊緣將會連同其來源頂點一起儲存**。 換句話說，對每個頂點而言，其分割區金鑰將會定義其本身及其外傳邊緣的儲存位置。 此最佳化為了避免跨分割區查詢時使用`out()`圖形查詢中的基數。

- **邊緣包含參考其指向的頂點**。 使用資料分割索引鍵和識別碼所指向的頂點，會儲存所有邊緣。 這項計算可讓所有`out()`方向查詢一律會為已設定領域的資料分割的查詢，並不是匿名的跨分割區查詢。 

- **圖形查詢必須指定資料分割索引鍵**。 若要在 Azure Cosmos DB 中充分利用水平資料分割，則應在選取單一端點時盡可能指定資料分割索引鍵。 以下是在資料分割的圖表中選取一或多個頂點的查詢：

    - 不支援 Gremlin API 中的容器以 `/id` 和 `/label` 作為資料分割索引鍵。


    - 依識別碼選取頂點，然後**使用 `.has()` 步驟指定資料分割索引鍵屬性**： 
    
        ```java
        g.V('vertex_id').has('partitionKey', 'partitionKey_value')
        ```
    
    - 藉由**指定包含資料分割索引鍵值和識別碼的 Tuple** 來選取頂點： 
    
        ```java
        g.V(['partitionKey_value', 'vertex_id'])
        ```
        
    - 指定**資料分割索引鍵值和識別碼的 Tuple 陣列**：
    
        ```java
        g.V(['partitionKey_value0', 'verted_id0'], ['partitionKey_value1', 'vertex_id1'], ...)
        ```
        
    - 選取一組具有其識別碼的頂點和**指定一份資料分割索引鍵值**: 
    
        ```java
        g.V('vertex_id0', 'vertex_id1', 'vertex_id2', …).has('partitionKey', within('partitionKey_value0', 'partitionKey_value01', 'partitionKey_value02', …)
        ```

    - 使用**資料分割策略**在查詢開頭，並指定範圍的 Gremlin 查詢的其餘部分的資料分割： 
    
        ```java
        g.withStrategies(PartitionStrategy.build().partitionKey('partitionKey').readPartitions('partitionKey_value').create()).V()
        ```

## <a name="best-practices-when-using-a-partitioned-graph"></a>使用資料分割圖表時的最佳做法

使用無限制容器進行資料分割圖表時，您可以使用下列指導方針來確保效能和延展性：

- **在查詢頂點時務必要指定資料分割索引鍵值**。 從已知資料分割取得頂點，是可達到效能的方法。 因為邊緣包含至其目標頂點參考識別碼和資料分割索引鍵，所有後續的相鄰作業一定範圍為分割區。

- **查詢邊緣時應盡可能使用外傳方向**。 如前所述，邊緣會連同其外傳方向的來源頂點一起儲存。 依此模式為原則設計資料和查詢時，將可盡量降低進行跨資料分割查詢的可能性。 相反地，`in()`查詢一定會耗費資源的展開傳送查詢。

- **選擇可將資料平均分散到每個資料分割的資料分割索引鍵**。 此決策高度仰賴解決方案的資料模型。 請閱讀 [Azure Cosmos DB 中的資料分割和調整](partition-data.md)，以深入了解如何建立適當的資料分割索引鍵。

- **將查詢最佳化以在資料分割的界限內取得資料**。 最佳的資料分割策略將與查詢模式相對應。 叢單一資料分割取得資料的查詢，將可提供最佳效能。

## <a name="next-steps"></a>後續步驟

接下來，您可以繼續閱讀下列文章：

* 了解 [Azure Cosmos DB 中的資料分割和調整](partition-data.md)。
* 了解 [Gremlin API 中的 Gremlin 支援](gremlin-support.md)。
* 了解 [Gremlin API 簡介](graph-introduction.md)。
