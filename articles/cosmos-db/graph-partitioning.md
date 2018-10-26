---
title: Azure Cosmos DB Gremlin API 中的分割 | Microsoft Docs
description: 了解如何使用 Azure Cosmos DB 中的資料分割圖表。
services: cosmos-db
author: luisbosquez
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-graph
ms.devlang: na
ms.topic: conceptual
ms.date: 02/28/2018
ms.author: lbosq
ms.openlocfilehash: bf91ec77f8d7248c6fa2047636f295c187f626c8
ms.sourcegitcommit: 4b1083fa9c78cd03633f11abb7a69fdbc740afd1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/10/2018
ms.locfileid: "49078217"
---
# <a name="using-a-partitioned-graph-in-azure-cosmos-db"></a>使用 Azure Cosmos DB 中的資料分割圖表

Azure Cosmos DB 中的 Gremlin API 的主要功能之一，是能夠透過水平延展性處理大規模的圖形。 此程序可透過 [Azure Cosmos DB 中的資料分割功能](partition-data.md#how-does-partitioning-work)，利用可在儲存體和輸送量兩方面獨立進行調整的容器來達成。 Azure Cosmos DB 在所有的 API 間支援下列類型的容器：

- **固定容器**：這些容器最多可儲存大小為 10 GB 的圖表資料庫，且最多可為其配置每秒 10,000 個要求單位。 若要建立固定容器，並不需要在資料中指定資料分割索引鍵屬性。

- **無限制容器**：這些容器可以自動調整，以透過水平資料分割儲存超過 10 GB 限制的圖表。 每個資料分割會儲存 10 GB，且資料會根據**指定資料分割索引鍵**自動取得平衡；此索引鍵是使用無限制容器時的必要參數。 此類型的容器可儲存基本上不受限制的資料大小，並且最多可允許每秒 100,000 個要求單位，或[藉由連絡支援人員](https://aka.ms/cosmosdbfeedback?subject=Cosmos%20DB%20More%20Throughput%20Request)而提高此單位。

本文件將詳細說明如何分割圖形資料庫，及其對頂點 (或節點) 和邊緣的影響。

## <a name="requirements-for-partitioned-graph"></a>資料分割圖表的需求

以下將詳細說明建立資料分割圖形容器時的須知：

- 如果預期容器的大小會超過 10 GB，及/或必須配置每秒 10,000 個以上的要求單位 (RU/s)，則**必須設定資料分割**。

- **頂點和邊緣會儲存成 JSON 文件**，並放置在 Azure Cosmos DB Gremlin API 容器的後端中。

- **端點需要資料分割索引鍵**。 此索引鍵會決定頂點將透過雜湊演算法儲存在哪個資料分割中。 此資料分割索引鍵的名稱是不含空格或特殊字元的單字字串，且會在使用 `/partitioning-key-name` 格式在入口網站上建立新容器時定義。

- **邊緣將會連同其來源頂點一起儲存**。 換句話說，對每個頂點而言，其資料分割索引鍵將會定義其本身及其外傳邊緣的儲存位置。 之所以這麼做，是為了避免在圖形查詢中使用 `out()` 基數時出現跨資料分割的查詢。

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

為了確保在無限制容器中使用資料分割圖表時能有最佳效能和延展性，您應遵循下列指導方針：

- **在查詢頂點時務必要指定資料分割索引鍵值**。 從已知的資料分割取得頂點，在效能方面是最有效率的方式。

- **查詢邊緣時應盡可能使用外傳方向**。 如前所述，邊緣會連同其外傳方向的來源頂點一起儲存。 這表示，依此模式為原則設計資料和查詢時，將可盡量降低進行跨資料分割查詢的可能性。

- **選擇可將資料平均分散到每個資料分割的資料分割索引鍵**。 此決策高度仰賴解決方案的資料模型。 請閱讀 [Azure Cosmos DB 中的資料分割和調整](partition-data.md)，以深入了解如何建立適當的資料分割索引鍵。

- **將查詢最佳化以盡可能在資料分割的界限內取得資料**。 最佳的資料分割策略將與查詢模式相對應。 叢單一資料分割取得資料的查詢，將可提供最佳效能。

## <a name="next-steps"></a>後續步驟
本文概略說明了使用 Azure Cosmos DB Gremlin API 進行資料分割的概念和最佳做法。 

* 了解 [Azure Cosmos DB 中的資料分割和調整](partition-data.md)。
* 了解 [Gremlin API 中的 Gremlin 支援](gremlin-support.md)。
* 了解 [Gremlin API 簡介](graph-introduction.md)。