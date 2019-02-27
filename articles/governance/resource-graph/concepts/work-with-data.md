---
title: 使用大型資料集
description: 了解如何在使用 Azure Resource Graph 期間取得並控制大型資料集。
services: resource-graph
author: DCtheGeek
ms.author: dacoulte
ms.date: 01/31/2019
ms.topic: conceptual
ms.service: resource-graph
manager: carmonm
ms.openlocfilehash: 8808f42cdd6fb547b70695278993faa0f52cdb61
ms.sourcegitcommit: fcb674cc4e43ac5e4583e0098d06af7b398bd9a9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/18/2019
ms.locfileid: "56338388"
---
# <a name="working-with-large-azure-resource-data-sets"></a>使用大型 Azure 資源資料集

Azure Resource Graph 是設計來使用和取得 Azure 環境中資源的相關資訊。 即使在查詢數千筆記錄時，Resource Graph 還是能夠快速取得此資料。 Resource Graph 有數個選項可用來使用這些大型資料集。

## <a name="data-set-result-size"></a>資料集結果大小

根據預設，Resource Graph 會將所有查詢限制為只傳回 **100** 筆記錄。 此控制項可保護使用者和服務，以防止會產生大型資料集的非預期查詢。 此事件大多會在客戶利用符合其特定需求的方式來實驗查詢以尋找並篩選資源時發生。 此控制項不同於使用 [top](/azure/kusto/query/topoperator) 或 [limit](/azure/kusto/query/limitoperator) Azure 資料總管語言運算子來限制結果。

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

下列範例示範如何略過查詢所產生的前 _10_ 筆記錄，改為從所傳回結果集的第 11 筆記錄開始：

```azurecli-interactive
az graph query -q "project name | order by name asc" --skip 10 --output table
```

```azurepowershell-interactive
Search-AzGraph -Query "project name | order by name asc" -Skip 10
```

在 [REST API](/rest/api/azureresourcegraph/resources/resources) 中，此控制項為 **$skip** 且屬於 **QueryRequestOptions**。

## <a name="paging-results"></a>分頁結果

如果需要將結果集分解為較小的記錄集以進行處理，或者因為結果集可能超過 _5000_ 筆傳回記錄的允許值上限，請使用分頁。 [REST API](/rest/api/azureresourcegraph/resources/resources) **QueryResponse** 會提供值來表示已將結果集分解：**resultTruncated** 和 **$skipToken**。
**resultTruncated** 是一個布林值，如果回應中有其他未傳回的記錄，則會通知取用者。 在 **count** 屬性小於 **totalRecords** 屬性時，也可識別出此條件。 **totalRecords** 會定義符合查詢的記錄筆數。

當 **resultTruncated** 為 **true** 時，即會在回應中設定 **$skipToken** 屬性。 此值會與相同的查詢和訂用帳戶值搭配使用，以取得下一組符合查詢的記錄。

> [!IMPORTANT]
> 查詢必須**投影****識別碼**欄位，才能使分頁運作。 如果查詢中遺漏了它，REST API 回應將不會包含 **$skipToken**。

如需範例，請參閱 REST API 文件中的[下一頁查詢](/rest/api/azureresourcegraph/resources/resources#next_page_query)。

## <a name="next-steps"></a>後續步驟

- 請參閱[入門查詢](../samples/starter.md)中使用的語言
- 請參閱[進階查詢](../samples/advanced.md)中的進階使用方式
- 了解[探索資源](explore-resources.md)