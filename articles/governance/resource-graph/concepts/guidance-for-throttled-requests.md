---
title: 已節流處理要求的指引
description: 了解如何建立更好的查詢，以避免對 Azure 資源 Graph 的要求，從正在進行節流處理。
author: DCtheGeek
ms.author: dacoulte
ms.date: 06/19/2019
ms.topic: conceptual
ms.service: resource-graph
manager: carmonm
ms.openlocfilehash: c644d230846d9c644c3845348431eef36c8279c8
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/20/2019
ms.locfileid: "67276895"
---
# <a name="guidance-for-throttled-requests-in-azure-resource-graph"></a>Azure 資源的圖形中的已節流處理要求的指引

在建立時以程式設計方式和經常使用的 Azure 資源的圖形資料，應考慮如何節流影響的查詢的結果。 變更資料的方式，會要求可協助您及您的組織避免受到節流，並維護 Azure 資源的相關的即時資料流。

本文章涵蓋四個區域和與 Azure 資源的圖表中的查詢建立相關的模式：

- 了解節流的標頭
- 批次查詢
- 錯開查詢
- 重新編頁的影響

## <a name="understand-throttling-headers"></a>了解節流的標頭

Azure 資源的圖形配置配額數量，每個使用者根據時間範圍。 例如，使用者可以傳送最多 15 個查詢每隔 5 秒內而不進行節流處理。 配額值取決於許多因素，並可能有所變更。

在每個查詢回應中，Azure 資源的圖表會新增兩個節流的標頭：

- `x-ms-user-quota-remaining` (int)：使用者的剩餘資源配額。 此值會對應至查詢計數。
- `x-ms-user-quota-resets-after` (hh:mm:ss)：持續時間之前就會重設使用者的配額耗用量。

為了說明標頭的運作方式，讓我們看看有標頭和值的查詢回應`x-ms-user-quota-remaining: 10`和`x-ms-user-quota-resets-after: 00:00:03`。

- 在接下來的 3 秒，最多 10 項查詢而不進行節流處理提交。
- 在 3 秒後的值`x-ms-user-quota-remaining`並`x-ms-user-quota-resets-after`將會重設`15`和`00:00:05`分別。

若要使用的標頭的範例，請參閱_輪詢_對於查詢的要求，請參閱中的範例[平行查詢](#query-in-parallel)。

## <a name="batching-queries"></a>批次查詢

批次的訂用帳戶、 資源群組或個別資源的查詢會比平行查詢更有效率。 配額的成本較大通常是查詢的許多小型且目標查詢的配額成本大於或等於。 建議的批次大小是小於_300_。

- 最佳化不佳的方法的範例

  ```csharp
  // NOT RECOMMENDED
  var header = /* your request header */
  var subscriptionIds = /* A big list of subscriptionIds */

  foreach (var subscriptionId in subscriptionIds)
  {
      var userQueryRequest = new QueryRequest(
          subscriptions: new[] { subscriptionId },
          query: "project name, type");

      var azureOperationResponse = await this.resourceGraphClient
          .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
          .ConfigureAwait(false);

  // ...
  }
  ```

- 範例 #1 的最佳化批次方法

  ```csharp
  // RECOMMENDED
  var header = /* your request header */
  var subscriptionIds = /* A big list of subscriptionIds */

  const int batchSize = 100;
  for (var i = 0; i <= subscriptionIds.Count / batchSize; ++i)
  {
      var currSubscriptionBatch = subscriptionIds.Skip(i * batchSize).Take(batchSize).ToList();
      var userQueryRequest = new QueryRequest(
          subscriptions: currSubscriptionBatch,
          query: "project name, type");

      var azureOperationResponse = await this.resourceGraphClient
          .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
          .ConfigureAwait(false);

    // ...
  }
  ```

- 範例 #2 的最佳化批次方法

  ```csharp
  // RECOMMENDED
  var header = /* your request header */
  var resourceIds = /* A big list of resourceIds */

  const int batchSize = 100;
  for (var i = 0; i <= resourceIds.Count / batchSize; ++i)
  {
      var resourceIdBatch = string.Join(",",
          resourceIds.Skip(i * batchSize).Take(batchSize).Select(id => string.Format("'{0}'", id)));
      var userQueryRequest = new QueryRequest(
          subscriptions: subscriptionList,
          query: $"where id in~ ({resourceIds}) | project name, type");

      var azureOperationResponse = await this.resourceGraphClient
          .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
          .ConfigureAwait(false);

    // ...
  }
  ```

## <a name="staggering-queries"></a>錯開查詢

因為節流會強制執行，我們建議為交錯的查詢。 亦即，而不是在相同的時間傳送 60 個查詢，請到四個的 5 秒視窗錯開查詢：

- 非交錯執行的查詢排程

  | 查詢計數         | 60  | 0    | 0     | 0     |
  |---------------------|-----|------|-------|-------|
  | 時間間隔 （秒） | 0-5 | 5-10 | 10-15 | 15-20 |

- 交錯執行查詢的排程

  | 查詢計數         | 15  | 15   | 15    | 15    |
  |---------------------|-----|------|-------|-------|
  | 時間間隔 （秒） | 0-5 | 5-10 | 10-15 | 15-20 |

以下是範例查詢 Azure 資源的圖表時，又能採用節流的標頭：

```csharp
while (/* Need to query more? */)
{
    var userQueryRequest = /* ... */
    // Send post request to Azure Resource Graph
    var azureOperationResponse = await this.resourceGraphClient
        .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
        .ConfigureAwait(false);

    var responseHeaders = azureOperationResponse.response.Headers;
    int remainingQuota = /* read and parse x-ms-user-quota-remaining from responseHeaders */
    TimeSpan resetAfter = /* read and parse x-ms-user-quota-resets-after from responseHeaders */
    if (remainingQuota == 0)
    {
        // Need to wait until new quota is allocated
        await Task.Delay(resetAfter).ConfigureAwait(false);
    }
}
```

### <a name="query-in-parallel"></a>平行查詢

即使透過平行處理，建議使用批次處理，有些時候，無法輕易地批次處理查詢。 在這些情況下，您可能想要以平行方式傳送多個查詢來查詢 Azure 資源的圖表。 以下是如何的範例_輪詢_根據節流這類案例中的標頭：

```csharp
IEnumerable<IEnumerable<string>> queryBatches = /* Batches of queries  */
// Run batches in parallel.
await Task.WhenAll(queryBatches.Select(ExecuteQueries)).ConfigureAwait(false);

async Task ExecuteQueries(IEnumerable<string> queries)
{
    foreach (var query in queries)
    {
        var userQueryRequest = new QueryRequest(
            subscriptions: subscriptionList,
            query: query);
        // Send post request to Azure Resource Graph.
        var azureOperationResponse = await this.resourceGraphClient
            .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
            .ConfigureAwait(false);
        
        var responseHeaders = azureOperationResponse.response.Headers;
        int remainingQuota = /* read and parse x-ms-user-quota-remaining from responseHeaders */
        TimeSpan resetAfter = /* read and parse x-ms-user-quota-resets-after from responseHeaders */
        if (remainingQuota == 0)
        {
            // Delay by a random period to avoid bursting when the quota is reset.
            var delay = (new Random()).Next(1, 5) * resetAfter;
            await Task.Delay(delay).ConfigureAwait(false);
        }
    }
}
```

## <a name="pagination"></a>分頁

Azure 資源的圖表會在單一查詢回應中傳回最多 1000 個項目，所以您可能需要[編頁](./work-with-data.md#paging-results)您的查詢，以取得您要尋找完整的資料集。 不過，某些 Azure 資源圖形用戶端處理分頁方式不同於其他項目。

- C# SDK

  使用 ResourceGraph SDK 時，您需要藉由傳遞從下一個分頁查詢的前一個查詢回應中傳回的跳過權杖處理分頁。 這項設計會表示您需要收集所有已編頁呼叫的結果，並將它們一起結合結尾。 在此情況下，您傳送每個分頁的查詢會接受一個查詢配額：

  ```csharp
  var results = new List<object>();
  var queryRequest = new QueryRequest(
      subscriptions: new[] { mySubscriptionId },
      query: "project id, name, type | top 5000");
  var azureOperationResponse = await this.resourceGraphClient
      .ResourcesWithHttpMessagesAsync(queryRequest, header)
      .ConfigureAwait(false);
  while (!string.Empty(azureOperationResponse.Body.SkipToken))
  {
      queryRequest.SkipToken = azureOperationResponse.Body.SkipToken;
      // Each post call to ResourceGraph consumes one query quota
      var azureOperationResponse = await this.resourceGraphClient
          .ResourcesWithHttpMessagesAsync(queryRequest, header)
          .ConfigureAwait(false);
      results.Add(azureOperationResponse.Body.Data.Rows);

      // Inspect throttling headers in query response and delay the next call if needed.
  }
  ```

- Azure CLI / Azure PowerShell

  使用 Azure CLI 或 Azure PowerShell 時，查詢 Azure 資源的圖表會自動分頁，以擷取最多 5000 個項目。 查詢結果會從所有分頁的呼叫傳回合併的項目清單。 在此情況下，根據查詢結果中的項目數目，在單一的分頁的查詢可能會耗用多個查詢的配額。 例如，在下列範例中，單一查詢的執行可能會耗用最多五個查詢配額：

  ```azurecli-interactive
  az graph query -q 'project id, name, type' -top 5000
  ```

  ```azurepowershell-interactive
  Search-AzGraph -Query 'project id, name, type' -Top 5000
  ```

## <a name="still-get-throttled"></a>仍然進行節流？

如果您正在執行上述建議之後進行節流，請連絡小組[ resourcegraphsupport@microsoft.com ](mailto:resourcegraphsupport@microsoft.com)。

提供這些詳細資料：

- 您的特定使用案例和商務驅動程式需要更高的節流限制。
- 您可以存取的資源數量？ 多少個是查詢所傳回的單一？
- 您感興趣的資源類型為何？
- 什麼是查詢模式？ X 每 Y 秒等查詢。

## <a name="next-steps"></a>後續步驟

- 請參閱中的使用中的語言[入門查詢](../samples/starter.md)。
- 在中使用進階，請參閱[進階查詢](../samples/advanced.md)。
- 了解如何[探索資源](explore-resources.md)。