---
title: 診斷和排解查詢問題，當使用 Azure Cosmos DB
description: 了解如何識別、 診斷和疑難排解 Azure Cosmos DB SQL 查詢。
author: ginamr
ms.service: cosmos-db
ms.topic: troubleshooting
ms.date: 07/10/2019
ms.author: girobins
ms.subservice: cosmosdb-sql
ms.reviewer: sngun
ms.openlocfilehash: 079e8677febfe6683d4f0e60a0e7ba6b06ea549d
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2019
ms.locfileid: "67835826"
---
# <a name="troubleshoot-query-performance-for-azure-cosmos-db"></a>適用於 Azure Cosmos DB 的查詢效能進行疑難排解
這篇文章說明如何識別、 診斷和疑難排解 Azure Cosmos DB SQL 查詢。 為了達到最佳效能，Azure Cosmos DB 查詢，請遵循下列疑難排解步驟。 

## <a name="collocate-clients-in-same-azure-region"></a>共置在相同的 Azure 區域中的用戶端 
確保呼叫端應用程式與佈建的 Azure Cosmos DB 端點位於相同的 Azure 區域中，將可能達到最低的延遲。 如需可用區域的清單，請參閱 < [Azure 區域](https://azure.microsoft.com/global-infrastructure/regions/#services)文章。

## <a name="check-consistency-level"></a>檢查一致性層級
[一致性層級](consistency-levels.md)可能影響效能和費用。 請確定您的一致性層級是適用於特定案例。 如需詳細資訊，請參閱[選擇的一致性層級](consistency-levels-choosing.md)。

## <a name="log-query-metrics"></a>記錄查詢度量
使用`QueryMetrics`到緩慢或昂貴的查詢進行疑難排解。 

  * 設定`FeedOptions.PopulateQueryMetrics = true`有`QueryMetrics`在回應中。
  * `QueryMetrics` 類別具有多載`.ToString()`函式，可以叫用它來取得的字串表示`QueryMetrics`。 
  * 衍生的下列深入解析，以及其他您可以利用計量： 
  
      * 是否有任何特定的元件，查詢管線的花了異常長的時間 （依序完成的毫秒數或更多的數百個）。 

          * 看看`TotalExecutionTime`。
          * 如果`TotalExecutionTime`的查詢是否小於一個端對端的執行時間，然後在用戶端或網路中花費的時間。 再次檢查用戶端和 Azure 區域的共置。
      
      * 文件中是否發生誤判分析 （如果輸出文件計數會是比較不會比擷取文件計數）。  

          * 看看`Index Utilization`。
          * `Index Utilization` = (傳回文件數 / 載入的文件)
          * 如果傳回的文件數目，遠少於載入數字會被分析誤判。
          * 限制較窄的篩選與所擷取的文件數目。  

      * 效能如何個別的反覆存取 (請參閱`Partition Execution Timeline`的字串表示法從`QueryMetrics`)。 
      * 是否查詢取用高的要求費用。 

如需詳細資訊，請參閱[如何取得 SQL 查詢執行計量](profile-sql-api-query.md)文章。
      
## <a name="tune-query-feed-options-parameters"></a>微調查詢摘要的選項參數 
可以透過要求的微調查詢效能[摘要選項](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.feedoptions?view=azure-dotnet)參數。 請嘗試設定下列選項：

  * 設定`MaxDegreeOfParallelism`以-1，然後比較效能，跨不同的值。 
  * 設定`MaxBufferedItemCount`以-1，然後比較效能，跨不同的值。 
  * 設定`MaxItemCount`為-1。

比較不同的值的效能，請重試值，例如 2、 4、 8、 16 和其他項目。
 
## <a name="read-all-results-from-continuations"></a>從接續讀取所有結果
如果您認為您不想要取得所有結果，請務必完全清空接續。 換句話說，保留讀取結果，接續 token 而產生更多文件。

可以達成完全清空，使用下列模式：

  * 繼續處理結果，而不是空的接續。
  * 繼續處理而查詢的結果。 

    ```csharp
    // using AsDocumentQuery you get access to whether or not the query HasMoreResults
    // If it does, just call ExecuteNextAsync until there are no more results
    // No need to supply a continuation token here as the server keeps track of progress
    var query = client.CreateDocumentQuery<Family>(collectionLink, options).AsDocumentQuery();
    while (query.HasMoreResults)
    {
        foreach (Family family in await query.ExecuteNextAsync())
        {
            families.Add(family);
        }
    }
    ```

## <a name="choose-system-functions-that-utilize-index"></a>選擇使用索引的系統函式
如果運算式可以轉譯成字串值的範圍，然後它可以利用索引;否則，它不能。 

以下是可以利用索引的字串函數的清單： 
    
  * STARTSWITH(str_expr, str_expr) 
  * LEFT(str_expr, num_expr) = str_expr 
  * SUBSTRING(str_expr, num_expr, num_expr) = str_expr, but only if first num_expr is 0 
    
    以下是幾個查詢範例： 
    
    ```sql

    -- If there is a range index on r.name, STARTSWITH will utilize the index while ENDSWITH won't 
    SELECT * 
    FROM c 
    WHERE STARTSWITH(c.name, 'J') AND ENDSWITH(c.name, 'n')

    ```
    
    ```sql

    -- LEFT will utilize the index while RIGHT won't 
    SELECT * 
    FROM c 
    WHERE LEFT(c.name, 2) = 'Jo' AND RIGHT(c.name, 2) = 'hn'

    ```

  * 避免在篩選中的系統函式 （或 WHERE 子句），不會由索引。 這類系統函數的範例包括 Contains、 Upper、 Lower。
  * 可能的話，請撰寫查詢以使用分割區索引鍵的篩選條件。
  * 若要達到高效能查詢會避免呼叫大寫/小寫篩選器中。 相反地，正規化在插入值的大小寫。 每個值插入的值與所需的大小寫，或插入的原始值和所需的大小寫的值。 

    例如:
    
    ```sql

    SELECT * FROM c WHERE UPPER(c.name) = "JOE"

    ```
    
    在此情況下，存放區"JOE"大寫，或儲存的原始值"Joe"和"JOE"。 
    
    如果標準化的 JSON 資料的大小寫，則查詢將會成為：
    
    ```sql

    SELECT * FROM c WHERE c.name = "JOE"

    ```

    第二項查詢將會提高效能，因為它不需要在每個值上執行轉換，以比較值，以"JOE"。

更多的系統函式的詳細資訊請參閱[系統函數](sql-query-system-functions.md)文章。

## <a name="check-indexing-policy"></a>檢查編製索引原則
若要確認目前[編製索引原則](index-policy.md)是最佳作法：

  * 確定編製索引原則會包含在查詢中使用的所有 JSON 路徑的更快速的讀取。
  * 排除不用於查詢的更多高效能寫入的路徑。

如需詳細資訊，請參閱[方式來管理編製索引原則](how-to-manage-indexing-policy.md)文章。

## <a name="spatial-data-check-ordering-of-points"></a>空間資料：檢查點的順序
多邊形內的點必須以逆時針順序指定。 以順時針順序指定的多邊形，代表區域內的反轉。

## <a name="optimize-join-expressions"></a>最佳化聯結運算式
`JOIN` 運算式可以擴充到大型 (cross product)。 當可行，針對較小的搜尋查詢空間透過較窄的篩選條件。

多重值的子查詢可以最佳化`JOIN`藉由推送述詞，每個選取多運算式之後，而不在中的所有跨聯結後的運算式`WHERE`子句。 如需詳細的範例，請參閱[最佳化聯結運算式](https://docs.microsoft.com/azure/cosmos-db/sql-query-subquery#optimize-join-expressions)文章。

## <a name="optimize-order-by-expressions"></a>最佳化 ORDER BY 運算式 
`ORDER BY` 如果欄位是疏鬆或不包含在索引原則，可能會降低查詢效能。

  * 對於疏鬆時間等欄位，減少搜尋空間盡量與篩選器。 
  * 單一屬性`ORDER BY`，包含在索引原則中的屬性。 
  * 多個屬性`ORDER BY`運算式，會定義[複合式索引](https://docs.microsoft.com/azure/cosmos-db/index-policy#composite-indexes)正在排序的欄位。  

## <a name="many-large-documents-being-loaded-and-processed"></a>許多大型文件正在載入並處理
時間和查詢所需的 Ru 不只回應的大小而定，它們也是取決於查詢處理管線由工作。 時間和 Ru 按比例增加少量的工作由整個查詢處理管線。 對於大型文件執行更多工作，因此更多的時間和 Ru 才能載入和處理大型文件。

## <a name="low-provisioned-throughput"></a>低佈建的輸送量
請確定已佈建的輸送量可以處理工作負載。 增加 RU 預算，針對受影響的集合。

## <a name="try-upgrading-to-the-latest-sdk-version"></a>請嘗試升級為最新的 SDK 版本
若要判斷最新 SDK，請參閱[SDK 下載和版本資訊](sql-api-sdk-dotnet.md)文章。

## <a name="next-steps"></a>後續步驟
請參閱以下有關如何測量每個查詢的 Ru，取得執行統計資料來微調您的查詢，以及更多的文件：

* [取得使用.NET SDK 的 SQL 查詢執行計量](profile-sql-api-query.md)
* [使用 Azure Cosmos DB 調整查詢效能](sql-api-sql-query-metrics.md)
* [適用於.NET SDK 的效能秘訣](performance-tips.md)