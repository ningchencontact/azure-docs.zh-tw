---
title: 常見問題疑難排解
description: 瞭解如何針對使用 Azure Resource Graph 查詢 Azure 資源的問題進行疑難排解。
author: DCtheGeek
ms.author: dacoulte
ms.date: 07/24/2019
ms.topic: troubleshooting
ms.service: resource-graph
manager: carmonm
ms.openlocfilehash: 511d170f90e8ed34b00a3960d084223ec73d99dd
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/24/2019
ms.locfileid: "68480549"
---
# <a name="troubleshoot-errors-using-azure-resource-graph"></a>使用 Azure Resource Graph 疑難排解錯誤

使用 Azure Resource Graph 查詢 Azure 資源時, 可能會遇到錯誤。 此文章說明可能發生的各種錯誤與解決方式。

## <a name="finding-error-details"></a>尋找錯誤詳細資料

大部分的錯誤是使用 Azure Resource Graph 執行查詢時的問題所造成。 當查詢失敗時, SDK 會提供有關失敗查詢的詳細資料。 這項資訊指出問題, 讓它可以修正, 而稍後的查詢會成功。

## <a name="general-errors"></a>一般錯誤

### <a name="toomanysubscription"></a>案例：訂閱太多

#### <a name="issue"></a>問題

存取超過1000個訂用帳戶 (包括[Azure 燈塔](../../../lighthouse/overview.md)的跨租使用者訂用帳戶) 的客戶, 在 Azure Resource Graph 的單一呼叫中, 無法跨所有訂用帳戶提取資料。

#### <a name="cause"></a>原因

Azure CLI 和 PowerShell 只轉送前1000個訂用帳戶, 以 Azure Resource Graph。 Azure Resource Graph 的 REST API 會接受執行查詢的最大訂閱數。

#### <a name="resolution"></a>解決方法

具有訂用帳戶子集的查詢批次要求會保留在1000訂閱限制之下。 解決方案會使用 PowerShell 中的**訂**用帳戶參數。

```azurepowershell-interactive
# Replace this query with your own
$query = 'project type'

# Fetch the full array of subscription IDs
$subscriptions = Get-AzSubscription
$subscriptionIds = $subscriptions.Id

# Create a counter, set the batch size, and prepare a variable for the results
$counter = [PSCustomObject] @{ Value = 0 }
$batchSize = 1000
$response = @()

# Group the subscriptions into batches
$subscriptionsBatch = $subscriptionIds | Group -Property { [math]::Floor($counter.Value++ / $batchSize) }

# Run the query for each batch
foreach ($batch in $subscriptionsBatch){ $response += Search-AzGraph -Query $query -Subscription $batch.Group }

# View the completed results of the query on all subscriptions
$response
```

## <a name="next-steps"></a>後續步驟

如果您沒有看到您的問題，或無法解決您的問題，請瀏覽下列其中一個管道以取得更多支援：

- 透過[Azure 論壇](https://azure.microsoft.com/support/forums/)取得 azure 專家的解答。
- 與 [@AzureSupport](https://twitter.com/azuresupport) 連繫－專為改善客戶體驗而設的官方 Microsoft Azure 帳戶，協助 Azure 社群連接至適當的資源，像是解答、支援及專家等。
- 如果需要更多協助，您可以提出 Azure 支援事件。 請移至 [Azure 支援網站](https://azure.microsoft.com/support/options/)，然後選取 [取得支援]。