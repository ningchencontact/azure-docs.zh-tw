---
title: Azure Cosmos DB Gremlin 的限制
description: 圖形引擎執行時間限制的參考檔
author: olignat
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: reference
ms.date: 09/10/2019
ms.author: olignat
ms.openlocfilehash: f1fe4cfac22a651f44338986d0a767fe3e1a360b
ms.sourcegitcommit: d70c74e11fa95f70077620b4613bb35d9bf78484
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/11/2019
ms.locfileid: "70910906"
---
# <a name="azure-cosmos-db-gremlin-limits"></a>Azure Cosmos DB Gremlin 限制
本文討論 Azure Cosmos DB Gremlin 引擎的限制，並說明它們可能對客戶周遊有何影響。

Cosmos DB Gremlin 建置於 Cosmos DB 基礎結構之上，這就是為什麼[Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/concepts-limits)的所有限制仍然適用的原因。 

## <a name="limits"></a>限制

達到 Gremlin 限制時，會以**x-ms-status-錯誤碼**= 429 來取消遍歷，表示節流錯誤。

**Resource**    | **預設限制** | **說明**
--- | --- | ---
*每個要求的記憶體* | **2 GB** | 要求可能會在處理期間耗用最大量的記憶體。 需要計算大型資料集的要求將會耗用額外的記憶體。 請考慮將要求範圍限定于較小的資料集，以避免超出此限制或使用 OLAP 方案。
*腳本長度* | **64 KB** | 每個要求的 Gremlin 周遊指令碼長度上限。
*運算子深度* | **400** |  周遊中的特有步驟總數。 例如， ```g.V().out()```具有運算子計數2：V （）和 out （）， ```g.V('label').repeat(out()).times(100)```運算子深度為3：V （）、repeat （）和 out （），因為```.times(100)```是```.repeat()```運算子的參數。
*平行處理原則的程度* | **32** | 在對儲存層發出的單一要求中，可查詢的儲存體分割區數目上限。 具有數百個數據分割的圖形會受到此限制的影響。
*重複限制* | **32** | ```.repeat()``` 運算子可執行的反覆項目數上限。 在大部分情況```.repeat()```下，步驟的每個反復專案都會執行廣度優先的回合，這表示任何一項迴圈最多隻能有32個頂點之間的躍點。
*遍歷超時* | **30 秒** | 當遍歷超過此時間時，將會將其取消。 Cosmos DB Graph 是在毫秒內完成絕大多數周遊的 OLTP 資料庫。 若要在 Cosmos DB 圖形上執行 OLAP 查詢，請使用[圖表資料框架](https://spark.apache.org/docs/latest/sql-programming-guide.html#datasets-and-dataframes)和[Cosmos DB Spark 連接器](https://github.com/Azure/azure-cosmosdb-spark)的[Apache Spark](https://azure.microsoft.com/services/cosmos-db/) 。
*述詞限制* | **20** | ```.has()``` 或 ```.hasNot()``` 步驟會套用在單一頂點或邊緣上。 達到這項限制時，應用程式上會出現的錯誤是 ```The SQL query exceeded the maximum number of joins. The allowed limit is 20```。 因為小組正在努力提升此限制，所以會造成暫時性的不便。 
*閒置連接逾時* | **5小時** | Graph 伺服器會保持 websocket 連線開啟，而不會有流量的時間量。 TCP keep-alive 封包或 HTTP keep-alive 要求不會延伸超過此限制的連線存留期，不過，如果未傳送，基礎 Azure 基礎結構可能會更快關閉連線。 Cosmos DB Graph 引擎在其上沒有執行 Gremlin 周遊時，會視為閒置。
*每小時的資源權杖* | **100** | Gremlin 用戶端用來連線到區域中 Gremlin 帳戶的唯一資源權杖數目。 當應用程式超過每小時的唯一`"Exceeded allowed resource token limit of 100 that can be used concurrently"`權杖限制時，將會在下一次驗證要求時傳回。

## <a name="next-steps"></a>後續步驟
* [Azure Cosmos DB Gremlin 回應標頭](gremlin-headers.md) 
* [使用 Gremlin Azure Cosmos DB 資源權杖](how-to-use-resource-tokens-gremlin.md)
