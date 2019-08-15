---
title: 在使用 Azure Cosmos DB 時診斷查詢問題並進行疑難排解
description: 瞭解如何識別、診斷和疑難排解 Azure Cosmos DB SQL 查詢問題。
author: ginamr
ms.service: cosmos-db
ms.topic: troubleshooting
ms.date: 07/10/2019
ms.author: girobins
ms.subservice: cosmosdb-sql
ms.reviewer: sngun
ms.openlocfilehash: a713ed69dc9c35e16b1cc5d9ad9819d53e2e1efe
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/13/2019
ms.locfileid: "68986157"
---
# <a name="troubleshoot-query-performance-for-azure-cosmos-db"></a>針對 Azure Cosmos DB 的查詢效能進行疑難排解
本文涵蓋如何識別、診斷和疑難排解 Azure Cosmos DB SQL 查詢問題。 為了達到 Azure Cosmos DB 查詢的最佳效能, 請遵循下列疑難排解步驟。 

## <a name="collocate-clients-in-same-azure-region"></a>在相同的 Azure 區域中共置用戶端 
確保呼叫端應用程式與佈建的 Azure Cosmos DB 端點位於相同的 Azure 區域中，將可能達到最低的延遲。 如需可用區域的清單, 請參閱[Azure 區域](https://azure.microsoft.com/global-infrastructure/regions/#services)一文。

## <a name="check-consistency-level"></a>檢查一致性層級
[一致性層級](consistency-levels.md)可能會影響效能和費用。 請確定您的一致性層級適用于指定的案例。 如需詳細資訊, 請參閱[選擇一致性層級](consistency-levels-choosing.md)。

## <a name="log-sql-query-in-storage-account"></a>在儲存體帳戶中記錄 Sql 查詢
[透過診斷記錄的 SQL API 查詢記錄](logging.md#turn-on-logging-in-the-azure-portal), 可讓您在您選擇的儲存體帳戶中記錄模糊的查詢。 這可讓您查看診斷記錄, 並使用更多 ru 尋找查詢, 並在 QueryRuntimeStatistics 中使用活動識別碼進行比對。 


## <a name="log-query-metrics"></a>記錄查詢計量
使用`QueryMetrics`來疑難排解緩慢或昂貴的查詢。 

  * `QueryMetrics`在回應中設定`FeedOptions.PopulateQueryMetrics = true`為。
  * `QueryMetrics`類別具有多載`.ToString()`函式, 可叫用以取得的字串`QueryMetrics`表示。 
  * 您可以利用計量來衍生下列深入解析, 還有其他資訊: 
  
      * 查詢管線的任何特定元件是否花費太長的時間才能完成 (以數百毫秒或更多的順序)。 

          * `TotalExecutionTime`查看。
          * `TotalExecutionTime`如果查詢的少於端對端執行時間, 則時間會花在用戶端或網路中。 再次檢查用戶端和 Azure 區域是否已共置。
      
      * 分析的檔中是否有誤報 (如果輸出檔案計數遠低於抓取的檔計數)。  

          * `Index Utilization`查看。
          * `Index Utilization`= (傳回的檔數目/已載入的檔數目)
          * 如果傳回的檔數目遠小於載入的數目, 則會分析誤報。
          * 限制以較窄的篩選器抓取的檔數目。  

      * 個別來回行程的`Partition Execution Timeline`優於方式 (請參閱的字串`QueryMetrics`表示)。 
      * 查詢是否耗用過高的要求費用。 

如需詳細資訊, 請參閱[如何取得 SQL 查詢執行計量](profile-sql-api-query.md)一文。
      
## <a name="tune-query-feed-options-parameters"></a>調整查詢摘要選項的參數 
您可以透過要求的[摘要選項](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.feedoptions?view=azure-dotnet)參數來調整查詢效能。 請嘗試設定下列選項：

  * 先`MaxDegreeOfParallelism`設定為-1, 然後比較不同值之間的效能。 
  * 先`MaxBufferedItemCount`設定為-1, 然後比較不同值之間的效能。 
  * 設定`MaxItemCount`為-1。

比較不同值的效能時，請嘗試 2、4、8、16 等等的值。
 
## <a name="read-all-results-from-continuations"></a>讀取接續作業中的所有結果
如果您不要取得所有結果，請務必完全清空接續作業。 換句話說，當接續 token 有更多文件要產生時，您就應該繼續取得結果。

您可以在下列其中一種模式中實現完全清空：

  * 繼續處理結果, 但接續不是空的。
  * 查詢有更多結果時繼續處理。 

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

## <a name="choose-system-functions-that-utilize-index"></a>選擇利用索引的系統函式
如果運算式可以轉譯成一個範圍內的字串值，就可以利用索引；否則，不能這麼做。 

以下是可利用索引的字串函式清單： 
    
  * STARTSWITH(str_expr, str_expr) 
  * LEFT(str_expr, num_expr) = str_expr 
  * SUBSTRING(str_expr, num_expr, num_expr) = str_expr，但僅限第一個 num_expr 為 0 的時候 
    
    以下是幾個查詢範例: 
    
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

  * 避免索引不提供的篩選準則 (或 WHERE 子句) 中的系統函數。 這類系統函數的一些範例包括 Contains、Upper、Lower。
  * 可能的話，請撰寫查詢以使用分割區索引鍵的篩選條件。
  * 若要達成高效能查詢, 請避免在篩選準則中呼叫 UPPER/LOWER。 相反地, 在插入時將值的大小寫正規化。 針對每個值, 以所需的大小寫插入值, 或以所需的大小寫插入原始值和值。 

    例如:
    
    ```sql

    SELECT * FROM c WHERE UPPER(c.name) = "JOE"

    ```
    
    在此情況下, 請將 "JOE" 大寫, 或儲存 "Joe" 的原始值和 "JOE"。 
    
    如果 JSON 資料大小寫正規化, 查詢就會變成:
    
    ```sql

    SELECT * FROM c WHERE c.name = "JOE"

    ```

    第二個查詢會更具效能, 因為它不需要在每個值上執行轉換, 以便比較值與 "JOE"。

如需更多系統函數詳細資料, 請參閱[系統函數](sql-query-system-functions.md)文章。

## <a name="check-indexing-policy"></a>檢查索引編制原則
若要確認目前的[編制索引原則](index-policy.md)是否是最佳的：

  * 請確定查詢中使用的所有 JSON 路徑都包含在索引編制原則中, 以加快讀取速度。
  * 排除不在查詢中用來進行更高效能寫入的路徑。

如需詳細資訊, 請參閱[如何管理索引編制原則](how-to-manage-indexing-policy.md)一文。

## <a name="spatial-data-check-ordering-of-points"></a>空間資料:檢查點的順序
多邊形內的點必須以逆時針順序指定。 以順時針順序指定的多邊形，代表區域內的反轉。

## <a name="optimize-join-expressions"></a>優化聯結運算式
`JOIN`運算式可以擴充為大型的交叉乘積。 可能的話, 請透過更窄的篩選準則來查詢較小的搜尋空間。

多重值子查詢可以優化`JOIN`運算式, 方法是在每個 select-many 運算式之後推送述詞, 而不是`WHERE`在子句中的所有交叉聯結之後。 如需詳細範例, 請參閱[優化聯結運算式](https://docs.microsoft.com/azure/cosmos-db/sql-query-subquery#optimize-join-expressions)一文。

## <a name="optimize-order-by-expressions"></a>將 ORDER BY 運算式優化 
`ORDER BY`如果欄位為稀疏或未包含在索引原則中, 則查詢效能可能會受到影響。

  * 若為稀疏欄位 (例如時間), 請盡可能減少搜尋空間與篩選準則。 
  * 若為單一`ORDER BY`屬性, 請在索引原則中包含屬性。 
  * 若為多`ORDER BY`個屬性運算式, 請在要排序的欄位上定義[複合索引](https://docs.microsoft.com/azure/cosmos-db/index-policy#composite-indexes)。  

## <a name="many-large-documents-being-loaded-and-processed"></a>載入和處理的許多大型檔
查詢所需的時間和 ru 不只取決於回應的大小, 它們也取決於查詢處理管線所完成的工作。 時間和 ru 會隨著整個查詢處理管線完成的工作量而增加。 針對大型檔執行更多工具, 因此需要更多的時間和 ru 來載入和處理大型檔。

## <a name="low-provisioned-throughput"></a>低布建輸送量
請確定布建的輸送量可以處理工作負載。 增加受影響之集合的 RU 預算。

## <a name="try-upgrading-to-the-latest-sdk-version"></a>嘗試升級至最新的 SDK 版本
若要判斷最新的 SDK, 請參閱[Sdk 下載和版本](sql-api-sdk-dotnet.md)資訊一文。

## <a name="next-steps"></a>後續步驟
請參閱下列檔, 以瞭解如何測量每個查詢的 ru、取得執行統計資料來微調查詢等等:

* [使用 .NET SDK 取得 SQL 查詢執行計量](profile-sql-api-query.md)
* [使用 Azure Cosmos DB 調整查詢效能](sql-api-sql-query-metrics.md)
* [.NET SDK 的效能秘訣](performance-tips.md)
