---
title: 如何監視索引子狀態和結果-Azure 搜尋服務
description: 監視狀態、 進度與結果的使用 REST API 或.NET SDK 在 Azure 入口網站中的 Azure 搜尋服務索引子。
ms.date: 06/28/2019
author: RobDixon22
manager: HeidiSteen
ms.author: v-rodixo
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: 07b4fe2ef830c3ce09b655cf4b433d14923229a9
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/01/2019
ms.locfileid: "67486281"
---
# <a name="how-to-monitor-azure-search-indexer-status-and-results"></a>如何監視 Azure 搜尋服務索引子狀態和結果

Azure 搜尋服務會提供狀態，以及監視的每個索引子的目前和過去執行的相關資訊。

當您想要則索引子監視很有用：

* 追蹤的持續期間的索引子進度執行。
* 檢閱進行中或前一個索引子執行的結果。
* 找出最上層的索引子錯誤和錯誤或警告有關個別文件編製索引。

## <a name="find-indexer-status-and-history-details"></a>尋找索引子狀態和歷程記錄的詳細資料

您可以存取索引子的監視資訊以各種方式，包括：

* 在 [Azure 入口網站](#portal)中
* 使用[REST API](#restapi)
* 使用[.NET SDK](#dotnetsdk)

（雖然格式的不同點的資料會根據所使用的存取方法），可監視資訊的索引子會包含下列所有：

* 索引子本身的狀態資訊
* 一次索引子，包括其狀態、 開始和結束時間，以及詳細的錯誤和警告，執行最新的相關資訊。
* 歷程記錄的索引子執行時，其狀態、 結果、 錯誤和警告清單。

處理大型資料磁碟區的索引子可能需要很長的時間來執行。 比方說，處理數以百萬計的來源文件的索引子可以執行 24 小時，並且幾乎會立即重新啟動。 針對大量索引子狀態可能會經常說**進行中**入口網站中。 即使執行索引子，就有一個詳細資料可進行中的進度和先前的執行相關。

<a name="portal"></a>

## <a name="monitor-indexers-in-the-portal"></a>在入口網站中監視索引子

您可以看到所有的程式中的索引子的目前狀態**索引子**您搜尋服務概觀 頁面上的清單。

   ![索引子清單](media/search-monitor-indexers/indexers-list.png "索引子清單")

索引子執行的時，在清單中顯示的狀態**In Progress**，而**Docs 成功**值會顯示目前處理的文件數目。 可能需要幾分鐘的時間來更新索引子狀態的值為入口網站和文件計數。

其最新的執行已成功示範索引子**成功**。 執行索引子可順利即使個別文件有錯誤，錯誤的數目是否小於一個索引子**失敗項目數上限**設定。

如果最近執行已結束並發生錯誤時，狀態會顯示**失敗**。 狀態**重設**表示已重設索引子的變更追蹤狀態。

按一下清單，以查看更多詳細的索引子的目前和最新的索引子執行。

   ![索引子摘要] 和 [執行歷程記錄](media/search-monitor-indexers/indexer-summary.png "索引子摘要] 和 [執行歷程記錄")

**索引子摘要**圖表會顯示在其最新的執行中處理的文件數目的圖表。

**的執行詳細資料**清單會顯示多達 50 個最近的執行結果。

按一下以查看特定執行的資訊清單中的執行結果。 這包括其開始和結束時間，以及任何錯誤以及所發生的警告。

   ![索引子執行詳細資料](media/search-monitor-indexers/indexer-execution.png "索引子執行詳細資料")

如果在執行期間發生的文件特有的問題，它們會列出的錯誤和警告的欄位中。

   ![索引子發生錯誤的詳細資料](media/search-monitor-indexers/indexer-execution-error.png "索引子發生錯誤的詳細資料")

警告時常用的某些類型的索引子，並不一定表示有問題。 例如影像或 PDF 檔案不包含任何要處理的文字時使用認知服務的索引子可以回報警告。

如需有關如何調查索引子錯誤和警告的詳細資訊，請參閱 <<c0> [ 在 Azure 搜尋服務索引子常見問題進行疑難排解](search-indexer-troubleshooting.md)。

<a name="restapi"></a>

## <a name="monitor-indexers-using-the-rest-api"></a>監視索引子使用 REST API

您可以擷取索引子使用的狀態和執行歷程記錄[取得索引子狀態命令](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status):

    GET https://[service name].search.windows.net/indexers/[indexer name]/status?api-version=2019-05-06
    api-key: [Search service admin key]

回應包含整體索引子的狀態、最後 (或進行中) 的索引子叫用，以及最新的索引子叫用歷程記錄。

    {
        "status":"running",
        "lastResult": {
            "status":"success",
            "errorMessage":null,
            "startTime":"2018-11-26T03:37:18.853Z",
            "endTime":"2018-11-26T03:37:19.012Z",
            "errors":[],
            "itemsProcessed":11,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null
         },
        "executionHistory":[ {
            "status":"success",
             "errorMessage":null,
            "startTime":"2018-11-26T03:37:18.853Z",
            "endTime":"2018-11-26T03:37:19.012Z",
            "errors":[],
            "itemsProcessed":11,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null
        }]
    }

執行歷程記錄包含最多 50 最新的執行，這會依反向時間順序 （最新的第一個） 排序。

請注意有兩個不同的狀態值。 索引子本身是最上層狀態。 索引子狀態**執行**表示索引子已正確設定，而且可用來執行，但不是確定它的目前執行。

每次執行的索引子也有自己的狀態，指出該特定執行是否正在進行中 (**執行**)，或已經完成，但**成功**， **transientFailure**，或**persistentFailure**狀態。 

當重新整理其變更追蹤狀態重設索引子時，個別的執行歷程記錄項目加上**重設**狀態。

如需狀態碼和監視資料的索引子的詳細資訊，請參閱 < [GetIndexerStatus](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status)。

<a name="dotnetsdk"></a>

## <a name="monitor-indexers-using-the-net-sdk"></a>監視索引子使用.NET SDK

您可以定義索引子使用 Azure 搜尋服務.NET SDK 的排程。 若要這樣做，請包含**排程**時建立或更新索引子的屬性。

下列C#範例寫入的索引子狀態的相關資訊，並在主控台中執行其最新 （或進行中） 的結果。

```csharp
static void CheckIndexerStatus(Indexer indexer, SearchServiceClient searchService)
{
    try
    {
        IndexerExecutionInfo execInfo = searchService.Indexers.GetStatus(indexer.Name);

        Console.WriteLine("Indexer has run {0} times.", execInfo.ExecutionHistory.Count);
        Console.WriteLine("Indexer Status: " + execInfo.Status.ToString());

        IndexerExecutionResult result = execInfo.LastResult;

        Console.WriteLine("Latest run");
        Console.WriteLine("  Run Status: {0}", result.Status.ToString());
        Console.WriteLine("  Total Documents: {0}, Failed: {1}", result.ItemCount, result.FailedItemCount);

        TimeSpan elapsed = result.EndTime.Value - result.StartTime.Value;
        Console.WriteLine("  StartTime: {0:T}, EndTime: {1:T}, Elapsed: {2:t}", result.StartTime.Value, result.EndTime.Value, elapsed);

        string errorMsg = (result.ErrorMessage == null) ? "none" : result.ErrorMessage;
        Console.WriteLine("  ErrorMessage: {0}", errorMsg);
        Console.WriteLine("  Document Errors: {0}, Warnings: {1}\n", result.Errors.Count, result.Warnings.Count);
    }
    catch (Exception e)
    {
        // Handle exception
    }
}
```

在主控台輸出會看起來像這樣：

    Indexer has run 18 times.
    Indexer Status: Running
    Latest run
      Run Status: Success
      Total Documents: 7, Failed: 0
      StartTime: 10:02:46 PM, EndTime: 10:02:47 PM, Elapsed: 00:00:01.0990000
      ErrorMessage: none
      Document Errors: 0, Warnings: 0

請注意有兩個不同的狀態值。 最上層的狀態是 索引子本身的狀態。 索引子狀態**執行**表示索引子已正確設定，並可用於執行，但不是確定它正在執行。

每次執行的索引子也有自己的狀態，該特定執行是否進行中 (**執行**)，或已完成但有**成功**或是**TransientError**狀態。 

當重新整理其變更追蹤狀態重設索引子時，個別的記錄項目加上**重設**狀態。

如需狀態碼和監視資訊的索引子的詳細資訊，請參閱 < [GetIndexerStatus](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status) REST API 中。

列舉清單也可以擷取特定文件的錯誤或警告的詳細`IndexerExecutionResult.Errors`和`IndexerExecutionResult.Warnings`。

如需有關監視索引子使用的.NET SDK 類別的詳細資訊，請參閱[IndexerExecutionInfo](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexerexecutioninfo?view=azure-dotnet)並[IndexerExecutionResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexerexecutionresult?view=azure-dotnet)。
