---
title: 使用大型資料集
description: 了解如何在使用 Azure Resource Graph 期間取得並控制大型資料集。
author: DCtheGeek
ms.author: dacoulte
ms.date: 04/01/2019
ms.topic: conceptual
ms.service: resource-graph
manager: carmonm
ms.openlocfilehash: 729e9fe749212942c6dc18fc7d6301934e7dd184
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/08/2019
ms.locfileid: "59262460"
---
# <a name="working-with-large-azure-resource-data-sets"></a>使用大型 Azure 資源資料集

Azure Resource Graph 是設計來使用和取得 Azure 環境中資源的相關資訊。 即使在查詢數千筆記錄時，Resource Graph 還是能夠快速取得此資料。 Resource Graph 有數個選項可用來使用這些大型資料集。

## <a name="data-set-result-size"></a>資料集結果大小

根據預設，Resource Graph 會將所有查詢限制為只傳回 **100** 筆記錄。 此控制項可保護使用者和服務，以防止會產生大型資料集的非預期查詢。 此事件大多會在客戶利用符合其特定需求的方式來實驗查詢以尋找並篩選資源時發生。 此控制項不同於使用 [top](/azure/kusto/query/topoperator) 或 [limit](/azure/kusto/query/limitoperator) Azure 資料總管語言運算子來限制結果。

> [!NOTE]
> 使用時**第一**，則建議使用至少一個資料行排序結果`asc`或`desc`。 不進行排序，傳回其結果會是隨機而不是重複。

預設限制可以透過與 Resource Graph 互動的所有方法來覆寫。 下列範例示範如何將資料集大小限制變更為 _200_：

```azurecli-interactive
az graph query -q "project name | order by name asc" --first 200 --output table
```

```azurepowershell-interactive
Search-AzGraph -Query "project name | order by name asc" -First 200
```

在 [REST API](/rest/api/azureresourcegraph/resources/resources) 中，此控制項為 **$top** 且屬於 **QueryRequestOptions**。

「限制最嚴格」的控制項將會勝出。 例如，如果您的查詢會使用 **top** 或 **limit** 運算子並產生比 **First** 還多的記錄，則傳回的記錄數目上限會等於 **First**。 同樣地，如果 **top** 或 **limit** 小於 **First**，傳回的記錄集會是小於 **top** 或 **limit** 所設定的值。

**First** 目前具有最大允許值 _5000_。

## <a name="skipping-records"></a>略過記錄

使用大型資料集的下一個選項是 **Skip** 控制項。 此控制項可讓查詢先跳過或略過所定義的記錄數目，然後再傳回結果。 **Skip** 適用於以有意義的方式排序結果的查詢，其目的是取得位於結果集中間某處的記錄。 如果所需的結果位於所傳回資料集的結尾處，則使用不同的排序設定，並改為從資料集頂端擷取結果會更有效率。

> [!NOTE]
> 使用時**略過**，則建議使用至少一個資料行排序結果`asc`或`desc`。 不進行排序，傳回其結果會是隨機而不是重複。

下列範例示範如何略過查詢所產生的前 _10_ 筆記錄，改為從所傳回結果集的第 11 筆記錄開始：

```azurecli-interactive
az graph query -q "project name | order by name asc" --skip 10 --output table
```

```azurepowershell-interactive
Search-AzGraph -Query "project name | order by name asc" -Skip 10
```

在 [REST API](/rest/api/azureresourcegraph/resources/resources) 中，此控制項為 **$skip** 且屬於 **QueryRequestOptions**。

## <a name="paging-results"></a>分頁結果

必須將結果集數量較少的記錄進行處理時，或因為結果集可能會超過允許的值的最大_1000年_傳回記錄，使用分頁。 [REST API](/rest/api/azureresourcegraph/resources/resources) **QueryResponse** 會提供值來表示已將結果集分解：**resultTruncated** 和 **$skipToken**。
**resultTruncated** 是一個布林值，如果回應中有其他未傳回的記錄，則會通知取用者。 在 **count** 屬性小於 **totalRecords** 屬性時，也可識別出此條件。 **totalRecords** 會定義符合查詢的記錄筆數。

當 **resultTruncated** 為 **true** 時，即會在回應中設定 **$skipToken** 屬性。 此值會與相同的查詢和訂用帳戶值搭配使用，以取得下一組符合查詢的記錄。

> [!IMPORTANT]
> 查詢必須**投影****識別碼**欄位，才能使分頁運作。 如果遺漏查詢，不會加入 REST API 回應 **$skipToken**。

如需範例，請參閱 REST API 文件中的[下一頁查詢](/rest/api/azureresourcegraph/resources/resources#next_page_query)。

## <a name="next-steps"></a>後續步驟

- 請參閱[入門查詢](../samples/starter.md)中使用的語言
- 請參閱[進階查詢](../samples/advanced.md)中的進階使用方式
- 了解[探索資源](explore-resources.md)